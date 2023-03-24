# 地形:保持干燥+功能切换

> 原文：<https://levelup.gitconnected.com/terraform-staying-dry-feature-toggling-f75cf5660406>

![](img/db5d49ba3b6ebb21db60387b03d4aa67.png)

照片由[乔·马涅](https://unsplash.com/@joemania?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

所有代码都有生命周期。今天的好代码明天可能就是坏代码。这并不是说在代码最初实现时做出了错误的决定，而是有时您根本无法预见您的代码将被要求做什么和支持什么。正因为如此，我经常尝试预先建立良好的模式，希望这些模式能够扩展并为未来的问题提供解决方案。然而，即使仔细考虑，一个常量仍然存在——新代码将被需要，旧代码将被淘汰。

根据我的经验，处理这个常量的最简单的方法是将您的代码分割成小的/可重用的片段，这些片段可以根据需要方便地管理和交换。

如果你还没有读过，我强烈建议你读一下 [Terraform:使用计数条件时保持干燥](https://medium.com/dev-genius/terraform-staying-dry-when-using-count-conditionals-2c4f6c15a4ac)。虽然那篇文章和这篇文章讨论的是同一个主题，但是它们针对不同的问题提供了不同的方法/解决方案。在第一篇文章中，如果你不熟悉“保持干燥”是什么意思，我们也会谈到。

我们将在这里讨论的方法不仅允许您保持干燥，而且可以轻松地重用/实例化您的代码，并根据需要打开/关闭特性。您可以以最少的复杂性或文档要求获得所有这些

我曾经是在自动化中使用布尔的粉丝。在某种程度上，我仍然认为布尔有价值，但是有一天我的一个同事告诉我:“布尔只告诉你一条信息，为什么不让它更有帮助呢”。这相当于问某人“你想吃冰淇淋吗？”。如果他们说是，你将不得不立即跟进另一个问题“你想要什么口味的？”。相反，你可以直接问“你想要什么口味的冰淇淋？”同时得到两个问题的答案。

使用 Terraform 中的对象地图，您可以做到这一点。这是它的样子:

```
variable "ice_cream_order" {
  type = map(object({
    flavor       = string
    bowl_or_cone = string
    size         = string
    toppings     = list(string)}))
}
```

现在，不用我说别的，你已经知道点冰淇淋需要什么了。创建的对象定义了预先需要的每一个属性。

我有点言过其实了，但是我想强调一些事情。我们可以仅使用一个对象来完成上述结果。然而，使用对象映射给我们带来了额外的好处，我们可以根据需要多次调用这个代码，而不需要任何其他东西。这是我们点冰淇淋时的样子。

```
ice_cream_order = {
  kens = {
    flavor       = chocolate
    bowl_or_cone = cone
    size         = medium
    toppings     = ["whipped cream"]
  }
  billys = {
    flavor       = vanilla
    bowl_or_cone = bowl
    size         = small
    toppings     = ["chocolate chips", "M&Ms"]
  }
  sarahs = {
    flavor       = strawberry
    bowl_or_cone = cone
    size         = large
    toppings     = ["strawberries", "whipped cream"]
  }}
```

为了简单起见，我将放弃冰淇淋的类比，假设我们正在配置服务器。我们新的天体图是:

```
variable "server_fleet" {
  type = map(object({
    image_name      = string
    server_hostname = string
    instance_type   = string
    ssh_key_name    = string }))
}
```

使用上面的对象映射，我们的声明如下所示:

```
server_fleet = {
  bastion = {
    image_name      = "Ubuntu"
    server_hostname = "bastion"
    instance_type   = t2.small
    ssh_key_name    = kens-ssh-key
  }
  web = {
    image_name      = "Ubuntu"
    server_hostname = "web-server"
    instance_type   = t3.large
    ssh_key_name    = kens-ssh-key
  }
  db01= {
    image_name      = "Centos"
    server_hostname = "mysql-db01-server"
    instance_type   = t3.4xlarge
    ssh_key_name    = kens-ssh-key
  }
  db02= {
    image_name      = "Centos"
    server_hostname = "mysql-db02-server"
    instance_type   = t3.4xlarge
    ssh_key_name    = kens-ssh-key
  }
}
```

现在，当我们定义创建服务器所需的资源时，我们将使用`for_each`遍历每个对象。

```
resource "aws_security_group" "random_server" {
  for_each = var.server_fleet name        = "${each.value.server_hostname}-security-group"
  description = "Defines access to the ${each.value.server_hostname} server"
  // Truncated}resource "aws_eip" "random_server" {
  for_each = var.server_fleet instance = aws_instance.random_server[each.key].id
  vpc      = true}resource "aws_instance" "random_server" {
  for_each = var.server_fleet ami           = each.value.image_name
  instance_type = each.value.instance_type
  key_name      = each.value.ssh_key_name tags = {
    Name = each.value.server_hostname
  } // Truncated}
```

注意:我们使用`each.value.PROPERTY`引用对象内部的值。

这里将发生的是，对于地图中的每个对象，上述所有资源都将被实例化。您将最终拥有四台服务器，每台服务器都有一个安全组和一个 EIP(公共 IP 地址)。这里的好处很多。仅举几个例子:

*   您的代码是配置驱动的
*   乍一看，代码很容易理解
*   你的代码是枯燥的，因为你不必重复自己。
*   根据您定义的对象，您可以打开/关闭代码的不同部分。(我们将在下面更深入地讨论这个问题)。

举了上面的例子后，我想花一点时间来解决这个问题。使用 terraform 模块可以很容易地完成上面的特定示例。Terraform 模块就其定义而言，允许您对代码进行分组，并根据需要多次实例化它。然而，以上只是演示该方法的一个示例。使用对象是对模块的补充，它们并不互相排斥。

以上内容的真正美妙之处在于，您可以在更细粒度的级别上获得与模块相同的好处，并且还可以切换特性。

这种方法最擅长的一个例子是，您有一个需要新基础设施资源的新服务。您可以根据是否定义了先决条件对象，在每个环境中切换这些资源。

另一个例子是，只有特定的环境需要特定的资源(例如 WAF)。最后一个例子是，每个环境需要不同数量的对等连接。

Terraform 有多种方法可以解决上述问题，但这是唯一一种 100%由配置驱动的方法。您的所有环境都可以使用完全相同的地形，同时干净地适应差异。您还可以通过比较不同环境的配置，轻松看出它们之间的差异。这大大减少了环境漂移。

如何处理异常决定了代码的伸缩性。

作为最后一点，您可以将上述方法用作转换状态的一部分。使用前面的例子:

```
you have a new service that requires new infrastructure resources. You can toggle these resources per environment based on whether the pre-requisite object is defined.
```

一旦新服务被部署到所有环境中，您就可以移除这种切换，这样您的代码/配置就更清晰了。

同样的方法也适用于贬低资源的情况。向相关代码添加一个开关，并开始一次关闭一个环境的资源。一旦从所有环境中移除了资源，您就可以移除切换并一起删除代码。

为了获得无限的故事，你还可以考虑注册[](https://blog.rhel.solutions/membership)**成为中等会员，只需 5 美元。如果您使用* [*我的链接*](https://blog.rhel.solutions/membership) *注册，我会收到一小笔佣金(无需您额外付费)。**