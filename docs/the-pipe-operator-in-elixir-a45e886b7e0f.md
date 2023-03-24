# 《长生不老药》中的管道操作员

> 原文：<https://levelup.gitconnected.com/the-pipe-operator-in-elixir-a45e886b7e0f>

## 就像马里奥:进入管道

我最近的校外编程之旅带我去了一些有价值的地方，包括[def hello doIO.puts("Hello World") end# no import needed, IO just works like the native puts method in Ruby.# now let's return "Hello World" as the end of our functiondef ret_hello do"Hello World"end](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[就像 Ruby 一样，Elixir 也有“隐式返回”，即无论最后一个语句或变量的返回值是什么，都将是函数的返回值。这是超级“干净”的，因为不必记住在哪里返回你的值，只需写你需要的。](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[这也可以通过参数来实现。](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

```
def add(x,y) dox + yend# returns the sum
```

[不需要保存变量，在 JavaScript 中也不需要保存变量，但是通常习惯于干净地返回一个变量而不是一个表达式。](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[但是多功能呢？在 Ruby 或 JavaScript 中，它们可能看起来像这样。](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

```
# the above exampledef add(x,y) dox + yend# new functiondef multiply_by_two(number) donumber * 2end
```

[现在通常，为了“链接”这些函数或者将一个函数传递给另一个函数，我们必须使用一些变量。](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

```
# the ruby way, specific but more codedef add_then_multiple_by_two(x,y) dosum = add(x,y)double = multiply_by_two(sum)end//the JS way, less code but confusing/messyconst addThenMultiplyByTwo = (x, y) => { return multiplyByTwo(sum(x,y))

}
```

[这两种方式都不可怕，但如上所述，Elixir 是一种函数式编程语言，这意味着它通常不使用全局变量或“状态”来监视变量并将它们发送给不同的函数。正因为如此，它有一个叫做**的管道操作符**，这是一种语法上很好的链接方式。](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[![](img/30a7e0f10908ff81707a4c12be06816c.png)](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[这个烟斗认为它赢得了年度烟斗奖，但实际上是长生不老药赢了，SMDH。](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

```
# The Elixir way!def add_then_multiply_by_two(x,y) doadd(x,y)
|> multiply_by_twoend# add_then_multiply_by_two(2,3) returns 10
```

[这个小管道或“| >”**获取管道传递给它的函数的返回值，并将其作为第一个参数隐式地传递给下一个函数。**](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[您可以使用任意多的函数来实现这一点，而且 Elixir 通常会在其模块的“main”方法中实现这一点。你甚至可以从其他模块“导入”或“别名”函数，就像在 Ruby/TypeScript/Python 或 ES6 JS 中一样，以便在你的其他代码中使用。那你也可以把它们锁起来。](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[您也可以稍后添加更多参数！](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

```
# adding a new methoddef multiply_by_anything(x,y) dox * yend#chaining all the ones already!def main(x,y,z) dosum(x,y)
|> multiply_by_two
|> multiply_by_anything(z)end# main(2,3,4) returns 5, then 5 *2 = 10, then 10 *4 = 40!
```

[尽管我们有“z”作为额外的参数，但是由于管道操作符总是将返回值传递给函数的第一个参数，所以“管道”中函数的额外参数作为第二个或第三个参数传递！](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[我个人认为这非常酷，这是 Elixir 传递数据的方式，而不必使用“状态管理”的方式，如 React 中的状态、Redux 的存储或通过 WebSockets 的跟踪。所有的东西都被无缝地连接在一起，每个部分都做着各自的工作。](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[说到做个人的工作，西克莫的工作就是摇来摇去。](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[![](img/35f3e18eb4245219393fb77f96dc0262.png)](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[梧桐树！](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[它的主人史黛西喜欢它摇着舌头开心，也让皇冠高地的其他人开心。他喜欢给我跳跃，拥抱和嗅嗅，而我给他擦屁股。他是个好男孩。](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[白对瑙尔道:](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[网络信息中心(Network Information Center)ˌ网路界面卡(Network Interface Card)ˌ全国工业理事会(National Industrial Council)ˌ航行情报中心(Navigation Information Center)](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )

[](/how-to-get-a-list-of-languages-in-their-own-language-with-bash-linux-python-node-and-google-31324ccb79cd# Print )