# 在 Spring Boot 云运行应用中处理来自 Google 发布/订阅推送订阅的请求

> 原文：<https://levelup.gitconnected.com/handling-requests-from-google-pub-sub-push-subscriptions-in-spring-boot-cloud-run-applications-d686c36811a1>

![](img/0300fa2e63e5d76c49fd82d1c4373047.png)

[Cloud Run](https://cloud.google.com/run/docs) 是一个无服务器应用平台，这意味着部署在那里的应用不能一直运行，因此不能使用默认的“拉”订阅类型。

推送订阅调用 HTTP 端点，这就是云运行服务被触发的方式。在[文档](https://cloud.google.com/run/docs/triggering/pubsub-push#run_pubsub_handler-java)中有为此目的构建的端点的例子，但是 Java 例子是非常基础的。这里有一种体面的方式来编写一个请求处理程序，它将与 Spring 框架一起工作。

假设你(在 Terraform 中)有一个关于广受好评的 Showtime show、 [Yellowjackets](https://www.sho.com/yellowjackets) 中角色生活中的事件的主题，以及一个过滤洛蒂所采取行动的订阅:

```
resource "google_pubsub_subscription" "example" {
  name   = "yellowjackets-events---lottie"
  topic  = data.google_pubsub_topic.yellowjackets_events.name
  filter = "attributes.character = \"Lottie\""
  push_config {
    push_endpoint = "${google_cloud_run_service.app.status[0].url}/lottie-events"
    oidc_token {
      service_account_email = data.google_service_account.pubsub_invoker.email
    }
  }
}
```

邮件正文可能如下所示:

```
{
  "date": "1996-10-01",
  "creepyStatement": "We won't be hungry much longer",
  "visionType": "AERIAL_INFERNO"
}
```

Spring Boot 请求处理程序可以这样写:

```
@RestController
@Slf4j
public class PubSubPushController { @RequestMapping(value = "/lottie-events",
                    method = RequestMethod.*POST*,
                    produces = MediaType.*APPLICATION_JSON_VALUE*,
                    consumes = MediaType.*APPLICATION_JSON_VALUE*)
    public ResponseEntity<?> receiveLottieEvent(
            HttpServletRequest req
    ) throws Exception {
        // (using Jackson)
        JsonNode bodyNode = objectMapper.readTree(req.getReader());
        String base64Data = bodyNode.get("message")
                .get("data").asText();
        byte[] data = Base64.*getDecoder*().decode(base64Data);
        LottieEvent event = objectMapper.readValue(
                data, LottieEvent.class);
        log.info(event.getCreepyStatement()); return new ResponseEntity<>(HttpStatus.*OK*);
    }
}
```

但是我发现对于控制器方法来说有点吵。它必须读取原始的 pub/sub 消息，获取文本形式的`message.data`属性——这是一个 Base64 编码的字符串，因此您必须对其进行解码，等等。我们可以把它抽象出来，这样我们就可以立即从控制器方法参数中得到我们需要的东西。

让我们为方法参数制作一个容器，其中`T`可以是一个`LottieEvent`:

```
@Data  // (Lombok)
public class PubSubPushRequest<T> { private final T body;
    private final Map<String, Object> attributes;
    // you might want headers here too
}
```

然后，执行一个`[HandlerMethodArgumentResolver](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/method/support/HandlerMethodArgumentResolver.html)`:

```
@Component
public class PubSubArgumentResolver
        implements HandlerMethodArgumentResolver { private final ObjectMapper objectMapper; public PubSubArgumentResolver(ObjectMapper objectMapper) {
        this.objectMapper = objectMapper;
    } @Override
    public boolean supportsParameter(MethodParameter parameter) {
        return PubSubPushRequest.class
                .isAssignableFrom(parameter.getParameterType());
    } @Override
    public Object resolveArgument(
            MethodParameter parameter,
            ModelAndViewContainer mavContainer,
            NativeWebRequest webRequest,
            WebDataBinderFactory binderFactory
    ) throws Exception {
        HttpServletRequest req = webRequest
                .getNativeRequest(HttpServletRequest.class);
        ParameterizedType dataType = (ParameterizedType)
                parameter.getGenericParameterType();
        Class<?> dataClass = (Class<?>)
                dataType.getActualTypeArguments()[0];
        JsonNode bodyNode = objectMapper.readTree(req.getReader());
        String base64Data = bodyNode.get("message").get("data")
                .asText();
        byte[] data = Base64.*getDecoder*().decode(base64Data);
        Object dataObj = objectMapper.readValue(data, dataClass); Map<String, Object> attributes = objectMapper.convertValue(
                bodyNode.get("message").get("attributes"),
                new TypeReference<Map<String, Object>>() {}); return new PubSubPushRequest<>(dataObj, attributes);
    }
}
```

…并注册它:

```
@Configuration
public class WebMvcConfiguration implements WebMvcConfigurer { private final PubSubArgumentResolver pubSubArgumentResolver; public WebMvcConfiguration(
            PubSubArgumentResolver pubSubArgumentResolver
    ) {
        this.pubSubArgumentResolver = pubSubArgumentResolver;
    } @Override
    public void addArgumentResolvers(
            List<HandlerMethodArgumentResolver> resolvers
    ) {
        resolvers.add(pubSubArgumentResolver);
    }
}
```

最后，我们可以更新控制器方法:

```
// ...
public ResponseEntity<?> receiveLottieEvent(
        PubSubPushRequest<LottieEvent> pushRequest
) throws Exception { LottieEvent event = pushRequest.getBody();
// ...
```

好多了！而`PubSubPushRequest`可以用于其他订阅的其他事件。也就是说，如果你确定你真的想知道这些女士在做什么。

![](img/dc0bb537a9dea645ac457d5d86d1d4b3.png)

到目前为止，哪名大学女子足球队队员成为“鹿角女王”似乎已经很清楚了