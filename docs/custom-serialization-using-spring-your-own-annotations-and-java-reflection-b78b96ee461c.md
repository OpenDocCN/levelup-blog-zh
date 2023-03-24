# 在 Spring 中使用您自己的个性化注释和 Java 反射进行定制序列化

> 原文：<https://levelup.gitconnected.com/custom-serialization-using-spring-your-own-annotations-and-java-reflection-b78b96ee461c>

有时，我们希望在域对象作为响应被发送回来之前，以一种非常特定的方式对它们进行序列化。有时这些方式在我们的实体中形成了一个更广泛的模式。注释对于建议我们的应用程序如何序列化某些类型的对象非常有帮助。

## 什么是序列化？

这通常是指将对象的状态转换成字节流。但是在开发 web 服务的上下文中，这通常意味着将我们的域对象的状态转换成遵循某些约定的字符串，比如 JSON。

```
public class Position {
    double latitude = 0.0
    double longitude = 0.0;
}
```

将此位置对象序列化为 JSON 字符串将输出:

```
{ "latitude": 0.0, "longitude": 0.0}
```

默认情况下，Spring 提供了许多不同的策略来序列化对象，但是有时候多做一点是值得的。

## 我们要建造什么？

我们将通过一个非常简单的例子来展示如何使用我们自己的自定义注释来确定字段如何被序列化。

# 领域

我将让我所有的域类扩展一个名为 *IdentifiedResource* 的类，它规定了我的资源在我的应用程序中是如何被标识的。这种类将成为我们的自定义序列化程序的目标。

```
**@JsonSerialize(using = MyCustomSerializer.class)**
public class IdentifiedResource {

    private String id; //...
}
```

注意，我已经指出了我将使用什么类来把这种对象转换成 JSON。我们稍后将回到这一点。

为了有一个更广泛的例子，让我们用一个名为 *NamedResource* 的类来扩展这个类，它将代表我们领域中具有人类可读名称的对象。

```
public class NamedResource **extends IdentifiedResource** {

    private String name;

    //...

}
```

现在让我们给它一些背景。

```
public class Manufacturer extends NamedResource { }public class Model  extends NamedResource{}public class Car extends IdentifiedResource {

    **@Identified**
    private Model model;

    **@Named**
    private Manufacturer manufacturer;

    **@ServiceDependent**(service = MyService.class, 
        method = "getCarPosition")
    private Position position;

    //...
}
```

哇哦。那是什么？你看这辆车有型号，制造商和位置。谁知道呢，也许这是一家为客户提供按需汽车共享服务的公司。

但是那些注释是什么呢？我当然不会在 Spring 的注释包中看到它们。让我们仔细看看。

## @已识别

我希望这个注释告诉我的序列化程序，我不希望这个内部字段被完全序列化，只要给我它的 ID 就可以了。

## @已命名

类似于标识的注释，但是这里我想让它获取资源的名称。

## @服务依赖

这个有点棘手。我将使用这个来告诉我的序列化程序，我的字段实际上依赖于在特定服务上执行一个方法。

如果您有关于存储在不同位置的对象的信息，并且希望在序列化时将它们打包在一起，这可能非常有用。

## 花些时间反思

![](img/6daa3527c0ea5a369d6833542551f51c.png)

Java 反射:如果您从未使用过这个强大的 Java 特性，现在是开始使用的好时机，因为我们将在下一步中使用它。它对于在运行时修改行为或类和方法非常有用，这正是我们正在尝试做的。我不会详细解释，但网上有很多[指南。](https://www.baeldung.com/java-reflection)

## 串行器

好了，我们已经有了领域对象、自定义注释，心中只有美好的愿望。是时候写一个类了，这个类将会为我们做一些艰苦的工作。

```
public class **MyCustomSerializer** extends StdSerializer<Object> {

    public **MyCustomSerializer**() {
        super(Object.class);
    }

    @Override
    public void **serialize**(Object o, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException {
        *//...*
    }

}
```

这是我们需要的序列化器的基本结构。如果我们愿意，我们可以把我们的 IdentifiedResource 作为类型参数给它，但是因为我们将使用反射来处理字段和方法，所以它实际上适用于我们想用它处理的任何其他类型的对象。

# 该串行化方法

啊，演出的明星。但首先要做的是。

## 如何处理每个注释？

当然，我们可以在函数体中有一长串的*if*和*else*，但是，嘿，我们不要这样做，好吗？我将尝试编写一个函数来搜索用 **@ SerializerAdvice** 注释的函数，它将告诉我如何序列化我的注释字段。

## 找到正确的方法

我希望所有方法都位于我的自定义序列化程序中，所以我将简单地创建一个映射，包含所有合适的方法，就在构造函数中。

```
private Map<Class, Method> **serializationMap** = new HashMap<>();public MyCustomSerializer() {
    super(Object.class);

    Arrays.*stream*(this.getClass().getDeclaredMethods())
            .filter(method -> method.isAnnotationPresent(SerializerAdvice.class))
            .forEach(method -> {
                SerializerAdvice annotation = method.getAnnotation(SerializerAdvice.class);
                Class annotationClass = annotation.value();
                **serializationMap.put(annotationClass, method);**
            });
}
```

下面是我们为获得方法映射所做的工作:

*   对当前类的所有方法进行流式处理
*   过滤掉那些没有用 SerializerAdvice 注释的
*   把剩下的放在一个 HashMap 中，告诉我们每种类型的注释使用哪种方法。

## 连载

让我们看看 serialize 函数应该是什么样子。

```
@Override
public void **serialize**(Object target, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException {

    jsonGenerator.writeStartObject();Class targetClass = target.getClass();

    for (Field field : targetClass.getDeclaredFields()) {

        Annotation[] annotations = field.getAnnotations();

        Optional<Method> advisingMethod = Arrays.*stream*(annotations) 
            .filter(annotation ->            serializationMap.containsKey(annotation.annotationType())) 
            .map(annotation -> serializationMap.get(annotation.annotationType())) 
            .findFirst(); 

        if (advisingMethod.isPresent()) {
             advisingMethod.get().invoke(this, target, field, jsonGenerator);
        } else { 
             PropertyDescriptor descriptor = BeanUtils.*getPropertyDescriptor*(target.getClass(), field.getName());
             jsonGenerator.writeObjectField(field.getName(), descriptor.getReadMethod().invoke(target));
        }

    }

    jsonGenerator.writeEndObject();
}
```

在我们开始用*JSON generator . write startobject()，*序列化我们的对象后，我们得到被序列化的对象的类，并遍历它的字段。以我们的汽车为例，我们正在迭代制造商、型号和位置。

然后，我们可以获得每个字段的所有注释的数组:

```
Annotation[] annotations = field.getAnnotations();
```

看看我们的序列化器中是否有合适的方法来处理这种注释:

```
Optional<Method> advisingMethod = Arrays.*stream*(annotations) 
        .filter(annotation -> serializationMap.containsKey(annotation.annotationType())) 
        .map(annotation -> serializationMap.get(annotation.annotationType())) 
        .findFirst();
```

如果我们碰巧找到了一个，我们就将该字段的序列化委托给它，否则我们就使用一个 [PropertyDescriptor](https://docs.oracle.com/javase/7/docs/api/java/beans/PropertyDescriptor.html) 让业务照常进行。

```
if (advisingMethod.isPresent()) {

     advisingMethod.get().invoke(this, target, field, jsonGenerator);} else { 

     PropertyDescriptor descriptor =
BeanUtils.*getPropertyDescriptor*(target.getClass(), field.getName());

     jsonGenerator.writeObjectField(field.getName(), descriptor.getReadMethod().invoke(target));}
```

## 建议方法

既然我们的自定义序列化程序已经启动并运行，我们所要做的就是引入用@ SerializerAdvice 注释的方法，这些方法将告知如何处理每个字段。因此，让我们为每个自定义注释创建一个。

@ Named 和@ Identified 的方法非常简单。我们只需获取字段的值，将它们转换成我们期望的对象类型(NamedResource 和 IdentifiedResource ),然后只提取我们想要的值(name 和 id)。

```
@SerializerAdvice(Named.class)
public void nameField(Object target, Field field, JsonGenerator jsonGenerator) throws Exception {
    PropertyDescriptor descriptor = BeanUtils.*getPropertyDescriptor*(target.getClass(), field.getName());
    Object fieldValue = descriptor.getReadMethod().invoke(target);
    String name = ((NamedResource) fieldValue).getName();
    jsonGenerator.writeObjectField(field.getName(), name);
}

@SerializerAdvice(Identified.class)
public void identifyField(Object target, Field field, JsonGenerator jsonGenerator) throws Exception {
    PropertyDescriptor descriptor = BeanUtils.*getPropertyDescriptor*(target.getClass(), field.getName());
    Object fieldValue = descriptor.getReadMethod().invoke(target);
    String name = ((IdentifiedResource) fieldValue).getId();
    jsonGenerator.writeObjectField(field.getName(), name);
}
```

@ ServiceDependent 字段的建议更复杂，需要一个 ApplicationContext bean，因为我们希望访问我们的服务 bean 之一来完成工作:

```
@Autowired
private ApplicationContext context;@SerializerAdvice(ServiceDependent.class)
public void getFromService(Object target, Field field, JsonGenerator jsonGenerator) throws Exception {
    ServiceDependent annotation = field.getAnnotation(ServiceDependent.class);
    Class serviceBeanClass = annotation.service();
    String methodName = annotation.method();
    Object service = context.getBean(serviceBeanClass);
    Method method = service.getClass().getMethod(methodName, target.getClass());
    Object serviceMethodInvokeResult = method.invoke(service, target);
    jsonGenerator.writeObjectField(field.getName(), serviceMethodInvokeResult);

}
```

在服务类中有许多可能的方法来找到正确的方法，最终您可以选择这样的函数应该是什么样子。在我的例子中，我希望它们将当前正在序列化的类的对象作为参数，并返回与正在序列化的字段类型相同的对象:

```
@Service
public class MyService {

    public Position getCarPosition(Car car) {
        Position position = new Position();
        // Do your stuff
        return position;
    }

}
```

## 我们来试试吧！

我写了一个简单的控制器，它给我一个完全不存在的汽车实例。

```
@RestController
public class MyController { @GetMapping(value = "/car")
    public ResponseEntity<Car> getMeMyCar() {
        Manufacturer manufacturer = new Manufacturer();
        manufacturer.setId("koyota-identifier");
        manufacturer.setName("Koyota");
        Model model = new Model();
        model.setId("torolla-identifier");
        model.setName("Torolla");
        Car car = new Car();
        car.setId("car-identifier");
        car.setManufacturer(manufacturer);
        car.setModel(model);
        return ResponseEntity.*ok*(car);
    }}
```

以及一个使用 Spring 的 MockMvc 获取资源并检查字段是否按预期序列化的测试。

```
@Test
void testCarSerialization() throws Exception {
    this.mockMvc.perform(*get*("/car"))
            .andExpect(*jsonPath*("$.manufacturer")
                  .value("Koyota"))
            .andExpect(*jsonPath*("$.model")
                  .value("torolla-identifier"))
            .andExpect(*jsonPath*("$.position.latitude")
                  .value(0.0))
            .andExpect(*jsonPath*("$.position.longitude")
                  .value(0.0));
}
```

# 有用！

这是我们的系列车:

```
{
    "model": "torolla-identifier",
    "manufacturer": "Koyota",
    "position": {
        "latitude": 0.0,
        "longitude":0.0
    }
}
```

这是添加定制注释的一种简单而实用的方法，以便为您的服务提供一种更加结构化的序列化资源的方式。你可以在这里找到完整的代码。我希望你喜欢它！