# 从 Spring Boot 应用程序向亚马逊 SQS 发送消息

> 原文：<https://levelup.gitconnected.com/sending-messages-to-amazon-sqs-from-a-spring-boot-application-eaa6f6b07bb>

> 原载于[我的个人博客](https://blog.contactsunny.com/tech/sending-messages-to-amazon-sqs-from-a-spring-boot-application)2020 年 1 月 14 日。

![](img/1763ab8fd98af905343a7d7055b5a591.png)

今天，我们来看另一个概念验证(POC)应用。我们将看看如何将 Amazon 的简单队列服务(SQS)集成到我们的 Spring Boot 应用程序中，以便我们可以向队列发送消息。

我将在这篇文章中使用一些受阿帕奇卡夫卡影响的术语，因为我有丰富的卡夫卡经验。然而，我不打算在这里比较阿帕奇卡夫卡和亚马逊 SQS。为了清楚起见，任何向 SQS 队列发送消息的服务，我将把这样的服务称为生产者。任何从 SQS 队列接收消息的服务，我都称之为消费者。既然我们已经弄清楚了，让我们开始吧。

# 创建 SQS 队列

创建 SQS 队列非常简单。前往您的 AWS 控制台，在控制台中搜索 SQS，如果还没有创建任何队列，您应该会看到类似下面截图的页面。

点击页面中间的*立即开始*按钮。在那里，您将看到两个选项，用于选择您想要创建的队列类型——标准和 FIFO。基本上，FIFO 是一种特殊类型的队列，消息的顺序在队列中保持不变。你可以在页面上看到关于这一点的解释。另一方面，标准队列虽然不维护消息的顺序，但允许队列有更大的吞吐量。FIFO 只在消息的顺序特别重要的情况下使用。此外，FIFO 并非在所有地区都可用。对于这个例子，我们将坚持使用标准队列。

创建队列后，您将在屏幕的下半部分看到队列的所有重要细节。这里的一个重要信息是 SQS 队列 URL。它应该看起来像这样:

```
[https://sqs.us-east-2.amazonaws.com/1234/abcd](https://sqs.us-east-2.amazonaws.com/1234/abcd)
```

记下这个 URL，因为我们将在项目中使用它来连接 SQS 服务。

# 代码

我们现在从代码开始。我们需要在 Spring Boot 应用程序中添加的第一件事是 SQS 依赖。幸运的是，Maven 中央存储库中提供了依赖关系。因此，我们只需在我们的 *pom.xml* 文件的*依赖关系*块中添加以下内容:

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

填写属性文件中的空白。我们必须在 Java 代码中获取这些值。因为我们使用的是 Spring Boot，所以使用 *@Value* 注释可以很容易地将这些值写入代码:

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

我们在这里创建的 *amazonSQS* 对象就是我们从现在开始要使用的对象。现在，要发送消息，您所要做的就是调用 SQS 对象上的 *sendMessage()* 方法，就像这样:

```
SendMessageResult result = amazonSQS.sendMessage(this.sqsUrl, message);
```

正如您所猜测的，第一个参数是我们发送消息的 SQS 队列的 URL，第二个参数是实际的消息。我写的 SQS 相关代码是一个名为 SQSUtil 的类。这个类被注释为一个*@组件*，这样我就可以 *@Autowire* 它在代码库中的任何地方。该类如下所示:

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

    public void sendSQSMessage(String message) {

        logger.info("Sending SQS message: " + message);

        SendMessageResult result = this.amazonSQS.sendMessage(this.sqsUrl, message);

        logger.info("SQS Message ID: " + result.getMessageId());
    }
}
```

现在让我们看看我是如何使用它的。在主类中，我已经自动连接了这个实用程序类，如下所示:

```
@Autowired 
private SQSUtil sqsUtil;
```

消息是一个 JSON 字符串。但是我如何创建这个 JSON 字符串呢？我为此创建了一个简单的 POJO 类。我创建了这个 POJO 类的几个实例，并使用 Gson 库将 POJO 转换成 JSON 字符串。我们先来看看 POJO 类:

```
public class SamsungPhone {

    private String name;
    private String description;
    private long timestamp;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public long getTimestamp() {
        return timestamp;
    }

    public void setTimestamp(long timestamp) {
        this.timestamp = timestamp;
    }
}
```

我同意，很简单，一点道理都没有。总之，我用这个类创建了以下对象:

```
List<SamsungPhone> samsungPhones = new ArrayList<>();

SamsungPhone galaxyNote10Plus = new SamsungPhone();
galaxyNote10Plus.setName("Samsung Galaxy Note 10 Plus");;
galaxyNote10Plus.setDescription("2019 flagship phone with a 6.8 inch Super AMOLED display, S Pen, and much more");
galaxyNote10Plus.setTimestamp(System.currentTimeMillis());
samsungPhones.add(galaxyNote10Plus);

SamsungPhone galaxyNote10 = new SamsungPhone();
galaxyNote10.setName("Samsung Galaxy Note 10");;
galaxyNote10.setDescription("2019 flagship phone with a 6.5 inch Super AMOLED display, S Pen, and much more");
galaxyNote10.setTimestamp(System.currentTimeMillis());
samsungPhones.add(galaxyNote10);

SamsungPhone galaxyS10Plus = new SamsungPhone();
galaxyS10Plus.setName("Samsung Galaxy S 10 Plus");;
galaxyS10Plus.setDescription("Early 2019 flagship phone with a 6.5 inch Super AMOLED display, " +
        "dual punch hole selfie cameras, and much more");
galaxyS10Plus.setTimestamp(System.currentTimeMillis());
samsungPhones.add(galaxyS10Plus);

SamsungPhone galaxyS10 = new SamsungPhone();
galaxyS10.setName("Samsung Galaxy S 10");;
galaxyS10.setDescription("Early 2019 flagship phone with a 6.3 inch Super AMOLED display, " +
        "dual punch hole selfie cameras, and much more");
galaxyS10.setTimestamp(System.currentTimeMillis());
samsungPhones.add(galaxyS10);
```

如您所见，我正在将这些对象添加到一个列表中。我遍历该列表，将每次迭代中的对象转换为 JSON 字符串，并调用 SQSUtil 类中的 *sendSQSMessage()* 方法将它发送到 SQS 队列。现在让我们来看看这部分代码:

```
for (SamsungPhone samsungPhone : samsungPhones) {
    this.sqsUtil.sendSQSMessage(new Gson().toJson(samsungPhone));
}
```

这一点也不难，是吗？

在下一篇[文章](https://blog.contactsunny.com/tech/receiving-messages-from-amazon-sqs-in-a-spring-boot-application)中，我们将看到如何接收或消费我们刚刚发送的消息。如果你很好奇，想看看 Apache Kafka 的生产者和消费者的代码有多大的不同，可以看看这篇文章，我在那里也谈到了同样的问题。如果您想深入了解这个 SQS 示例的代码，请前往 [my Github repo](https://github.com/contactsunny/AmazonSQSProducerPOC) ，在那里您将获得这个示例的工作项目。