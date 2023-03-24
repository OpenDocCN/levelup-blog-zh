# 从 Spring Boot 应用程序向亚马逊社交网络发布消息

> 原文：<https://levelup.gitconnected.com/publishing-messages-to-amazon-sns-from-a-spring-boot-application-99d0cb57ce16>

> 最初发表于 2020 年 1 月 20 日我的个人博客。

![](img/3478585e951c931acda80976de1b434f.png)

在本帖中，我们将看到如何向 Amazon SNS 发布消息，它代表简单通知服务。如果你已经读过我关于[如何向 SQS 队列](https://blog.contactsunny.com/tech/sending-messages-to-amazon-sqs-from-a-spring-boot-application)发送消息的帖子，你会看到这个帖子与那个帖子非常相似。因为，多亏了 Amazon，客户端有如此相似的签名，你只需改变类，它就能像预期的那样工作。但是尽管如此，我还是会尽量在这里做详尽的解释。

首先，我们需要在 AWS 控制台上访问 Amazon SNS 主页。一旦你到了那里，用你自己选择的名字创建一个 SNS 主题。出于显而易见的原因，我选择了“thetechcheck”这个名字。我将继续使用这个名字。一旦你有了，拿一个主题 ARN 的拷贝，这是我们在发布消息到这个主题时要用的。设置好这些先决条件后，继续下一部分。

# 代码

# 属性文件

在我们进入编码部分之前，我们将首先进行配置设置。对于这个 POC，我们需要主题 ARN、AWS 凭证和主题区域。我们将把这些东西保存在 *application.properties* 文件中，因为我们正在构建一个 Spring Boot 项目。我的属性文件如下所示:

```
sns.topic.arn=arn:aws:sns:us-east-1:123456789:thetechcheck

aws.accessKey=
aws.secretKey=
aws.region=
```

显然，您必须填写凭证和地区的空白。我在上面的代码块中提到的 ARN 是假的，它只是用来表示 ARN 的样子。

接下来，我们必须将这些值导入到我们的类中，因为我们使用了 Spring Boot，我们可以使用 *@Value* 注释轻松导入这些值，如下所示:

```
@Value("${sns.topic.arn}")
private String snsTopicARN;

@Value("${aws.accessKey}")
private String awsAccessKey;

@Value("${aws.secretKey}")
private String awsSecretKey;

@Value("${aws.region}")
private String awsRegion;
```

# Amazon SNS 操作的 SNSUtil 实用程序类

我们将编写一个名为 *SNSUtil* 的实用程序类，它将是一个 *@Component* ，它将处理所有与 SNS 相关的操作。这个类将完成向 Amazon SNS 主题发布消息的实际工作。所以让我们以一个*@ post construct*钩子开始这个类，在这里我们将初始化一些东西:

```
@PostConstruct
private void postConstructor() {

    logger.info("SQS URL: " + snsTopicARN);

    AWSCredentialsProvider awsCredentialsProvider = new AWSStaticCredentialsProvider(
            new BasicAWSCredentials(awsAccessKey, awsSecretKey)
    );

    this.amazonSNS = AmazonSNSClientBuilder.standard()
            .withCredentials(awsCredentialsProvider)
            .withRegion(awsRegion)
            .build();
}
```

如您所见，我们正在创建一个 *AWSCredentialsProvider* 类的实例，在这里我们传递我们的 AWS 凭证。之后，我们将使用提供的构建器创建一个 Amazon SNS 客户端。确保您提供的区域与您创建主题的区域相同，否则它将不起作用，您将得到一个错误的请求异常。接下来我们需要看看如何发布消息。为此，我编写了一个简单的包装方法。

```
public void publishSNSMessage(String message) {

    logger.info("Publishing SNS message: " + message);

    PublishResult result = this.amazonSNS.publish(this.snsTopicARN, message);

    logger.info("SNS Message ID: " + result.getMessageId());
}
```

从上面的代码片段可以看出，发布消息的实际代码是一行代码。但是我已经在之前和之后添加了一些日志，以确保我们有一些日志可以玩。无论如何，我们现在将进入下一部分。

# POJO 类

因为我们需要向主题发布一些消息，所以我认为发布一些 JSON 消息而不仅仅是字符串是个好主意。生成 JSON 字符串最简单的方法是用 *Gson* 类序列化一个对象。为此，我们首先需要有一个可以序列化的类。与前面的例子类似，我创建了一个名为 *SamsungPhone* 的类。整个类如下:

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

这很简单。

# 测试 Amazon SNS 消息发布代码

现在我们将继续我们的主课。在这里，我们将首先 *@Autowire* 我们的实用程序类，就像这样:

```
@Autowired
private SNSUtil snsUtil;
```

接下来，我们将创建之前创建的 POJO 类的几个实例:

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

你可能知道，这是来自 [SQS 例子](https://blog.contactsunny.com/tech/sending-messages-to-amazon-sqs-from-a-spring-boot-application)的同一组数据，我只是复制了同样的东西。不管怎样，我们现在有了这些物体的列表。我们将遍历列表，使用 Gson 库将对象转换为 JSON 字符串，并将它们发布到 SNS:

```
for (SamsungPhone samsungPhone : samsungPhones) {
    this.snsUtil.publishSNSMessage(new Gson().toJson(samsungPhone));
}
```

仅此而已。

# 接收这些消息

下一个明显的问题是，一旦我们发布了这些信息，我们如何接收它们？订阅 SNS 主题的方法有很多种，最简单的方法是创建一个队列，然后订阅 SNS 主题。这样，每当有消息发布到相关的 SNS 主题时，该消息将被自动放入 SQS 队列。您可以编写一个简单的 SQS 队列使用者来接收和处理这些消息。你可以在这里阅读我的文章。我们现在结束了。我也写过我们如何使用这种方法来模仿阿帕奇卡夫卡。你可以在这里读到。

如果你想直接进入这个例子中已经完成的工作项目，而不是为你自己创建一个，你可以从[我的 Github repo](https://github.com/contactsunny/AmazonSNSPublisherPOC) 中派生出我的项目。