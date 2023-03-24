# 如何用 Ruby 给人发工资！

> 原文：<https://levelup.gitconnected.com/how-to-pay-people-in-ruby-108f76519a22>

## 编码问题演练。

![](img/cf912904b667963cca1f5514a293b46f.png)

哦，克莱伯先生，你是一个普通的史高治·麦克老鸭。

像大多数初级开发人员一样，我正在找工作，找工作包括很多代码挑战。现在，技术上来说， [#do stuff here final # the last line of a Ruby method is what is returned. endend](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[好了，更进步了！我们现在有了一个*结构*，它包含了函数接受和返回的内容。现在让我们想想我们在做什么。嗯，我知道我要在一个对象或散列数组上循环来检查每一个，所以在 Ruby 中有一种方法可以用*来完成。每个*语法。](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

```
module PayPeople def self.group_payments(pay_array, max_group_size=100)    final = [[]] # an array of arrays pay_array.each do |payment|# do something with the payment here end final # the last line of a Ruby method is what is returned. endend
```

[好吧，酷，所以我们现在可以访问每笔付款。但是我们需要查看用户/电子邮件是否已经在支付数组中，所以我们需要第二个循环！在这个例子中，我们必须检查最终数组中的每个*数组，看看用户是否已经在其中。这次我将使用一个 *for/in* 循环:*](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

```
module PayPeople def self.group_payments(pay_array, max_group_size=100) final = [[]] # an array of arrays pay_array.each do |payment| for arr in final do 
        # for each array in our final array do something end    end final # the last line of a Ruby method is what is returned. endend
```

[好了，现在我们越来越接近了，我们的代码越来越乱。我们将需要一个 *if/else 语句*来查看付款是否已经在我们最终数组中的数组组中，但是我没有在那里写大部分逻辑，而是决定写第二个 *helper 方法*来使这变得更容易，如果您的代码在受压的情况下变得混乱，我推荐这种技术。](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

```
module PayPeople def self.group_payments(pay_array, max_group_size=100) final = [[]] # an array of arrays pay_array.each do |payment| for arr in final do 
        # for each array in our final array do something end end final # the last line of a Ruby method is what is returned. end # new stuff def self.isEmailThere?(array, email) # let's check for an email! endend
```

[酷，所以我们现在有第二个方法要用，在这里我将使用一些奇特的语法和 *include？*法。请注意，我的助手函数有一个“？”因为按照 Ruby 的惯例，像我们这样返回布尔值的函数通常会以“？”结尾](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

```
def self.isEmailThere?(array, email) array.map{|payment| payment[:email]}.include?(email)end
```

[这表示对于每个数组，对于数组中的每笔支付，我们将检查电子邮件是否包含我们传入的电子邮件。我们通过将我们的支付数组改为电子邮件数组，然后查看我们的电子邮件是否在其中来做到这一点。](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[我们快完成了！](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[现在，我们必须考虑的唯一事情是最大组大小以及我们将要做的事情，即*将*推入哪个阵列。最终的代码会是这样的！](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

```
module PayPeople def self.group_payments(pay_array, max_group_size=100) final = [[]] # an array of arrays pay_array.each do |payment| for arr in final do        if !PayPeople.isEmailThere?(arr, payment[:email]) && arr.length < max_group_size arr.push(payout) break else final.push([payment]) break end end end final    end def self.isEmailThere?(array, email) array.map{|payment| payment[:email]}.include?(email) endend
```

[有很多“如果”和“结束”的话，但基本上是说，如果`final`中的数组不包括当前的电子邮件，并且足够小，那么让我们将支付信息推入其中，否则，将它推入一个新的数组中！](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[一个示例输入可能看起来像:](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

```
example = [{ email: "bob@example.com", amount: 400 },{ email: "susan@example.com", amount: 8000 },{ email: "bob@example.com", amount: 400 },{ email: "alan@example.com", amount: 300 },{ email: "alan@example.com", amount: 12 },{ email: "dana@example.com", amount: 675 }]
```

[示例输出可能如下所示:](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

```
[[{:email=>"bob@example.com", :amount=>400}, {:email=>"susan@example.com", :amount=>8000}, {:email=>"alan@example.com", :amount=>300}, {:email=>"dana@example.com", :amount=>675}], [{:email=>"bob@example.com", :amount=>400}], [{:email=>"alan@example.com", :amount=>12}]]
```

[因为我们有两个“Bob”和两个“Alan ”,所以它们被放入两个不同的数组中。成功！](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[从时间角度来看，这不是一个非常好的解决方案。我通过放入 *break* 语句对其进行了一些改进，这样一旦我们的插入完成，我们就可以停止对每笔支付的循环，但这仍然是指数时间的大 O 倍 这意味着它不是这个问题的“快速”解决方案，因为随着输入大小变大，它将变得越来越复杂。](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[如果有人有更快/更酷的解决方案，请在评论中告诉我；)](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[说到快和酷，我想我在展望公园遇到的游侠两者都是。](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[![](img/146e571071cf6a53eee9ea794ff5e5dc.png)](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[游侠！](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[当然，从这张照片上你不会这么做，但是护林员是一个真正的公园护林员，所以他知道什么时候该坐下来等待。](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[他有速度和纪律来解决困难的编码问题，或者至少，拥抱我的心。](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[现在就白了！](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[网络信息中心(Network Information Center)ˌ网路界面卡(Network Interface Card)ˌ全国工业理事会(National Industrial Council)ˌ航行情报中心(Navigation Information Center)](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)

[](/hourglass-sums-in-two-dimensional-arrays-the-stonecutters-handshake-of-programming-87b6bebc3bad# an array of arrays</span><span id=)