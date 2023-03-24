# PHP 表单基础指南

> 原文：<https://levelup.gitconnected.com/a-basic-guide-to-php-forms-7461e5217055>

![](img/728ccfdff205190712605f59d363ca66.png)

用 PHP 创建的表单是结合 HTML 文档制作的。表单本身不是用 PHP 做的，是用 HTML 做的。PHP 单独用于获取、使用和操作表单中的信息。

首先，我们使用 HTML 表单标签。在开始的表单标签中，必须包含一个动作属性。这个属性的值将是 PHP 代码的位置。如果 HTML 和 PHP 文件在同一个目录中，这将只是 PHP 文件的名称。如果 PHP 代码与这个 HTML 表单在同一个文件中，action 值可以留空。在开始的表单标记中，还需要有一个方法属性，它表示表单将用来完成什么样的动作。

```
<form action = "" method = "post">
   <input type = "submit">
</form>
```

该方法的两个可能值是`get`和`post`。`get`方法的独特之处在于它在提交表单的 URL 中显示用户的输入。更改 URL 中的输入值会更改网页中显示的输入值。虽然 get 和 post 都可以用来在表单中提交信息，但是建议使用`post`方法，因为它可以使信息更加安全，并且不允许信息被轻易更改，这减少了对用户提交的信息进行不必要的更改的机会。

如果我们想制作一个关于宠物的表格，它可以是这样的。

```
<form action="" method="post">
   Name: <input type="text" name="name">
   <br>
   Species: <input type="text" name="species">
   <br>
   Age: <input type="number" name="age">
   <br>
   <input type="submit">
</form>
```

这个表单有两个文本输入，一个是宠物的名字，一个是宠物的种类，还有一个数字输入是宠物的年龄。

在表单标签之间，可以使用任何常规的 HTML 表单输入。任何输入都必须有一个 name 属性，这一点很重要，因为 PHP 将使用这个属性来检索用户提交的信息。

可以使用以下格式检索信息。下面，name 变量使用表单中的方法(必须大写)和表单输入的名称进行赋值。

```
$variableName = $_METHOD["formInputName"];
```

我们可以使用下面的代码列出从表单中获得的信息。

```
Pet name: <?php echo $_POST["name"]?>
<br>
Pet species: <?php echo $_POST["species"]?>
<br>
Pet age: <?php echo $_POST["age"]?>
```

总之，这段代码为我们提供了一个简单的宠物表单，让用户输入宠物的名字、年龄和种类。提交后，表单显示宠物的所有信息，如下所示。

```
Pet name: Baby
Pet species: Dog
Pet age: 7
```