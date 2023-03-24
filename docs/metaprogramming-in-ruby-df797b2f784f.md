# Ruby 中的元编程

> 原文：<https://levelup.gitconnected.com/metaprogramming-in-ruby-df797b2f784f>

Ruby 令人印象深刻的动态特性赋予了您在运行时定义方法和类的自由，这就是所谓的元编程。通过使用 Ruby 进行元编程，您可以在运行时询问自己的代码问题，这使得您可以在用另一种语言完成相同任务所需时间的一小部分内完成任务。

![](img/3cd8d370dfe6100b29661d7b6d2e8686.png)

红宝石，[https://morioh.com/p/0116c0dcd0fe](https://morioh.com/p/0116c0dcd0fe)

# 什么是元编程？

根据维基百科:

> 元编程是编写计算机程序，这些程序将其他程序(或它们自己)作为它们的数据来编写或操作，或者在编译时完成原本要在运行时完成的部分工作。在许多情况下，这允许程序员在与他们手动编写所有代码相同的时间内完成更多的工作，或者它给程序更大的灵活性，以有效地处理新的情况，而无需重新编译。或者，更简单地说:元编程是编写在运行时编写代码的**代码，以使您的生活更加轻松。**

在本文中，我们将会看到一些不同的 Ruby 元编程的例子，特别是在使用 Sinatra 和 Rails 框架时。

# Rails 中的元编程

![](img/76118e7becd6e3f0015abc7bf53cdc2a.png)

Ruby on Rails，[https://en.wikipedia.org/wiki/Ruby_on_Rails](https://en.wikipedia.org/wiki/Ruby_on_Rails)

让我们从 [Rails 框架](https://rubyonrails.org/)中一个非常基本的例子开始。假设您有一个针对`users`的数据库表，其中包含针对`name`和`email`的列。Rails 不知道数据库中每个`user`都有哪些列，但是下面的查询工作得很好。

```
**User**.find_by_email('harry@potter.com')
# => <user id: 123, email: 'harry@potter.com', name: 'Harry Potter'>
```

我们从未定义过`find_by_email`方法，但它对我们来说就像预期的那样有效。这是因为 Rails 框架基于数据库中的列名动态创建了一个新方法。这是元编程的一个简单例子，因为该方法从未被定义，而是在运行时生成。

在研究 Ruby 的元编程时，您可能会遇到一个常见的术语，即 monkey patch。monkey 补丁是程序在面向对象语言中本地扩展或修改系统类的一种方式。

```
[1, 2, 3].to_s
=> "[1, 2, 3, 4]" **class** Array  
  **def** to_s    
    '[]'  
  **end**
**end**[1, 2, 3].to_s
=> "[]"
```

上面的例子是一个基本的方法，它展示了我们可以进入任何类，甚至是像 Array 这样的核心类，并且动态地修改它。

# Sinatra 中的元编程

![](img/7d9a2c3a104722c54817c09abb7ee9c4.png)

鲁比·辛纳特拉，[http://woodiwiss.me/the-awesome-of-sinatra/](http://woodiwiss.me/the-awesome-of-sinatra/)

[Sinatra](http://sinatrarb.com/) 框架有一个`Sinatra::Delegator`模块，用于将方法调用委托或分配给指定的`target`。目标默认设置为`Sinatra::Application`。

在下面的例子中，我们首先为`Module` 类定义了一个名为`delegar`的新方法，这样我们就可以说，当你调用某个方法时，你是在不同的对象上调用该方法，而不是在使用它的当前对象上。这是在`Sinatra::Delegator`中定义的`delegate`类方法的简化版本。

```
**class Module**
  **def delegar**(method, to:)
    define_method(method) do |*args, &block|
      send(to).send(method, *args, &block)
 **end
  end
end**
```

当这个方法被调用时，它将定义一个新方法，该方法的工作是将工作委托给另一个对象。

```
**class Receptionist**
  **def phone**(name)
    puts "Hello #{name}, I've answered your call."
 **end
end****class Company**
  attr_reader :receptionist
  delegar :phone, to: :receptionist  **def initialize**
    @receptionist = Receptionist.new
 **end
end**company = Company.new
company.phone 'Quinton'
# => "Hello Quinton, I've answered your call."
```

在上面的例子中，我们调用了`Company` 类上的`phone`方法，但是实际上是`Receptionist`类处理调用，因为我们已经将`phone`调用委托给了`Receptionist` *。*

让我们来看另一个例子，看看如何动态定义一个缺失的方法。当你在一个对象上调用一个方法时，Ruby 进入这个类并试图找到这个方法，如果找不到，它继续沿着祖先的链向上搜索。如果仍未找到该方法，则调用`method_missing`方法。如果发生这种情况，我们可以使用`define_method`动态创建一个新方法。这与使用通常的`def`来创建方法没有太大的不同，但是它允许我们保持代码干燥。

```
**class** **Developer**
  **def** **method_missing** **method**, ***args**, **&block**
    **return** **super** method, *args, &block 
           **unless** method.to_s =~ /^newApp_\w+/
    **self**.**class**.send(:define_method, method) **do**
      p "writing " + method.to_s.gsub(/^newApp_/, '').to_s
    **end**
    **self**.send method, *args, &block
  **end**

**end**

developer = Developer.new

developer.newApp_frontend 
# => "writing newApp_frontend"
developer.newApp_backend
# => "writing newApp_backend"
developer.newApp_debug
# => "writing newApp_debug"
```

在上面的代码中，调用一个不存在的方法会触发`method_missing`。在这种情况下，我们只想在方法名以`"newApp_"`开头时创建一个新方法。通过使用这段代码，我们可以在`define_method`的帮助下从`"newApp_"`开始创建数以千计的新方法。

本文只是触及了 Ruby 元编程的皮毛，它是一项非常强大的技术，但只有在正确和谨慎使用的情况下。它可以帮助你保持代码[干燥](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)并且有助于调试，但是如果过度使用也会增加混乱。希望这篇元编程介绍已经激发了您学习更多相关知识的好奇心！

[](https://www.toptal.com/ruby/ruby-metaprogramming-cooler-than-it-sounds) [## Ruby 元编程比听起来更酷

### Ruby 元编程是 Ruby 最有趣的一个方面，它使编程语言能够实现一个更好的编程环境。

www.toptal.com](https://www.toptal.com/ruby/ruby-metaprogramming-cooler-than-it-sounds) [](https://blog.codeship.com/metaprogramming-in-ruby/) [## Ruby 中的元编程——via @ codeship

### 在本文中，我们将探讨 Ruby 元编程的几个不同方面。首先，什么是…

blog.codeship.com](https://blog.codeship.com/metaprogramming-in-ruby/) [](http://rubylearning.com/blog/2010/11/23/dont-know-metaprogramming-in-ruby/) [## 不知道 Ruby 的元编程？

### 这篇客座博文由加文·莫里斯(Gavin Morrice)撰写，他是位于爱丁堡的软件精品店 Katana Code Ltd .的董事总经理

rubylearning.com](http://rubylearning.com/blog/2010/11/23/dont-know-metaprogramming-in-ruby/) [](https://www.rubyguides.com/2016/04/metaprogramming-in-the-wild/) [## Ruby 元编程:真实世界的例子

### 你可能以前读过 Ruby 元编程。但是...如果你没有几个具体的…

www.rubyguides.com](https://www.rubyguides.com/2016/04/metaprogramming-in-the-wild/) [](https://en.wikipedia.org/wiki/Metaprogramming) [## 元编程

### 元编程是一种编程技术，在这种技术中，计算机程序有能力将其他程序视为自己的程序

en.wikipedia.org](https://en.wikipedia.org/wiki/Metaprogramming) [](https://www.crondose.com/2016/08/examples-metaprogramming-guide-beginners/) [## 元编程示例——初学者指南

### 今天我将介绍元编程的例子。等等，别跑！元编程是更多…

www.crondose.com](https://www.crondose.com/2016/08/examples-metaprogramming-guide-beginners/)