# 用 ORM 使 NodeJS 中的 DynamoDB 访问变得容易

> 原文：<https://levelup.gitconnected.com/making-dynamodb-access-easy-in-nodejs-with-orm-4cc32418f7a>

## 让我们学习如何使用电动糖

![](img/99a080cbf95339b045fb1751b6852efb.png)

照片由 [RODNAE Productions](https://www.pexels.com/@rodnae-prod?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 从 [Pexels](https://www.pexels.com/photo/a-home-key-over-a-carton-box-8292772/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

目前，我们正在使用`aws-sdk`与 **DynamoDB** 进行交互。

由于 DynamoDB 是 NoSQL 键值存储，所以在团队中工作时会出现很多问题。在我们如何与数据库交互的语法中有很多错误。

我们需要一致的做事方式。今天，我们将看到我们是如何通过一个名为。`**Dynamoose**`

# 当前方法的问题

老实说，即使在使用 dynamodb 一段时间后，我仍然很难找到它的窍门。尤其是一些我们必须用来更新记录的特殊属性。

`**ExpressionAttributeNames**`和`**ExpressionAttributeValues**`本身就是另一个需要理解和处理的痛苦。

你猜怎么着？它们甚至不是类型安全的！

让我们举下面的例子。这段代码来自一个实际项目，我们在这个项目中更新了一个名为`member`的模型。细节并不重要。看看语法就知道了！

这段代码有几个问题。

*   `UpdateExpression`和`ConditionExpression`只是一个普通的字符串。因此，如果我们错过了一个字符，我们就编写了一个错误的 DB 查询，这将是一场噩梦。
*   最重要的是，我们没有任何类型的`validation`用于我们推入数据库的模型。
*   此外，我们没有任何自动完成功能
*   API 很难理解

# 有什么解决办法？

有多个库可以解决这个确切的问题(因为我们不是第一个感受到这种痛苦的人)，经过一番挖掘，我认为最好的库是`[dynamoose](https://dynamoosejs.com/getting_started/Introduction)`。

## 为什么 Dynamoose 是一个很好的选择？

*   类型脚本支持
*   验证支持
*   自动对象转换
*   非常直观的 API(使用熟悉的词语，如`get`、`put`等)

这个库遵循非常流行的`mongoose`库的 API 风格(用于 MongoDB)。也许你已经从名字中猜到了:P

让我们重新编写我们的查询，找出如何提高我们代码的质量。

# 步骤 1:首先，安装依赖项

让我们先安装依赖项

```
npm i dynamoose
```

# 步骤 2:创建一个模型类

让我们为将存在于 dynamoDB 中的数据创建一个模型。这是可选的。但是这样做将产生打字错误，这将使您免受不必要的打字错误。

模型. ts

很简单，对吧？

# 步骤 3:创建模式

模式是指模型的数据库规范。

在这里，我们可以为各个字段指定各种属性。它们稍后将用于验证，并确保没有脏数据进入数据库。

其中一些是…

`required` →是否需要该属性

`default` →属性的默认数据库值

`validate` →验证字段

`getter`和`setter` →我们要如何取回一个字段的值

`timestamp` →自动`CreatedAt`和`UpdatedAt`DB 型号的值(我们经常使用)

所以在我们的例子中,`ExampleSchema`将是

注意在模式的底部，我们已经指定了`timestamps`属性。这会自动为我们生成`timestamp`。

# 步骤 4:创建存储库

该库遵循 MongoDB 命名，因此它将其存储库标识为模型。我们将使用该模型，但以更聪明的方式。

我们将创建一个存储库来分离所有的数据库逻辑。以下是如何为`GET`、`CREATE`和`UPDATE`操作创建函数的示例

所以我们所有的数据库逻辑现在都被封装了。并且还要看一下`updateExample`功能。就像其他功能一样。

您只需提供主键和排序键以及想要更新的字段。你完了！

***如果你试图传递任何你的模型中没有定义的未知键，你会在编译时得到一个类型错误！***

# 最后一步:在代码中使用它

现在我们将使用这个存储库与数据库进行交互

应用程序

所以现在我们有了一个 CRUD 应用程序，我们可以利用`dynamoose`来消除数据库查询的痛苦。

今天到此为止。祝您愉快！:D

```
**Get in touch with me via** [**LinkedIn**](https://www.linkedin.com/in/56faisal/) **or my** [**Personal Website**](https://www.mohammadfaisal.dev/)**.**
```