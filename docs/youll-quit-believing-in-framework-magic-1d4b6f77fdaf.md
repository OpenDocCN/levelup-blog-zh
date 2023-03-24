# 不要相信框架“魔力”！

> 原文：<https://levelup.gitconnected.com/youll-quit-believing-in-framework-magic-1d4b6f77fdaf>

你真的需要使用所有这些框架和库吗？你知道如何实现它们吗？你能去掉它们中的任何一个吗？

![](img/68792646697fc18e2f99c7b097a6fa19.png)

Hibernate 的文档——由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Dollar Gill](https://unsplash.com/@dollargill?utm_source=medium&utm_medium=referral) 拍摄

# 1.“好的厨师不使用蛋糕配料”——克里斯汀·戈尔曼

我最近在 2011 年的一次 JavaZone 会议上看到了一个有趣的简短演讲。在录音中，Christin Gorman 用*现成的蛋糕配料*和 *Hibernate* 之间的幽默类比来展示盲目采用“酷”框架和库的负面影响。

尽管我并不热衷于放弃 Hibernate(*还没有*)，但我对视频中的想法产生了很多共鸣。

例如，学习一个新的、看起来很酷的框架的诱惑，而不是投资一些时间来巩固基础。

或者努力使代码适应各种框架和库，而不是将创造性的精力投入到解决实际的业务问题中。

该视频只有 9 分钟长，我强烈建议您观看:

# **2。“让魔法消失吧！”—鲍勃大叔**

看了 Christin 的演示，首先想到的是 Bob 大叔博客上的*[*让魔法消失*](https://blog.cleancoder.com/uncle-bob/2015/08/06/LetTheMagicDie.html)*文章*。*在这篇博文中，罗伯特·c·马丁从不同的角度讨论了同一个话题。**

**他从解释如何创建框架和库来补充编程语言的不足开始。**

**在那之后，他认为，作为开发人员，我们一直在寻找完美的语言或框架，因为我们相信魔法。**

**他用“*魔法*”来比喻框架和它们提供的功能。我们不完全理解的功能，我们不知道如何实现自己，但认为是理所当然的。**

**此外，Bob 叔叔说，唯一的逃避就是在盲目使用框架之前，实现它必须提供的任何特性。这将“*让魔法消失*”，并帮助我们理解问题到底是什么，这个库做什么，并将帮助我们决定我们是否真的需要它。**

**![](img/7811c49d26dc87bd6c054ea4b560da16.png)**

**照片由[达尼洛·达戈斯蒂诺](https://unsplash.com/@dani3cla?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)**

# **3.我个人的看法**

**这两个演讲真的让我思考。我开始思考我正在使用的所有不同的库和框架。我能去掉它们中的任何一个吗？如果没有，为什么？**

**过了一段时间，我得出结论，这个决定不必如此激进。在编程术语中，它不必是一个“布尔”值。**

**例如，即使不可能(或者我们不想)完全放弃一个库，我们仍然可以将它的使用限制在带来最大价值的特性上。**

**此外，我提出了四个问题，可以指导我决定是否应该使用一个库或框架的特性。女士们先生们，请允许我向你们提出“ *M.U.L.E.* ”问题:**

**![](img/1f8b3650852c4c0951d8294cdb3ffc5e.png)**

**劳拉·尼豪斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片**

**Magic:它带来多少**魔法**？
你能理解它是做什么的吗？即使之前没有使用过图书馆？即使没有阅读文档？你能自己实现它吗？**

**Uutility:它**有多大用处**？
它是在实现非常复杂的东西吗？这对你避免编写样板代码有帮助吗？它有更好的性能吗？如果您首先决定使用这个工具，它可能会提供一些东西。**

**L层:在你系统的哪个**层**里？
离你的领域层越近，你就越不愿意添加它。换句话说，我们希望避免在我们的领域层中使用外部库或侵入式框架。另一方面，当谈到“*基础设施*”层时，我们会不那么严谨。例如，您不会想要从头构建一个 OpenAPI/Swagger-UI 网页。**

**E**errors**:如何**易错**？
用它容易出错吗？这会让你的代码更难测试吗？**

# **4.代码示例**

**我希望你已经走了这么远。现在是时候做我们最喜欢的事情了，开始研究一些代码片段。**

**对于这一节，我选择了三个流行的 Java 库/框架。对于它们中的每一个，我们将把它与潜在的普通 Java 替代方案进行比较，并使用 M.U.L.E 问题对其进行评估。**

## **4.1.MapStruct**

**MapStruct 是一个 Java 库，可以用最少的配置生成 *Mapper* 对象。如果两个对象中的字段具有相同的名称和类型，它们将被自动映射。否则，将需要额外的注释。**

```
**@Mapper(componentModel = "spring")
public interface EmployeeMapstructMapper {

   @Mapping(target="employeeId", source="employee.id")
   @Mapping(target="employeeName", source="employee.name")
   EmployeeDto toDto(Employee employee);

   @Mapping(target="id", source="dto.employeeId")
   @Mapping(target="name", source="dto.employeeName")
   Employee fromDto(EmployeeDto dto);
}**
```

*   ****魔法**:我认为代码片段中的映射非常简单。如果没有以前的经验，我可能不知道 *componentModel = "spring"* 是做什么的。**
*   **如果字段具有相同的名称和类型，Mapstruct 允许我们通过自动映射字段来避免编写样板代码。**
*   **层:我们将使用领域层之外的映射器。**
*   ****错误**:如果我们决定重命名领域模型中的一个字段(例如“department”)，我们可能会忘记更新所有相关的映射器，并添加一个 *@Mapping* 注释来指定新名称。**

```
**public class EmployeeMapper { 
  public static EmployeeDto toDto(Employee employee) {
    if(isNull(employee)) {
      return null;
    }
    EmployeeDto dto = new EmployeeDto();
    dto.setEmployeeId(employee.getId());
    dto.setEmployeeName(employee.getName());
    dto.setDepartment(employee.getDepartment());
    return dto;
  }

  public static Employee fromDto(EmployeeDto dto) {
    if(isNull(dto)) {
      return null;
    }
    Employee employee = new Employee();
    employee.setId(dto.getEmployeeId());
    employee.setName(dto.getEmployeeName());
    employee.setDepartment(dto.getDepartment());
    return employee;
  }
}**
```

**对于大部分字段重合的大模型，MapStruct 可以派上用场。**

**不过，对于更复杂的场景(例如，映射嵌套对象)，这个库会带来太多的*魔法*来迎合我的口味。**

## **4.2.Spring 自定义验证程序**

**让我们看看用 Spring 创建自定义验证的推荐方法。这通常通过一个新的定制注释和一个针对它的 *ConstraintValidator* 实现来完成。**

**让我们看看它的样子，并将其与普通的 Java 替代方案进行比较:**

```
**@Documented
@Constraint(validatedBy = PhoneNumberValidator.class)
@Target({ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface ValidPhoneNumber {
  String message() default "Invalid phone number";

  Class<?>[] groups() default {};

  Class<? extends Payload>[] payload() default {};
}

public class PhoneNumberValidator implements ConstraintValidator<ValidPhoneNumber, String> {
  @Override
  public boolean isValid(String phoneNumbe, ConstraintValidatorContext cxt) {
   return phoneNumber != null
     && phoneNumber.matches("[0-9]+")
     && (phoneNumber.length() > 8)
     && (phoneNumber.length() < 14);
  }
}

public class Employee {
  private String name;
  private Long id;
  @ValidPhoneNumber
  private String phoneNumber;

  // all arguments constructor
}**
```

*   ****魔法**:我要说这里的“魔法”还真不少！我相信很难找到 *@ValidPhoneNumber 的实现。*和*，*也许更重要的是*，*可能很难确定*何时*和*是否*将执行该验证。**
*   ****实用程序**:与普通的 Java 解决方案相比，定制约束在这个上下文中没有添加任何额外的值。**
*   ****层**:如果 *Employee* 类是域层的一部分，那么普通 Java 版本要好得多。使用自验证构造函数，我们可以确保我们将始终拥有从业务角度来看有效的对象。**
*   ****错误:**虽然自验证构造函数永远不会出错，但是 Spring 约束只能在 Spring 上下文中进行计算。**

```
**public class PhoneNumber {
  private final String value;

  public PhoneNumber(String value) {
    if (value != null
      && value.matches("[0-9]+")
      && (value.length() > 8)
      && (value.length() < 14)) {
      throw new IllegalArgumentException("invalid Phone Number!");
    }
    this.value = value;
  }

  // getter for the 'value' field
}

public class Employee {
  private final Long id;
  private final String name;
  private final PhoneNumber phoneNumber;

  // all arguments constructor
}**
```

**此外，不依赖 Spring 的验证迫使我们想出一个很好的创造性解决方案，并丰富我们的领域模型。**

## **4.3.冬眠**

**是时候谈谈房间里的大象了。而且，当我说*大象*的时候，我是认真的:冬眠是巨大的！**

**尽管——大多数时候 JPA @实体存在于我们的核心域中，我们可以像 Bob 叔叔建议的那样，将数据库视为一个*细节*，一个外围组件。**

**诚然，Hibernate 并不总是生成最好的 SQL。但是，在我看来，它还能提供更多。例如，我不想实现诸如延迟加载或事务性之类的特性:它们与我试图解决的业务问题没有直接关系。**

**个人认为 Hibernate 的“*实用*”维度正在弥补它带来的“*魔力*”。**

# **谢谢大家！**

**感谢你阅读这篇文章，请让我知道你的想法！欢迎任何反馈。**

**如果你想阅读更多关于干净的代码、设计、单元测试、函数式编程以及许多其他内容，请务必查看我的其他文章。**

**如果你喜欢我的内容，可以考虑[关注或者订阅](https://medium.com/@emanueltrandafir)到邮件列表。**

**最后，如果你考虑成为一个中等会员，支持我的博客，这里是我的[推荐人](https://medium.com/@emanueltrandafir/membership)。**

**编码快乐！**