# 使用可选作为输入参数的参数

> 原文：<https://levelup.gitconnected.com/an-argument-for-using-optional-as-input-parameters-43b13fe6dfce>

![](img/51cf2df38c01ad9051f6f90160f05b71.png)

这篇文章可能是我迄今为止在这里写的最难的一篇。不是因为它确实是技术性的，也不是因为它需要事先进行大量的研究，而是因为它是有争议的，我必须确保我将在这里提出的论点是好的。尽管如此，我知道有些人不会同意我的观点。所以还是言归正传吧。

*文章原载于我的个人网站* [*下在 Java 中应该用 Optional 作为输入参数吗？*](https://petrepopescu.tech/2021/10/an-argument-for-using-optional-as-input-parameters/)

关于 Optional，以及它是应该作为方法的输入参数还是只作为返回值，Java 社区正在进行辩论。大多数人，包括 Java 生态系统中的大人物，都主张只使用 Optional 作为输出。甚至[Sonar 有一个规则](https://rules . Sonar source . com/Java/RSPEC-3553)也为可选的 as 参数授予“主要”严重性。因此，说我在这里的发言有争议可能是一种轻描淡写。

![](img/0c1ac83726ee97875dbbed9fcf9e65ae.png)

[https://courses . petrepopescu . tech](https://courses.petrepopescu.tech)

在大多数情况下，我同意。可选参数的存在通常可以归因于知识的缺乏或错误的设计。但是，我没有那么严格，也不片面。事情不仅仅是非黑即白。这就是为什么我认为在有限的场景中，不仅有一个可选的输入参数是可以的，而且是值得推荐的。

**我们的场景**

假设您有一个保存学生列表的 Datasource 类。这个列表是如何获得的现在并不重要。这个学生类有多个字段，如下面的代码所示。

```
public class Student {
    private String firstName;
    private String lastName;
    private String middleName;
    private int yearOfBirth;
    private int studyYear;
}
```

这是一个简单的数据类，没有什么太花哨的。为了简单起见，我没有编写任何 getters 或 setters，但是它们确实存在。现在，我们需要一种方法来检索这些学生，所以我们的 Datasource 类有一个方法“getStudentsFitleredBy()”。然而，我们不需要所有的学生，只需要一个子集:那些符合特定标准的学生。

我们可以按名字、姓氏、学习年份或这三者的任意组合来筛选。因此，我们需要在 getStudents()方法中传递所需的参数。

这就是我认为将这些参数作为可选参数(如下所示)会增加价值的地方。让我们看看返回经过筛选的学生列表的代码。

```
public List<Student> getStudentsFitleredBy(Optional<String> firstName, Optional<String> lastName, Optional<Integer> studyYear) {
    Stream<Student> studentsStream = this.students.stream();
    if (firstName.isPresent()) {
        studentsStream = studentsStream.filter(student -> student.getFirstName().equals(firstName.get()));
    }
    if (lastName.isPresent()) {
        studentsStream = studentsStream.filter(student -> student.getLastName().equals(lastName.get()));
    }
    if (studyYear.isPresent()) {
        studentsStream = studentsStream.filter(student -> student.getStudyYear() == studyYear.get().intValue());
    }

    return studentsStream.collect(Collectors.toList());
}
```

Optional 在这种情况下带来了三重优势。首先，您不必为每个期望的过滤参数组合编写重载方法。第二，如果不希望通过其中一个参数进行过滤，只需发送 Optional.empty()。第三，也是最重要的一点，您可以从方法的签名中清楚地看到，任何参数都可以省略，方法仍将按预期工作。

![](img/0c1ac83726ee97875dbbed9fcf9e65ae.png)

[https://courses . petrepopescu . tech](https://courses.petrepopescu.tech)

Optional 的引入是为了让你可以清楚地看到“这个方法可能会也可能不会返回一些东西”。同样的逻辑在这里，只是我们说“这个方法将返回由任意可选参数过滤的学生”。我知道你现在在想什么。为什么不在没有可选的情况下使用它们并执行空检查，而不是 isPresent()？是的，这是一个有效的问题，以这种方式编写方法是可行的。然而，你会失去清晰度。作为一个局外人或不同于实际实现该方法的开发人员，如果不查看文档或代码，您不会知道是否所有参数都是必需的。

至于在方法内部对可选的进行空检查，我认为这里不需要。如果有人发送 null，他显然是错误地使用了 API。

如果你现在认为没有人编写这样的方法来过滤列表，那你就错了。但即便如此，还是让我们稍微改变一下吧。与其有一个被过滤的列表，不如考虑这个 datasource 类是一个 DAO，它使用 CriteriaBuilder 构造一个 JPA 查询，从数据库中检索信息。现在事情看起来没那么傻了，是吗？

**其他可能同意我观点的人**

在标准 Java 库中，我找不到任何 Optional 被用作输入参数的例子。也许在某个地方有，但是现在它对我来说仍然是隐藏的。但是我可以提供一个使用它的著名框架的例子:Spring。

对于 Spring 控制器，如果您有请求参数(后面的那些？在 URL 中)，您可以在控制器的方法处理程序中将它们指定为可选的。这样，URL 中接收到的内容将被映射，而其他内容将是可选的

```
@GetMapping("/students")
public List<Student> getStudents(@RequestParam Optional<String> firstName,
                                 @RequestParam Optional<String> lastName,
                                 @RequestParam Optional<Integer> studyYear) {
    ...
}
```

*你可以有这样的要求*:`/同学们？firstName=Petre & studyYear=2 `年

对于该请求，lastName 参数将被映射到 Optional.empty()