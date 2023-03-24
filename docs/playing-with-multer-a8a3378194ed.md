# 与 Multer 一起玩

> 原文：<https://levelup.gitconnected.com/playing-with-multer-a8a3378194ed>

## 使 Node.js 中的图像上传更简单的技巧

![](img/917e9a2c5696d5ab7a2c78626255d91c.png)

来源:[Unsplash.com](https://unsplash.com/photos/m_HRfLhgABo?utm_source=unsplash&utm_medium=referral&utm_content=creditShareLink)

当 web 客户端在服务器上上传一个文件(比如一张图片或者一个文档)时，通常是通过一个表单，它被编码为`multipart/form-data`。因此，multer 是一个与 Express.js 和 Node.js 一起使用的中间件，使这些文件的处理更加容易。

在深入研究如何定制 multer 之前，让我们先看看它的基础知识。对于这里的例子，让我们假设一个学生的图像必须被上传，并且图像必须被保存在后端的文件夹`assets/images`中。**首先，至关重要的是，在前端客户端的表单中，** `**enctype=multipart/form-data**` **被设置。**

乘法器在磁盘存储中提供了两个参数:

1.  `destination`，目标文件夹
2.  `filename`，这是我们想要保存文件的名字。

对于上面的例子，这可以如下进行

在这里，我们对文件进行检查，以确保它是一个图像，然后我们给它一个随机的名称，同时将它存储在后端。您也可以选择不更改名称，在这种情况下，文件将与用户使用的名称相同。

现在，让我们看看如何定制它。

## 如果要根据某些条件将文件存储在不同的目的地呢？

假设 web 客户端允许教师和学生上传他们的个人资料图像，他们的图像分别存储在文件夹`assets/images/teachers` 和`assets/images/students` 中。所以，在设置文件的目的地之前，重要的是我们要弄清楚这是老师的形象还是学生的形象。

一种方法是在客户端设置的`formData` 中定义一个布尔变量。但是由于 multer 是一个中间件，文件通常出现在任何 formData 的顶部，所以表单中的其他参数在这里是不可访问的。为此，需要做一个小的改变，那就是在 formData 的末尾添加文件。这样，检查这是老师还是学生的图像变得更容易，因此可以将其保存在适当的文件夹中。

所以设布尔变量为`isStudent`。

我们的表单数据包含学生/教师注册时的基本数据，看起来像这样

如您所见，`“image”`被附加在 `formData`的末尾。

现在，multer 上传功能将修改如下-

## 如果您想使用从数据库中检索的值来重命名文件，该怎么办？

比方说，我不想给这些图像起一个随机的名字，而是想给它们起一些有意义的名字，以确保我以后可以检索这些图像。继续我们的例子，让我们假设图像必须被命名为——学生的`“RollNo.jpg”`和教师的`“StaffNo.jpg”`。在这里，`RollNo`和`StaffNo`将不得不从数据库中检索，给定这些人的唯一`IDValues`。

因此，解决这个问题的一种方法是在 multer 存储器的`filename` 参数中命名该文件，就像我们到目前为止所做的那样，然后在用于该路径的控制器函数中对其进行重命名。仅在控制器函数中重命名的原因是，只能在控制器函数中从数据库中检索值。

因此，我们的 multer 上传功能将与上述功能相同。

让控制器函数被称为`setProfileImage().`

控制器函数将以这种方式进行重命名-

在这里，我根据是学生还是教师来检索员工编号，然后重命名该文件。这里使用的数据库是 Mongodb，但这可以用任何数据库来完成。

另一种不用重命名就能保存图像的方法是在 Mongoose 对象中添加一个名为 `imagePath`的新字段，它将存储我们给定的随机文件名。

还有更多的方法可以定制 multer，比如它可以处理多个文件上传，不像上面例子中的单个文件上传。更多关于 multer 的信息可以在[这里](https://github.com/expressjs/multer)找到。

## 就是这样！

我希望这对你使用 multer 上传文件和以不同的方式保存文件有用。

## 资源-

[https://github.com/expressjs/multer#readme](https://github.com/expressjs/multer#readme)