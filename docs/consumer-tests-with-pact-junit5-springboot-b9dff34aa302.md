# Pact、Junit5、SpringBoot 的消费者测试

> 原文：<https://levelup.gitconnected.com/consumer-tests-with-pact-junit5-springboot-b9dff34aa302>

![](img/d483e33d13c4926c1c97a582bdab8df3.png)

由 [Aniket Bhattacharya](https://unsplash.com/@aniket940518?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

> 你会放火烧你的房子来测试你的烟雾报警器吗？
> [*https://docs . pact . io*](https://docs.pact.io/)

消费者创建消费者测试，该测试确认消费者和它所依赖的提供者之间的契约。

Pact 是消费者驱动的，也就是说，它是由消费者运行的测试驱动的，正如我们将在下面看到的。

pact 框架基于消费者定义的交互来创建 Pact 文件。然后用它来验证服务交互的提供者。在标记为成功之前，提供者需要通过基于来自消费者的文件的测试。

这种验证是由 Pact 框架提供的，它允许对消费者和提供者进行独立测试，而不必将两者集成在一起。当您有过多的微服务时，这是一个巨大的优势。

以下是 Java、Spring 和 Junit5 中使用的。

# **消费端**

[https://github . com/DiUS/pact-JVM/tree/master/pact-JVM-consumer-JUnit 5](https://github.com/DiUS/pact-jvm/tree/master/pact-jvm-consumer-junit5)

## POM 摘录

```
 <dependency>
            <groupId>au.com.dius</groupId>
            <artifactId>pact-jvm-consumer-junit5_2.12</artifactId>
            <version>3.5.24</version>
        </dependency>

        <dependency>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
            <version>1.3</version>
            <scope>test</scope>
    </dependency>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <configuration>
                    <systemPropertyVariables>
                        <pact.rootDir>target/pacts</pact.rootDir>
                    </systemPropertyVariables>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

## 测试示例

```
import au.com.dius.pact.consumer.MockServer;
import au.com.dius.pact.consumer.Pact;
import au.com.dius.pact.consumer.dsl.PactDslWithProvider;
import au.com.dius.pact.consumer.junit5.PactConsumerTestExt;
import au.com.dius.pact.consumer.junit5.PactTestFor;
import au.com.dius.pact.model.RequestResponsePact;
import org.apache.http.HttpResponse;
import org.apache.http.client.fluent.Request;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;

import static org.hamcrest.CoreMatchers.is;
import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.core.IsEqual.equalTo;

@ExtendWith(PactConsumerTestExt.class)
@PactTestFor(providerName = "test_provider", port = "1234")
public class ATest {

    @Pact(provider="test_provider", consumer="test_consumer")
    public RequestResponsePact createPact(PactDslWithProvider builder) {
        return builder
                .given("test state")
                .uponReceiving("ExampleJavaConsumerPactTest test interaction")
                .path("/")
                .method("GET")
                .willRespondWith()
                .status(200)
                .body("{\"responsetest\": true}")
                .toPact();
    }

    @Test
    void test(MockServer mockServer) throws Exception {
        HttpResponse httpResponse = Request.Get(mockServer.getUrl() + "/").execute().returnResponse();
        assertThat(httpResponse.getStatusLine().getStatusCode(), is(equalTo(200)));
    }
}
```

## 契约文件

```
{
    "provider": {
        "name": "test_provider"
    },
    "consumer": {
        "name": "test_consumer"
    },
    "interactions": [
        {
            "description": "ExampleJavaConsumerPactTest test interaction",
            "request": {
                "method": "GET",
                "path": "/"
            },
            "response": {
                "status": 200,
                "body": {
                    "responsetest": true
                }
            },
            "providerStates": [
                {
                    "name": "test state"
                }
            ]
        }
    ],
    "metadata": {
        "pactSpecification": {
            "version": "3.0.0"
        },
        "pact-jvm": {
            "version": "3.5.24"
        }
    }
}
```

运行测试

`mvn clean test`

这应该会在目录 target/pacts 中创建上面的包文件

# **制作人结束**

## POM 摘录

```
<dependencies>
        <dependency>
            <groupId>au.com.dius</groupId>
            <artifactId>pact-jvm-provider-junit5_2.12</artifactId>
            <version>3.5.24</version>
            <exclusions>
                <exclusion>
                    <groupId>junit</groupId>
                    <artifactId>junit</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.0.8.RELEASE</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.22.0</version>
                <dependencies>
                    <dependency>
                        <groupId>org.junit.platform</groupId>
                        <artifactId>junit-platform-surefire-provider</artifactId>
                        <version>1.2.0</version>
                    </dependency>
                    <dependency>
                        <groupId>org.junit.jupiter</groupId>
                        <artifactId>junit-jupiter-engine</artifactId>
                        <version>5.2.0</version>
                    </dependency>
                </dependencies>
                <configuration>
                    <systemPropertyVariables>
                        <pact.rootDir>target/pacts</pact.rootDir>
                    </systemPropertyVariables>
                </configuration>
            </plugin>

        </plugins>
    </build>
```

将 pact 文件从消费者(现在)复制到生产者项目模块目标目录

## 试验码

这要求 producer service 已经启动，并假定它与一个端点一起运行(http://localhost:8080/service/)。

```
@Provider("test-provider")
@PactFolder("target/pacts")
public class ProducerTest {

    private String providerUrl = "http://localhost:8080/service/";

    @TestTemplate
    @ExtendWith(PactVerificationInvocationContextProvider.class)
    void pactVerificationTestTemplate(PactVerificationContext context)
    {
        context.verifyInteraction();
    }

    @BeforeEach
    void before(PactVerificationContext context) throws Exception {
        context.setTarget(HttpTestTarget.fromUrl(new URL(
                providerUrl)));
    }

    @State({"test state"})
    public void toState() {
    }
```

那就跑

`mvn clean test`

输出如下所示:

```
Verifying a pact between consumer and test_provider
  Given test state
  GET REQUEST
    returns a response which
      has status code 200 (OK)
      includes headers
        "Content-Type" with value "application/json" (OK)
      has a matching body (OK)
```

## 用跳趾试验

这使用了一个测试 spring runner，因此您不需要单独运行该服务，尽管它涉及到模拟应用程序的某些部分。

```
@ExtendWith(SpringExtension.class)
@SpringBootTest(classes = Application.class, webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT,
        properties = "server.port=8080")

@Provider("test-provider")
@PactFolder("src/test/resources/pacts")
public class SpringProducerTest {

    @MockBean
    private Service service;

    @BeforeEach
    void setupTestTarget(PactVerificationContext context) {
        context.setTarget(new HttpTestTarget("localhost", 8080, "/context"));
    }

    @TestTemplate
    @ExtendWith(PactVerificationInvocationContextProvider.class)
    void pactVerificationTestTemplate(PactVerificationContext context) {
        context.verifyInteraction();
    }

    @State({"test State"})
    public void toState() {
        when(service.get(anyString()).thenReturn("dummy");
    }

}
```

# 打包文件管理

您可以使用 pact broker 来存放和提供 pact 文件。在这种情况下，包文件注释由包代理注释代替。

或者，如果您有一个存储库，那么对于消费者来说，管道的一部分是发布到 repo，而对于生产者来说，管道的一部分是在运行测试之前从 repo 中检索文件。

*原载于 2018 年 12 月 24 日*[*blog . ramjee . uk*](http://blog.ramjee.uk/2018/12/24/microservices-and-consumer-tests/)*。*