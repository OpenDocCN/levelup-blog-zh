# 每个软件工程师都应该知道的事情:构建器模式

> 原文：<https://levelup.gitconnected.com/things-every-software-engineer-should-know-the-builder-pattern-d076963f3298>

![](img/c9828b46eda1fa59475f610a2cfc44bb.png)

# 泰勒:博士

builder 模式用于创建类，这使得创建类变得更加容易。当构建器创建的类特别大/复杂时，这很有用。构建器的一个主要优势是它们使代码更易于阅读。

它允许以下对象构造:

```
BookDTO book = new BookDTO( "072a36ae-0a6f-46cc-bdc9-aa1dcff38fd5",
  ZonedDateTime.now(),
  null,
  "c0315e90-6faa-40ed-a796-7609a545c5a3",
  "Clean Code: A Handbook of Agile Software Craftsmanship",
  "Very interesting book on how to write your code in a clean way",
  "9788025122853",
  "45bbce25-4d7d-47ac-84d2-6bc7d4202890",
  "Paperback",
  431);
```

要写成:

```
BookDTO book = BookDTOBuilder.builder()
  .withId("072a36ae-0a6f-46cc-bdc9-aa1dcff38fd5")
  .withDateCreated(ZonedDateTime.now())
  .withDateModified(null)
  .withAuthorId("c0315e90-6faa-40ed-a796-7609a545c5a3")
  .withTitle(
    "Clean Code: A Handbook of Agile Software Craftsmanship"
  )
  .withDescription(
    "Very interesting book on how to write your code in a clean way"
  )
  .withISBN("9788025122853")
  .withSeriesId("45bbce25-4d7d-47ac-84d2-6bc7d4202890")
  .withEdition("Paperback")
  .withNumberOfPages(431)
  .build();
```

在我看来，它更容易阅读，因为浏览者对创建对象所用的参数有一个更清晰的概念。

# 谁死了

大家好，我是 Doogal，我是 Babylon Health 的技术负责人，我是 doodl.la 的创始人，我也花了几年时间从几个非常有才华的人那里学习软件工程，这些故事是我努力向前回报的方式。

在我担任技术主管期间，我指导了许多新的软件工程师，我发现经常有这样的情况，工程师不知道他们不知道什么。所以这个“每个软件工程师都应该知道的事情”系列是我在做软件的第一年给自己的信息的备忘单。

软件是一个很大的主题，黄金法则是，任何问题的答案都可以从“视情况而定……”开始，因此，这些故事中的信息是不完整的。它试图给出最基本的信息，所以当你读这些故事时，请记住兔子洞比这里展示的主题更深。

# 问题:带有太多参数的构造函数

## 有什么问题？

有时候当你写一个类的时候，你想创建一个有很多字段的对象，5 个，10 个，20 个，30 个，也许更多。同时，您希望保持对象不可变，所以一旦构造好了，您就不希望允许修改它。对于不可变的对象，它应该具有的属性之一是没有任何 setters，因为如果您可以在构造后设置字段，那么对象就不再是不可变的。

要在大多数传统语言中创建这个对象，你需要使用一个构造函数，比如说我们在图书领域工作，我们有一个 BookDTO，它包含以下字段:

```
public BookDTO { private String id;
  private ZonedDateTime dateCreated;
  private ZonedDateTime dateModified;
  private String authorId;
  private String title;
  private String description;
  private String isbn;
  private String seriesId;
  private String edition;
  private Integer numberOfPages;}
```

注意:DTO 是指数据传输对象，这些对象通常是为了在应用程序中携带信息而创建的。它们被创建一次，然后在几个地方被读取。

在这个例子中，我们的 BookDTO 有 10 个字段，其中大部分是字符串，有些可能为空。当提到构造函数时，它会是这样的:

```
public BookDTO( String id,
  ZonedDateTime dateCreated,
  ZonedDateTime dateModified,
  String authorId,
  String title,
  String description,
  String isbn,
  String seriesId,
  String edition,
  Integer numberOfPages) { this.id = id;
  this.dateCreated = dateCreated;
  this.dateModified = dateModified;
  this.authorId = authorId;
  this.title = title;
  this.description = description;
  this.isbn = isbn;
  this.seriesId = seriesId;
  this.edition = edition;
  this.numberOfPages = numberOfPages;}
```

## 为什么会有问题？

那么为什么这是一个问题呢？这是一个构造函数，它可以工作，你可以用它来创建你的不可变对象。在一段代码中，这个构造函数可能是这样使用的:

```
BookDTO book = new BookDTO( "072a36ae-0a6f-46cc-bdc9-aa1dcff38fd5",
  ZonedDateTime.now(),
  null,
  "c0315e90-6faa-40ed-a796-7609a545c5a3",
  "Clean Code: A Handbook of Agile Software Craftsmanship",
  "Very interesting book on how to write your code in a clean way",
  "9788025122853",
  "45bbce25-4d7d-47ac-84d2-6bc7d4202890",
  "Paperback",
  431);
```

或者，更有可能的是，它将通过从某种数据库记录类型对象中提取字段来构造:

```
Record record = sqlExecutor.fetchOne( "SELECT * FROM books WHERE id = ?",
  bookId);
BookDTO book = new BookDTO( record.get(0),
  record.get(1),
  record.get(2),
  record.get(3),
  record.get(4),
  record.get(5),
  record.get(6),
  record.get(7),
  record.get(8),
  record.get(9));
```

阅读这个可能很困难，更糟糕的是，这种设置很容易出现错误。如果我们在 BookDTO 中添加一个新的字符串字段会发生什么？我们需要确保将它添加到构造函数调用中，但是如果我们不注意，我们可能会将错误的字符串赋给特定的参数。

静态类型语言在这方面帮了你一点忙，但这仍然是个问题。在像 Javascript 这样的动态类型语言中，这可能是一个更大的问题，因为构造函数会接受你给它的任何东西，如果你没有正确地测试，这可能会导致很难跟踪的错误。

## 经验法则:超过 4 个参数

那么对于一个构造函数来说，多少个参数才算多呢？和所有软件一样，答案是“视情况而定…”。需要考虑使用构造函数的具体环境。

然而，当一个构造函数有 4 个参数时，我就开始问这样一个问题:参数太多了吗？如果已经确定构造函数有太多的参数，解决方法之一是使用构建器模式来构造对象。

# 解决方案:构建器模式

## 如何实现一个

粗略地说，要创建一个构建器类:

*   创建一个新类，可能命名为类似于“{NameOfTheClassToBuild}Builder”的名称，例如“BookDTOBuilder”。
*   将与将要构建的类中存在的成员相同的成员添加到生成器类中
*   对于每个成员，添加一个设置该成员的方法
*   添加一个 build 方法，该方法收集所有成员并使用它们调用构造函数。

所以对于我们的书来说，这个看起来像这样:

```
public BookDTOBuilder { private String id;
  private ZonedDateTime dateCreated;
  private ZonedDateTime dateModified;
  private String authorId;
  private String title;
  private String description;
  private String isbn;
  private String seriesId;
  private String edition;
  private Integer numberOfPages; public BookDTOBuilder withId(String id) { this.id = id;
    return this;  } public BookDTOBuilder withDateCreated(ZonedDateTime dateCreated) { this.dateCreated = dateCreated;
    return this; } public BookDTOBuilder withDateModified(
      ZonedDateTime dateModified) { this.dateModified = dateModified;
    return this; } public BookDTOBuilder withAuthorId(String authorId) { this.authorId = authorId;
    return this; } public BookDTOBuilder withTitle(String title) { this.title = title;
    return this; } public BookDTOBuilder withDescription(String description) { this.description = description;
    return this; } public BookDTOBuilder withISBN(String isbn) { this.isbn = isbn;
    return this; } public BookDTOBuilder withSeriesId(String seriesId) { this.seriesId = seriesId;
    return this; } public BookDTOBuilder withSeriesId(String edition) { this.edition = edition;
    return this; } public BookDTOBuilder withNumberOfPages(Integer numberOfPages) { this.numberOfPages = numberOfPages;
    return this; } public BookDTO build() { return new BookDTO( id,
      dateCreated,
      dateModified,
      authorId,
      title,
      description,
      isbn,
      seriesId,
      edition,
      numberOfPages ); } public static BookDTOBuilder builder() { return new BookDTOBuilder(); }}
```

您可能已经注意到，设置成员的方法正在返回构建器的实例，这是一个很好的功能。它允许将这些方法链接在一起。此外，有一个静态方法来构造构建器，这是一个方便，它不是必需的，我只是觉得它更整洁。

使用这些额外功能，您可以更改原本应该写成的内容:

```
BookDTOBuilder bookDTOBuilder = new BookDTOBuilder();
bookDTOBuilder.withId(id);
bookDTOBuilder.withDateCreated(dateCreated);
BookDTO bookDTO = bookDTOBuilder.build();
```

变成像这样的东西:

```
BookDTO bookDTO = BookDTOBuilder.builder()
  .withId(id)
  .withDateCreated(dateCreated)
  .build();
```

在我看来，这更清楚了。

## 优势

**命名参数非常容易看到**

使用 builder 模式，前面提到的关于从数据库记录构造 BookDTO 的例子变成了这样:

```
Record record = sqlExecutor.fetchOne("SELECT * FROM books WHERE id = ?",
  bookId);
BookDTO book = BookDTOBuilder.builder()
  .withId(record.get(0))
  .withDateCreated(record.get(1))
  .withDateModified(record.get(2))
  .withAuthorId(record.get(3))
  .withTitle(record.get(4))
  .withDescription(record.get(5))
  .withISBN(record.get(6))
  .withSeriesId(record.get(7))
  .withEdition(record.get(8))
  .withNumberOfPages(record.get(9))
  .build();
```

这使得理解哪些参数与哪些构造函数参数相匹配变得容易得多。至关重要的是，这使得出错和引入意外错误变得更加困难。

**构建器上的“with”方法可以以任何顺序调用**

因此，如果开发人员将它们按不同的顺序排列，也没关系。如果他们在 BookDTO 的构造函数上这样做，就会导致数据被赋给错误的变量。可以按以下顺序调用构建器，这不会有什么不同:

```
BookDTO book = BookDTOBuilder.builder()
  .withDescription(record.get(5))
  .withISBN(record.get(6))
  .withSeriesId(record.get(7))
  .withEdition(record.get(8))
  .withNumberOfPages(record.get(9))
  .withId(record.get(0))
  .withDateCreated(record.get(1))
  .withDateModified(record.get(2))
  .withAuthorId(record.get(3))
  .withTitle(record.get(4))
  .build();
```

我建议不要这样做，因为这会使代码更难阅读，因为记录中的参数顺序没有被按顺序调用，但构建器可能会以这种方式调用。

**可以有任意多的空值**

由于构建器将只设置调用相关“with”方法的变量，所以您不必显式地将参数设置为 null。例如，如果我们的构造函数是这样初始化的:

```
BookDTO book = new BookDTO( "072a36ae-0a6f-46cc-bdc9-aa1dcff38fd5",
  ZonedDateTime.now(),
  null,
  "c0315e90-6faa-40ed-a796-7609a545c5a3",
  null,
  null,
  null,
  "45bbce25-4d7d-47ac-84d2-6bc7d4202890",
  null,
  431);
```

我们必须显式地添加所有的空输入，但是使用一个生成器，可以这样做:

```
BookDTO book = BookDTOBuilder.builder()
  .withId("072a36ae-0a6f-46cc-bdc9-aa1dcff38fd5")
  .withDateCreated(ZonedDateTime.now())
  .withAuthorId("c0315e90-6faa-40ed-a796-7609a545c5a3")
  .withSeriesId("45bbce25-4d7d-47ac-84d2-6bc7d4202890")
  .withNumberOfPages(431)
  .build();
```

**可以有更多开发者友好的方法**

当我们创建“with”方法时，我们可以添加覆盖常见用例的变体。例如，如果我们在 BookDTO 中更新了一个版本列表，那么“with”方法可能如下所示:

```
public BookDTOBuilder withEditions(List<String> editions) { this.editions = editions;
  return this;}
```

然而，我们只有一个版本可能是相当常见的，因此，为了帮助开发人员使用这个构建器，可以添加一个新方法以方便使用:

```
public BookDTOBuilder withEdition(String edition) { this.editions = new ArrayList<String>();
  this.editions.add(edition);
  return this;}
```

使用这种方法，复杂对象的构造可以变得更简单。

## 不足之处

**实施建设者更是功不可没**

仅仅是为这个故事的 BookDTO 编写构建器就相当耗时。必须为每个类添加一个这样的组件确实会带来很大的开销。手动编写它们也非常繁琐，因为它们非常公式化。

幸运的是，由于它们公式化的本质，它们变得易于自动化。在 java 中有一个库叫做 [lombok](https://projectlombok.org/) 。它允许通过向类添加注释来为类自动生成构建器。

我不知道 Javascript 有什么等价的东西，在我写这篇文章的时候，一个为 Javascript 创建构建器类的想法正在形成。您也许可以开发自己的类，编写一个接受构造函数和字段名列表的类，返回一个包含每个字段名的“with”方法的类，然后调用构造函数。

**要求读者了解构建器模式**

如果读者以前没有接触过构建器模式，那么这可能会有点令人不安。希望在阅读完这些方法之后，读者能够很容易地理解这个类的目的。

**建筑商需要测试吗？**

构建器是很多代码，一般来说代码需要测试，那么构建器需要测试吗？就我个人而言，我会说不，他们往往是如此公式化，以至于花时间测试他们是不值得的，但一如既往，它取决于。有些人非常严格地要求测试每一行，在这种情况下，是的，构建者应该被测试。

# 摘要

构建器模式是一个有用的工具，可以使复杂/大型的类更容易构建。它们使用于构造类的参数更加清晰易读，并允许添加额外的方法来改善开发人员的体验。

缺点是它们需要更多的时间，而且写起来很乏味。幸运的是，有一些工具/库可以自动生成构建器。

感谢你的阅读，我希望你觉得这个指南有用，我打算在这个系列中增加几个故事，这样对新工程师来说，这是一个真正有用的资源。