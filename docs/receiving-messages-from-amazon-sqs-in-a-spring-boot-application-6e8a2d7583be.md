# 在 Spring Boot 应用程序中接收来自亚马逊 SQS 的消息

> 原文：<https://levelup.gitconnected.com/receiving-messages-from-amazon-sqs-in-a-spring-boot-application-6e8a2d7583be>

> 原载于[我的个人博客](https://blog.contactsunny.com/tech/receiving-messages-from-amazon-sqs-in-a-spring-boot-application)2020 年 1 月 16 日。

![](img/56c05f888238b094e019dcf32e99a6c2.png)

在本帖中，我们将了解如何在 Spring Boot 应用程序中接收来自亚马逊 SQS 队列的消息。这是上一篇文章的延续，在上一篇文章中，我们讨论了如何[向 SQS 队列](https://blog.contactsunny.com/tech/sending-messages-to-amazon-sqs-from-a-spring-boot-application)发送消息。很明显，接下来的问题是我们如何接收这些信息。所以在这篇文章中，我们会这样做。

如果您还没有创建 SQS 队列，请查看之前关于如何创建的帖子。这里，我假设您已经有了管道设置。所以我要跳过帖子的这一部分。我们将直接进入代码。

# 代码

我们需要在 Spring Boot 应用程序中添加的第一件事是 SQS 依赖。幸运的是，Maven 中央存储库中提供了依赖关系。因此，我们只需在我们的 *pom.xml* 文件的*依赖关系*块中添加以下内容:

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-aws-messaging</artifactId>
    <version>2.2.1.RELEASE</version>
</dependency>
```

接下来，我们必须处理好属性。对于这个例子，我们将使用 AWS 的静态凭证提供者。所以我们需要访问密钥和秘密密钥。我假设你已经有这些东西了。所以我将跳过如何设置它的说明。在我们的 *application.properties* 文件中，我们将拥有以下属性:

```
sqs.url=https://sqs.us-east-2.amazonaws.com/1234/abcd

aws.accessKey=
aws.secretKey=
aws.region=
```

填写属性文件中的空白。我们必须在 Java 代码中获取这些值。因为我们使用的是 Spring Boot，所以使用 *@Value* 注释可以很容易地将这些值写入我们的代码:

```
@Value("${sqs.url}")
private String sqsUrl;

@Value("${aws.accessKey}")
private String awsAccessKey;

@Value("${aws.secretKey}")
private String awsSecretKey;

@Value("${aws.region}")
private String awsRegion;
```

在这之后，我们必须为 AWS cred greates 和 SQS 分别创建一个对象。我们可以使用下面的代码片段来做到这一点:

```
AWSCredentialsProvider awsCredentialsProvider = new AWSStaticCredentialsProvider(
        new BasicAWSCredentials(awsAccessKey, awsSecretKey)
);

AmazonSQS amazonSQS = AmazonSQSClientBuilder.standard().withCredentials(awsCredentialsProvider).build();
```

现在我们必须开始阅读信息。我们在一个 *while(true) {}* 循环中这样做。这是因为我们不想停止收听新信息。我们必须持续不断地这样做。但是在我们进入循环之前，我们必须创建一个请求对象来周期性地从 SQS 请求消息。在这个请求中，我们可以指定每个响应中需要多少消息，以及连续请求之间的时间间隔。这个请求对象是 *ReceiveMessageRequest* 类的一个实例，我们是这样创建它的:

```
final ReceiveMessageRequest receiveMessageRequest = new ReceiveMessageRequest(sqsUrl)
        .withMaxNumberOfMessages(1)
        .withWaitTimeSeconds(3);
```

现在我们进入循环。在循环中，我们使用之前创建的 SQS 客户端实例来请求消息。一旦我们这样做了，我们就会得到一个消息列表作为回报。然后，我们可以继续处理每条消息。该循环如下所示:

```
while (true) {

    final List<Message> messages = amazonSQS.receiveMessage(receiveMessageRequest).getMessages();

    for (Message messageObject : messages) {
        String message = messageObject.getBody();

        logger.info("Received message: " + message);
    }
}
```

我在这里保持处理简单，只是打印出消息体。

# 一个队列有多个消息使用者

如果您来自 Apache Kafka 领域，您会知道我们可以让多个消费者使用相同的组 ID 来并行处理一个主题中的消息。这里显而易见的问题是，我们能对 SQS 做同样的事情吗？

简单的回答是，是的，我们可以。但是，这里没有组 ID 的概念，或者类似的概念。如果有多个服务在监听同一个 SQS 队列，那么进入该队列的消息将分布在所有这些服务中。没有两个服务会得到相同的消息。如果你想模仿卡夫卡的消费者，为同一个主题使用多个群组 id，你需要结合使用 SNS(亚马逊的简单通知服务)和 SQS。如果你们感兴趣的话，也许我会再写一篇关于如何做的文章。

# 在 SQS 确认一个信息

在 Apache Kafka 中，一旦我们使用了来自某个主题的消息，并处理了该消息，我们就通过提交偏移量来确认该消息。一旦你这样做了，这条消息将不会在你下次请求消息时发送给你。如果您提交了偏移量，这意味着您已经处理完了该消息，并且您在告诉系统不要再向您发送该消息。在这里有可能做同样的事情吗？

这个问题的答案也是肯定的。在 SQS 世界里有一种叫做*能见度超时*的东西。Visibility timeout 是消息对任何使用者可见的时间。因此，如果在可见性超时期限内多次查询消息，很可能会得到相同的消息。因此，SQS 的“提交偏移量”等同于从队列中删除消息。在我们前面看到的代码片段中的 *for* 循环中，我们可以在阅读完消息后删除它。我为此编写了一个简单的方法:

```
private void deleteMessage(Message messageObject) {

    final String messageReceiptHandle = messageObject.getReceiptHandle();
    amazonSQS.deleteMessage(new DeleteMessageRequest(sqsUrl, messageReceiptHandle));

}
```

我们将更改前面代码片段中的循环，以便在处理消息后调用此方法:

```
while (true) {

    final List<Message> messages = amazonSQS.receiveMessage(receiveMessageRequest).getMessages();

    for (Message messageObject : messages) {
        String message = messageObject.getBody();

        logger.info("Received message: " + message);

        deleteMessage(messageObject);
    }
}
```

我写的 SQS 相关代码是一个名为 SQSUtil 的类。这个类被注释为一个*@组件*，这样我就可以在代码库中的任何地方 *@Autowire* 它。该类如下所示:

```
@Component
public class SQSUtil {

    @Value("${sqs.url}")
    private String sqsUrl;

    @Value("${aws.accessKey}")
    private String awsAccessKey;

    @Value("${aws.secretKey}")
    private String awsSecretKey;

    @Value("${aws.region}")
    private String awsRegion;

    private AmazonSQS amazonSQS;

    private static final Logger logger = LoggerFactory.getLogger(SQSUtil.class);

    @PostConstruct
    private void postConstructor() {

        logger.info("SQS URL: " + sqsUrl);

        AWSCredentialsProvider awsCredentialsProvider = new AWSStaticCredentialsProvider(
                new BasicAWSCredentials(awsAccessKey, awsSecretKey)
        );

        this.amazonSQS = AmazonSQSClientBuilder.standard().withCredentials(awsCredentialsProvider).build();
    }

    public void startListeningToMessages() {

        final ReceiveMessageRequest receiveMessageRequest = new ReceiveMessageRequest(sqsUrl)
                .withMaxNumberOfMessages(1)
                .withWaitTimeSeconds(3);

        while (true) {

            final List<Message> messages = amazonSQS.receiveMessage(receiveMessageRequest).getMessages();

            for (Message messageObject : messages) {
                String message = messageObject.getBody();

                logger.info("Received message: " + message);

                deleteMessage(messageObject);
            }
        }
    }

    private void deleteMessage(Message messageObject) {

        final String messageReceiptHandle = messageObject.getReceiptHandle();
        amazonSQS.deleteMessage(new DeleteMessageRequest(sqsUrl, messageReceiptHandle));

    }
}
```

如果你很好奇，想看看 Apache Kafka 的生产者和消费者的代码有多大的不同，看看[这篇文章](https://blog.contactsunny.com/tech/simple-apache-kafka-producer-and-consumer-using-spring-boot)，在那里我也谈到了同样的问题。你可以在[我的 Githup repo](https://github.com/contactsunny/AmazonSQSConsumerPOC) 上获得这个帖子的完整工作项目。