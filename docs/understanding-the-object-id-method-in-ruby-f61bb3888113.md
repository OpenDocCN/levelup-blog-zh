# 理解 Ruby 中的 Object_id 方法

> 原文：<https://levelup.gitconnected.com/understanding-the-object-id-method-in-ruby-f61bb3888113>

![](img/0e99b1511d074be42b009c8cd1614d48.png)

照片由[马里·梅德尔](https://www.pexels.com/@mali?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[派克斯](https://www.pexels.com/photo/short-fur-white-and-black-cat-225406/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

Ruby 和许多编程语言一样可以创建对象，我们可以用这些对象来存储属性、方法并对它们执行操作。在 Ruby 中，一切都是对象、字符串、整数、布尔、散列、数组和类的实例。

每个对象都有一堆我们可以使用的公共方法。在本文中，我们将主要关注一种方法— ***对象 Id。***

对于每个对象，Ruby 都提供了一个名为 ***object_id 的方法。*** 你猜对了，这代表了特定对象的一个随机 id。这个值是对象存储在内存中的地址的参考。

每个对象都有一个唯一的对象 id，该 id 在对象的整个生命周期中不会改变。

让我们看一些 Ruby 中数据类型的例子:

```
name = "Paul"
name2 = "Paul"
name.object_id == name2.object_id # false
name2 = name
name.object_id == name2.object_id # true
```

在这个例子中，我们声明了两个 string 类型的变量，并赋予了相同的值。然后我们比较每个变量的 ***object_id*** 看它们是否相同，我们得到 false。

这样做的原因是因为每个变量在声明的时候都在内存中获得不同的位置来存储它们的值，所以即使我们分配了相同的字符串，它们在内存中也不共享相同的地址位置，因此它们的对象 id 是不同的。

但是，当我们将 ***name*** 的值赋给变量 ***name2*** 并再次比较它们的 id 时，会发生什么呢？

这次发生的情况是，变量 ***name*** 与变量 ***name2*** 在内存中共享同一个地址。因此，当我们重新分配变量 ***name2*** 时，我们并没有在内存中创建新的地址，而是共享了 ***name*** 拥有的相同地址。

因为这个原因，我们在第二个比较中得到真实。

正如我提到的，ruby 中的所有对象都有一个 *object_id* 。

```
num = 23
num.object_id # 47
users = {"carl": [1,2,3], "paul": [3,4,5]}
users.object_id # 70342844770580
numbers = (0..10).to_a
numbers.object_id  # 70342856985540
person = Person.new("person1")
person.object_id  # 70342819709960
```

如果你试验并声明布尔、整数或 nil 变量，你会注意到它们在 over 上得到相同的 ***object_id*** 。你可以在这里阅读更多关于为什么会发生这种情况[。](https://stackoverflow.com/questions/3430280/how-does-object-id-assignment-work)

我想你明白了，这个方法只是给你的对象一个 id。但是我们如何使用它，让它更有用呢？。坚持住！！我们会找到答案的。

让我们看看下面的例子。

在上面的例子中，我们有一个用户类，它在 initialize 函数中接受名称和颜色数组。

现在，在这种情况下，让我们说颜色数组将只在类的范围内可用，不公开，所以我们可以使用一个类变量来存储颜色。

我们初始化一个类变量等于一个空散列。然后在初始化函数中，我们使用 ***object_id*** 作为关键字，并将颜色数组存储为符号。

由于类变量将保存一个对所有用这个类创建的实例的引用，我们可以确保我们在类中使用 ***object_id*** 存储的数据不会被 GC(垃圾收集)。

然后 color_mapping 方法只取我们设置给 hash 中的 ***object_id*** 键的值，并给每种颜色分配一个随机数。

# 结论

这基本上就是 Ruby 中的 ***object_id*** 方法，一个对象的随机标识符。如果你对如何使用这种方法有更多的建议，请在下面留下评论。

感谢您的阅读！

你可能会觉得这很有趣。

[](/postgresql-views-with-ruby-on-rails-78260cd6f021) [## 使用 Ruby on Rails 的 PostgreSQL 视图

### 利用视图的力量封装复杂的查询。

levelup.gitconnected.com](/postgresql-views-with-ruby-on-rails-78260cd6f021) [](https://medium.com/swlh/what-are-solid-principles-in-software-development-world-a5ec98637c01) [## 在软件开发世界中，什么是坚实的原则？

### 用 Ruby 编程语言实现

medium.com](https://medium.com/swlh/what-are-solid-principles-in-software-development-world-a5ec98637c01) [](https://medium.com/swlh/deploy-rails-app-to-digital-ocean-f1aeb0991470) [## 将 Rails 应用程序部署到数字海洋

### 铁路，数字海洋，市场

medium.com](https://medium.com/swlh/deploy-rails-app-to-digital-ocean-f1aeb0991470)