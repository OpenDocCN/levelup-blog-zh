# Java 库来提高您的生产力

> 原文：<https://levelup.gitconnected.com/java-libraries-to-increase-your-productivity-79093535a371>

![](img/0422318459ccce0d59c69ccbec869ac1.png)

照片由 [**安德里亚斯·克拉森**在](https://unsplash.com/@schmaendels) [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄

编写样板代码或实际上不需要的代码会浪费很多时间。java 有一个很大的社区，它创建了许多库，通过消除编写样板代码的需要或为常见的 Java 问题提供实用程序，帮助您提高生产率。在本文中，我们将探索其中的一些库。

# ***1)龙目岛项目***

厌倦了只有 getters、setters、constructors、overridden equals 方法等样板方法的文件？好吧，那龙目岛是给你的。大多数 IDE 只需点击一下按钮就能自动生成 getter 和 setter 方法，但不同之处在于 IDE 在 java 文件本身中生成这些方法，而 Lombok 直接在类文件中生成所有方法。不仅如此，Lombok 还帮助我们删除了其他样板代码。让我们通过一些例子来更好地理解这一点。

```
public class StudentServiceWithoutLombok implements IStudentService{
private static final Logger log = Logger.getLogger(StudentServiceWithLombok.class);[@Override](http://twitter.com/Override)
 public void method1() {
  log.info("In method1 ");
 }}
```

上面的代码没有 Lombok，现在让我们用 Lombok 框架写同样的代码。

```
@Log4j
public class StudentServiceWithLombok implements IStudentService{[@Override](http://twitter.com/Override)
 public void method1() {
  log.info("In method1 ");
 }}
```

您现在可以看到，我们不必在这个文件中初始化 logger 变量，它只是一行，但在有数千个文件的较大项目中，您不必一次又一次地编写相同的行，相反，您可以专注于您的业务逻辑。下面再给你举个例子就是多了一个没有龙目岛的班级。

```
public class Student{

    private Integer rollNumber;
    private String name;

    public Student(Integer rollNumber, String name)
    {
        super();
        this.rollNumber = rollNumber;
        this.name = name;
    }

    public Integer getRollNumber()
    {
        return rollNumber;
    }

    public void setRollNumber(Integer rollNumber)
    {
        this.rollNumber = rollNumber;
    }

    public String getName()
    {
        return name;
    }

    public void setName(String name)
    {
        this.name = name;
    }

    [@Override](http://twitter.com/Override)
    public String toString()
    {
        return "Student ["
            + "rollNumber=" + rollNumber
            + ", name=" + name + ", "
            + "]";
    }
}
```

现在，如果我必须用 Lombok 编写相同的代码，它将如下所示:

```
@AllArgsConstructor
@Data
public class Student{
    private Integer rollNumber;
    private String name;
}
```

震惊？嗯，Lombok 的代码没有任何样板代码，更容易阅读。

Lombok 可以删除大量样板代码，您可以专注于业务逻辑，代码看起来也会更整洁。Lombok 还有比我们在这篇文章中讨论的更多的东西，你可以从他们的官方网站[查看 Lombok 项目的所有酷功能。](https://projectlombok.org/)

# ***2) MapStruct***

你在你的 java 项目中使用多层架构了吗？如果是，那么你肯定应该考虑使用 MapStruct。在多层体系结构中，应用程序的所有层都是松散耦合的，因此有时您必须将一层中的对象映射到另一层对象，一个常见的例子是驻留在持久层中的实体将被映射到应用层中的 d to。将简单的 POJO 映射到另一个 POJO 可能需要大量样板代码。考虑下面两个类，这里我们必须将学生的实体类与其 DTO 进行映射。

```
@Entity
@Data
public class Student{
    private Integer rollNumber;
    private String name;
}@Data
public class StudentDTO{
    private Integer rollNumber;
    private String name;
}
```

如果你必须映射两个类，那么你可能要写一个转换器类，它有一个函数，将 Student 作为输入，将 StudentDTO 作为输出，如下所示

```
public class StudenttoStudentDTOconvertor
{
  public StudentDTO convert(Student student)
 {
    StudentDTO studentDTO = new StudenDTO();
    studentDTO.setName(student.getName);
    studentDTO.setRollNo(student.getRollNo());
    return studentDTO;
 }
}
```

学生是一个简单的类，只有 2 个字段，然后你也可以看到，我们必须写 4 行代码，如果这个类有大量的字段，代码行数的转换器也将增加。为了避免编写这种简单的样板代码，我们使用了 MapStruct 来自动生成代码。现在让我们用 MapStruct 重写转换器类代码。

```
@Mapper 
public interface StudenttoStudentDTOMapper 
{ 
    StudentDTO map(Student student);     
}
```

嗯，真的是这样。你真的不需要实现这个接口，MapStruct 会自动为你实现。万一将来你决定在实体类中添加一个新的字段，比如父亲的名字，那么你可以在 DTO 中添加相同的字段，你真的不需要在映射器类中做任何改变。Co，ol 没有？要了解更多关于 MapStruct 的其他酷功能，请查看他们的官方网站。

# **T3)3)番石榴 **

我无法比在 guava 的 GitHub 库中更好地解释它了。这是我刚刚从 Github 复制的定义:

> Guava 是 Google 的一组核心 Java 库，包括新的集合类型(如 multimap 和 multiset)、不可变集合、一个图形库，以及用于并发、I/O、哈希、缓存、原语、字符串等的实用程序！它在 Google 的大多数 Java 项目中被广泛使用，也被许多其他公司广泛使用。

番石榴听起来很酷吧？番石榴有实用程序，不仅可以帮助你解决 java 中的简单问题，也可以解决非常复杂的问题。不理解我吗？让我们用一个例子来理解

```
public void checkAge(int age) {
      if(age<0)
      {
        throw new IllegalArgumentException(“age id invalid”);
      }
    }
```

同样的程序可以用番石榴库写成:

```
public void checkAge(int age) {
      Preconditions.checkArgument(age > 0, “Invalid Age”);
    }
```

这两个程序做着同样的工作，但是利用番石榴库的代码更干净。它可能看起来不多，但在番石榴中有更多。我们再举一个例子。

```
public void example() {
    // Initialising Guava LinkedHashMap Collection
    Map<String, String> myMap = Maps.newLinkedHashMap();
    myMap.put(“name”, “abc”);
    myMap.put(“rollno”, “123”);
    String delimiter = “&”;
    String separator = “=”;
    String result = Joiner.on(delimiter).withKeyValueSeperator(separator).join(myMap);
    String expected = “name=abc&rollno=123”;
    assertThat(result, expected);
}
```

从上面的程序中，您可以看到使用 guava 将地图转换为字符串是多么容易，如果您在 java 中遇到任何问题，无论是与缓存、并发还是任何其他领域相关的问题，大多数情况下您都可以在 guava 库中找到解决方案。要了解更多关于番石榴库的信息，别忘了查看他们的 Github repo。

# ***4)假装***

如果您是一名 java 开发人员，您肯定会遇到必须调用一些 REST API 的情况。有许多像 *OkHttpClient* 这样的 HTTP 客户端可以用来调用 rest API，但是它们通常需要大量的代码，这些代码对我们的业务需求没有任何帮助，如果您调用多个 REST API，您的代码也会重复。在 HTTP 客户端库之上，我们还有像 RestTemplate 这样的包装器库，让我们的生活变得更简单。用 *OkHttpClient 编写的示例代码将如下所示。*

```
OkHttpClient okHttpClient = new OkHttpClient();
Request request = new Request.Builder()
    .url("[https://127.0.0.1/posts/1](https://jsonplaceholder.typicode.com/posts/1)")
    .build();
Call call = okHttpClient.newCall(request);
try (Response response = call.execute();
     ResponseBody body = response.body()) {String string = body.string();
  System.out.println(string);
} catch (IOException e) {
  throw new RuntimeException(e);
}
```

请注意我们必须如何处理异常，我们还必须将响应转换为我们的对象，以便我们可以使用它。你不觉得我们为了调用一个简单的 Rest API 写了很多代码吗？让我们看看使用 RestTemplate 时代码会是什么样子。

```
final String uri = "[https://127.0.0.1/posts/1](https://jsonplaceholder.typicode.com/posts/1)"
RestTemplate restTemplate = new RestTemplate();
PostDTO result = restTemplate.getForObject(uri,PostDTO.class);
```

在 RestTemplate 中非常简单，你的响应也被转换成我们可以使用的对象，对吗？但是，如果我们在每个 API 中调用多个 REST API，我们必须从属性中获取 URL 并以编程方式调用 Rest API，我甚至没有提到我们仍然必须进行错误处理。有两种方法可以处理错误:1 是简单地编写 try-catch，但必须为每个调用编写相同的代码，另一个选项是异常处理程序，但在这两种情况下，我们都必须编写更多的代码。现在让我们看看如何使用 feign 调用 Rest API。

```
*@FeignClient(name="content-service", url="127.0.0.1")*
public interface ContentService {
  @RequestLine("GET posts/{postNumber}")
  String getDocumentByType(@Param("contentType") String postNumber);
}
```

信不信由你，就是这样。Feign 将自动生成代码来调用 Rest API，我们作为开发人员将无需担心任何事情。如果需要添加更多的 API，我们可以添加方法，而不必重写代码来调用 REST API，很酷吧？如果你想在某些特定错误的情况下重试一些 API，我们可以通过改变配置来轻松地改变 URL，feign 可以很容易地帮你做到这一点。如果你使用 feign，测试你的代码是非常容易的。从他们的 GitHub [repo](https://github.com/OpenFeign/feign#why-feign-and-not-x) 中查看更多关于 feign 的酷功能。

# ***5)冬眠***

嘿，你讨厌在你的代码中写 SQL 查询吗？您有时会忘记在运行查询后关闭数据库连接吗？那这个图书馆就是为你准备的。当您尝试访问数据库时，会涉及到大量的样板代码，您必须打开连接、运行查询、将结果集转换为实体、关闭连接，除此之外，您还必须编写高效的查询。如果你使用 hibernate，你就不必担心上述任何事情，你可以专注于你的业务而不是这些。你可以从[这里](https://medium.com/javarevisited/top-5-hibernate-online-training-courses-for-beginners-and-advance-java-programmers-469460596b2b)了解更多关于 hibernate 的知识。

# ***结论***

在编程的世界里，你希望尽量减少应用程序中的样板代码，也不希望重新发明轮子，因为当你编写越来越多的代码时，你将需要创建测试用例来测试每一行，并且总是有可能引入一些 bug。在 Java 中，有多个库有助于消除样板代码，有些库提供了很好的生产就绪实用程序，这样您就不必重新发明轮子了。