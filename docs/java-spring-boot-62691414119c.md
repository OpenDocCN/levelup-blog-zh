# Java Spring Boot 第一应用程序

> 原文：<https://levelup.gitconnected.com/java-spring-boot-62691414119c>

![](img/1f0c91801609de0cf1143813f6116790.png)

照片由 [Unsplash](https://unsplash.com/s/photos/green-leaf?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[实体模型图形](https://unsplash.com/@mockupgraphics?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄

**Java | Spring Boot |春季数据 JPA | H2 数据库|百里香叶**

到目前为止，我在 web 开发旅程中关注的是 JavaScript 及其框架。我经常对非 JavaScript 框架感到疑惑，但是，由于我需要集中精力掌握我的 JavaScript 技能，所以我从来没有去尝试过。在我能够开始使用新的框架之前，学习一种新的编程语言的必要性阻碍了我。

在我作为初级 web 开发人员的第一份工作中，我必须熟悉 ColdFusion 和面向对象编程范例。(我写了一篇关于这个话题的四部分文章，你可以在这里找到)。

由于 Java 是面向对象和基于类的，我觉得我已经准备好开始我的 Java 之旅了。这个长达一周的过程，我在文章*中记录了 Java* 的第一步。我认为在开始使用 Java Spring 之前，先学习 Java 编程语言的基础知识是明智的。

对我来说，在这个阶段，框架 *Java Spring* 比作一个商场，一个接一个的商店，每个商店都提供不同的产品。 *Java Spring* 为不同类型的项目提供了一个模块化的框架集合， *Spring Boot* 、 *Spring 框架*、 *Spring 数据*、 *Spring 安全*、 *Spring for Android* 、 *Spring Mobile* 、 *Spring Web Services* 等等，都是可以根据您的应用目的轻松组合的项目。我明白了为什么 Java Spring 被认为是一个成熟的企业框架。

关于学习 Java Spring 的资源，在 [spring.io](https://spring.io/) 上有很好的文档，并提供了大量的示例代码和教程。你也不会有困难在互联网上找到你的问题的答案，因为社区是相当大的。

这篇文章只涉及 Java Spring 的非常非常小的一部分。它记录了一个 web 应用程序的设置，该应用程序将数据存储在关系数据库 *H2* 中，并使用模板引擎*百里香叶*将其呈现在浏览器中。

## 要求

对于 Windows 10 计算机上的此项目:

*Java 软件*

*   从 java.com[下载 *Java SE 开发工具包 14* 64 位](https://www.java.com/)
*   将您的*环境变量*的系统路径设置为【您的本地路径】\Java\jdk-14。
*   打开命令行(cmd)并用命令
    `> java -version`检查安装是否成功

*兼容 Java 的 IDE*
我使用的是 *Visual Studio 代码*但是也有明确的 Java 集成开发环境，例如免费开源的 *Apache NetBeans IDE* 、行业标准的 *IntelliJ IDEA、*或 *Eclipse* 。
在 Visual Studio 代码中，安装以下扩展:

*   Java 扩展包
*   Spring Boot 扩展包
*   Java 代码生成器

Java Spring 项目需要一组支持所请求特性的库和包。为此，需要一个项目管理工具。如果你熟悉 JavaScript，你可能会马上想到 *yarn* 或者 *npm* 。对于我们的项目，我们选择 *Maven* 作为项目管理工具。Maven 有助于构建和管理你的 Java 项目。它创建了一个所谓的*POM*(Project-Object-Model)，其中包含项目的所有信息和配置细节，并保存在一个 pom.xml 文件中。

Spring 提供了一个生成 spring boot 项目的初始化工具，名为 *Initializr。它使得建立目录结构、创建构建文件、配置依赖项等任务变得困难。非常容易。 *Initializr* 可以在[*start . spring . io*](http://start.spring.io)*，*中访问，您可以在那里配置您的项目并生成一个需要提取并导入到 spring 项目中的 zip 文件，或者您可以直接在您的 IDE 中访问它，我正在这个项目中这样做。*

使用组合键 *Ctrl + Shift + P，e*enter`>*Spring*`打开 **Visual Studio 代码**中的命令面板，进行如下选择:

*   选择 *Initialzr:创建一个 Maven 项目*
*   指定项目语言:Java
*   项目的输入组 Id:com . project
*   项目的输入工件 Id:first app
*   指定 Spring Boot 版本:2.2.6(不要选择快照)
    选择依赖项:
    *Spring Web* ，
    *Spring Data JPA，*
    *H2 数据库* 回车
*   选择文件夹:webDev/Java/Spring(您的文件位置)
*   生成到此文件夹

通过这几个步骤，我们的项目就建立起来了。

在我们开始之前，我想提一些一般性的注意事项。在这个项目的过程中，我们会遇到 Java 和 Spring 特有的概念，以及一般的计算机编程相关术语。深入研究每一个都需要太多的时间。对于从未听说过它们的人来说，我非常简短的解释是不够的，但我希望它们能在上下文中发挥作用，并鼓励你寻找更详细的解释。

P **持久化**
瞬态数据在应用程序会话中创建，一旦应用程序关闭就不再可用。存储在永久存储器中的数据在应用程序或系统重启后可用，它已达到*持久性*状态。大部分数据通过 *JDBC* (Java 数据库连接性)*序列化*、 *JCA* (Java EE 连接器架构)、JPA 等*持久化*在数据库中。

J

ibernate
是一个对象关系映射解决方案(ORM)。它会自动创建表和查询。不需要编写任何 SQL 来创建和查询表。

P *JavaBean 就是 POJO 的一个例子。*

一个 **注释** 元数据，用 *@* 符号添加到类、方法、变量、参数和包中。例如， *@Override* 指示编译器检查匹配的方法。如果它不存在，将会抛出一个错误。如果父类中不存在 *@Override* 注释，它将被自动创建。

Package一组相似类型的类。有*内置的*包，例如 java.lang、java.sql 等。并且还有*用户自定义*包。如果您创建一个保存类的新文件夹，这将被视为一个新包。

O **重载**
在一个类中，你可以创建多个方法，都用相同的名字。他们只需要改变参数列表。这将创建同一方法的多个版本。根据所提供的参数列表，会自动选择相应的方法。
重载也适用于构造函数。例如，Hibernate 需要一个空的无参数构造函数，而我们需要实例化对象的参数列表。因此，我们有两个构造函数。

注意:所有注释、接口、包等。需要导入在类中使用的。Visual Studio Code IntelliSense 将通过在关键字下加红色下划线来提醒您这一点，并给出适当的代码行作为建议，供您选择。

在这个项目中，您将会遇到以下注释。注释提供了一些定义类、方法、变量、参数等的元数据。会被注释掉。它对其进行配置并提供一些特定的功能。

*   **@实体**(多为表格，实体实例对应一个表格行)
*   **@Id** (指定实体的主键)
*   **@GeneratedValue** (指定值自动生成)
*   **@ManyToMany** (通知 Hibernate 多对多关系)
*   **@JoinTable** (通知 Hibernate 用属性创建一个连接表)
*   **@JoinColumn** (将列标记为联接列)
*   **@Controller** (将类定义为控制器，调度程序将扫描映射的方法和@RequestMapping 注释)
*   **@组件**(仅创建一个实例，在应用程序启动时自动创建)
*   **@Override** (允许子类覆盖继承的方法)
*   **@RequestMapping** (将 web 请求映射到处理程序控制器或方法)

现在，我们开始构建应用程序。出于演示目的，我们使用学生及其班级的基本示例。我们将创建一个数据库，并在浏览器中显示该数据。

首先，我们创建两个 beans 来保存学生和班级的数据。

# 创建 POJOs

在*src/main/Java . com . project . first app*中创建目录 **domain** 。
在文件夹*域中，*创建两个*POJO*bean，包含构造器、设置器和获取器:

## Student.java

*   为 *id* 、*名字、*和*姓氏*创建私有字段，并使用其数据类型。
    `private Long id;
    private String firstName;
    private String lastName;`
*   为学生将要学习的课程集合创建私有字段。使用*类型路线*的*接口* *设置*。
    集合是元素的集合，它不能包含重复的元素，因为学生每学期只会注册一次特定的课程。
    `private Set<Course> courses`
*   我们将这个私有字段课程 *s* 声明为 *HashSet。*HashSet 类实现*接口* *集合*并创建*集合*存储在*哈希表*中。一个 *HashSet* 只允许唯一的元素，并且顺序没有保证，这将 *Set* 与 *List* 区分开来。
    HashSet 类有类似 *add()* 、 *remove()* 、 *clear()* 、 *contains()* 等成员方法。可用。
    `private Set<Course> courses = new HashSet<>();`
*   创建一个空的构造函数(Hibernate，它处理 JPA 需要一个空的构造函数)。
    `public Student() {}`
*   使用参数 *firstName* 和 *lastName 创建一个构造函数。* 

## Classes.java

使用与*Student.java*中相同的结构。
我们需要字段 *id* (数据类型为 Long)，以及 course、courseTitle 和 courseDescription(数据类型均为*字符串*)。由于一个班级有很多学生，我们为私有字段*学生*创建一个*哈希表*，就像在*Students.java 做的那样。*

这两个 beans 携带我们想要存储到数据库中的数据。

# JPA 实体

我们将这些 POJOs 做成 JPA 实体,可以通过 Hibernate 保存到数据库中。*实体*代表一个表格，一个实体实例对应一个表格行。

以下适用于*Student.java*和*Course.java*

*   在类声明前添加 **@Entity** 注释。Hibernate 现在将这个类识别为一个实体。
    `@Entity`
    `public class Student {...`
*   用 **@id** 对持久字段 *id* 进行注释,@Id 注释使其成为实体的主 Id。
*   用**@ generated value(strategy = generation type)注释 id。AUTO)** 这告诉 Hibernate 如何生成 id。配置 *id* 值 **@ generated value(strategy = generation type)的自动递增。汽车)** `@Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;`

# 绘图

接下来，我们需要*分配关系*并进行映射。在我们的关系数据库设置中，在实体*学生*和*课程*之间有一个*多对多*关系。很多学生报一门课，一个学生可以报多门课。

在*Student.java*标注私人球场并绘制地图。
`@ManyToMany(mappedBy = “students”)`

在 Course.java，不需要映射，我们只标注关系。在它的正下方，我们设置了一个连接表，给它命名，并设置了两个表的外键。我们在 Course.java 的文件中这样做。


下一部分需要对代码的推导进行大量解释。我不会在这篇文章中涉及这一部分。相反，我只会提供代码，并要求你只是实现它。有很多资源可以详细解释，只需搜索*‘Java equals and hashCode’*。

我们将通过对每个方法应用注释 *@Override* 来覆盖方法 *equals()* 、 *hashCode()、*和 *toString()* 。这些方法在 object 类中，每个实例都将继承它们。
方法 **equals()** 检查两个对象是否相等， *x.equals(y)* 将返回 *false* 。
hashCode*是 Java 为每个对象生成的唯一编号。如果两个 hashCodes 相同，那么这些对象将为方法 *equals()* 返回 *true* 。由于这些方法的默认实现导致了一些冲突，我们需要覆盖它们。一些 ide，例如 *IntelliJ* 会根据请求自动生成这些方法。*

## Equal()和 HashCode()

安装 Hibernate 时需要。我们希望基于 id 的对象的相等性。

*在 Author.java:* `@Override
public int hashCode() {
return id != null ? id.hashCode() : 0;
}`

`@Override`
`public boolean equals(Object o) {`


在*Book.java*中使用相同的，除了`Course course = (Course) o;`

## toString()

我们重写此方法，以便能够在调试模式下呈现输出。
在*Author.java*和*Book.java*中实现 toString 方法(只需分别调整属性)。这将在调试模式下格式化输出。

`@Override
public String toString() {
return “Student{“ +
“id=” + id +
“, firstName=’ “ + firstName + ‘\’ ‘ +
“, lastName=’ “ + lastName + ‘\’ ‘ +|
“, courses=” + courses +
‘}’;
}`

# Spring 数据 JPA 存储库

一般来说，存储库是一个容器。JPA 存储库可以比作 DAO。通过 Hibernate 建立数据库连接，并管理所有 CRUD 函数。虽然在 DAO 中，我们需要自己编写查询，但是 JPA 存储库已经实现了所有的功能。

*   创建一个名为*仓库*的新包(目录)。
*   在新文件夹中创建一个类型为*接口*的新类，名为 *StudentRepository* 。
*   在新文件夹中创建一个类型为*接口*的新类，名为 *CourseRepository* 。
*   这两个类都扩展了 CrudRepository，它是对存储库进行通用 CRUD 操作的接口。需要提供两个必需的属性:类型和 id 数据类型。
    `public interface StudentRepository extends CrudRepository<Student, Long>{ }
    public interface CourseRepository extends CrudRepository<Course, Long> {}`

注意，CrudRepository 包含所有 CRUD 方法，Spring 已经实现了这些方法。如果查看文件 CrudRepository.class，会发现函数 *save()* ， *findById()* ， *existsById()* ， *findAll()* ， *delete()* 等。

接下来，我们将创建一些数据。由于我们还没有数据库，这些数据只能在本地内存中获得。

## 初始化数据

*   添加一个名为 *bootstrap 的新包。*
*   添加一个新类 *BootStrapData。*
*   用@Component 将类注释为组件。
*   我们想要实现接口 *CommandLineRunner* ，它搜索特定类型的实例(在我们的例子中是 StudentRepository 和 CourseRepository)并运行它们。
    `@Component
    public class BootStrapData implements CommandLineRunner {`
*   设置仓库的私有字段
    `private final StudentRepository studentRepository;
    private final CourseRepository courseRepository;`
*   创建构造函数
    `public BootStrapData(StudentRepository studentRepository, CourseRepository courseRepository) {
    this.studentRepository = studentRepository;
    this.courseRepository = courseRepository;
    }`
*   添加数据:`@Override
    public void run(String… args) throws Exception {
    Student bob = new Student(“Bob”, “Wild”);
    Student gil = new Student(“Gil”, “Mess”);
    Course oop = new Course( “CS202”, “OOP”, “Object-Oriented Programming”);
    Course intro = new Course(“SC101”, “Intro”, “Introduction to Computer Science”);
    bob.getCourses().add(intro);
    bob.getCourses().add(oop);
    gil.getCourses().add(intro);
    gil.getCourses().add(oop);
    oop.getStudents().add(bob);
    oop.getStudents().add(gil);
    intro.getStudents().add(bob);
    intro.getStudents().add(fran);
    studentRepository.save(bob);
    studentRepository.save(gil);
    courseRepository.save(oop);
    courseRepository.save(intro);`
*   打印信息到屏幕上大约有多少本书
    `System.out.println(“Number of Courses: “ + courseRepository.count());
    System.out.println(“Number of Students: “ + studentRepository.count());`

# H2 数据库

我们需要启用 H2 数据库控制台。在 application.properties 设置中`spring.h2.console.enabled = true`

*   运行文件 DemoApplication.java(右击文件名并选择*运行*)并检查在“/h2-console”中是否有 ***H2 控制台。数据库位于“JDBC:H2:mem:testdb”***
*   接下来，在浏览器中输入`*localhost:8080/h2-console*` *。*
*   控制台将会打开。检查上面的 *JDBC URL* 是否与 *H2 引导信息*匹配。
*   连接

# 配置控制器

在 MVC(模型-视图-控制器)设计模式中，控制器链接用户前端和应用程序的业务逻辑。它通过调用与管理和服务数据的*模型*通信的相应方法来处理来自*视图*的请求，将数据返回给*视图，*视图将数据呈现在浏览器中。通过用@Controller 注释一个类，你可以把它和它的所有成员方法变成控制器。

*   创建一个新的包，在这个上下文中有一个新的文件夹，叫做*控制器。*
*   在新包*控制器*中创建一个新文件*StudentController.java*。
*   用*@控制器*对类进行注释。*T39*
*   创建私有常量 *StudentRepository* 。
    `private final StudentRepository studentRepository;`
*   通过构造函数将*学生资源库*注入控制器。
    `public StudentController(StudentRepository studentRepository) {
    this.studentRepository = studentRepository;
    }`
*   创建一个返回所有学生数据记录的控制器方法。为此，您需要提供接口*模型*作为参数。模型接口充当数据容器，它提供不同的方法。我们使用 addAttribute()，它有两个参数。第一个(`“students”`)定义保存返回数据的变量，第二个(`studentRepository.findAll()`)将 CrudRepository-request 方法(在本例中为`find.All()`)应用于指定的存储库。
    `public String getStudents(Model model) {
    model.addAttribute(“students”, studentRepository.findAll());
    return “students”;
    }`
*   通过用 *@RequestMapping* 对其进行注释并提供路径来映射该方法。
    `@RequestMapping(“/students”)`

对文件*CourseController.java*采用相同的步骤。使用路径 *"/courses"* 进行映射。

我们已经完成了这个非常简单的例子的业务逻辑部分，现在将注意力转向前端。我们需要创建一些显示数据的视图。为此，我们使用百里香叶，它完全集成在

# 百里香叶

是来自 Apache 的开源 Java 库，完全集成到 Java Spring 中。它是一个用于 web 和非 web 环境的服务器端 Java 模板引擎，可以与 HTML5 一起使用。百里香使用附加的 HTML 标签属性，这些属性应用了 Spring 标准方言的规则。要了解更多信息，请前往 thymeleaf.org。

## 设置

检查 pom.xml 文件中的*百里香叶*的依赖项，如果没有列出，则添加它。
`<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>`

Spring 会自动在/ *资源中生成文件夹*模板*。*

在*模板*中，我们创建了文件*studentlist.html。* 添加通用的 HTML 结构。安装了扩展 Emmet 后，我只需要输入一个引号'！'并按回车键确认。

首先，在添加百里香功能之前，我们将为页面结构和一些模拟数据创建一个普通的 HTML 表。

## 超文本标记语言

`<table>
<tr>
<th>id</th>
<th>firstName</th>
<th>lastName</th>
</tr>
<tr>
<td>007</td>
<td>Jay</td>
<td>Dee</td>
</tr>
</table>`

转到 localhost:8080/students 并检查结果。

# 百里香叶

将 HTML 文件转换成百里香文档。在标签中添加一个百里香的链接。
`<html lang=”en” xmlns:th=”http:www.thymeleaf.org">`

现在，我们准备在餐桌上添加百里香叶功能。Thymeleaf 提供了一种 SpringStandard 方言，它提供了使用 Spring 所需的所有实现。我们只利用了百里香的一小部分能力。

将一个百里香叶迭代器添加到我们当前拥有模拟数据的表行中。我们迭代从控制器方法`getStudents()`返回的`“students”`数据。
`<tr th:each=”student : ${students}”>`

现在，我们可以访问每个数据列的对象属性。添加标签属性如图所示:
`<td th:text=”${student.id}”>007</td>`
`<td th:text=”${student.firstName}”>Jay</td>
<td th:text=”${student.lastName}”>Dee</td>`

再次进入*localhost:8080/students*查看结果。

为了稍微改进一下表格的样式，我们通过在 HTML 标题中添加以下链接，快速添加了 bootstrap，这是一个广泛使用的 CSS 框架。
`<link rel=”stylesheet” href=”https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">`

学生表的完整 HTML 代码:

`<div class=”card w-75">
<div class=”card text-center”>
<div class=”card-header”>Students</div>
<div class=”card-body”>
<table class=”table table-hover”>
<tr scope=”row”>
<th scope=”col”>id</th>
<th scope=”col”>firstName</th>
<th scope=”col”>lastName</th>
</tr>
<tr scope=”row” th:each=”student : ${students}”>
<td scope=”col” th:text=”${student.id}”>007</td>
<td scope=”col” th:text=”${student.firstName}”>mo</td>
<td scope=”col”th:text=”${student.lastName}”>dee</td>
</tr>
</table></div></div></div>`

为了完成这个小教程，创建一个具有相同结构的*course.html*文件。现在，我们可以在浏览器中呈现保存在 H2 数据库中的数据。
这当然不是一个完整的 web 应用程序，但它向我们介绍了用 Java Spring 构建 web 应用程序的开端。我希望你喜欢这次谈话。我很高兴从你那里得到任何建设性的反馈。

除了我为这篇文章查阅了许多不同的在线资源之外，我还要感谢 Spring 框架大师约翰·汤姆逊。他提供了大量的在线 Java Spring 课程，带有清晰的解释和逐步说明。

这篇文章的所有代码都可以在我的 GitHub 资源库 [JavaSpring_firstApp](https://github.com/maraxai/JavaSpring_firstApp) 中找到，按照本文中的顺序分成不同的分支。