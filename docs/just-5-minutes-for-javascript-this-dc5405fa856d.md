# JavaScript“This”只需 3 分钟

> 原文：<https://levelup.gitconnected.com/just-5-minutes-for-javascript-this-dc5405fa856d>

![](img/f383d8b40acd9a8592eba1b959a481f2.png)

杰米·坦普尔顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 的`this`很烦人，会导致一些难以调查的问题。

我看过很多关于 JavaScript 的`this`的文章，我提炼出了理解它的最快方法。

# 恶作剧

你能回答 2 个电话的输出吗？

```
var obj = {
  showThis: function(){
    console.log(this)
  }
}var a = obj.showThis
obj.showThis() // this is obj
a() // this is window
```

如果你不理解这里的行为。这篇文章可能会有所帮助。

# 如何调用 Javascript 函数

在 ES6 中，有 3 种方法调用函数。

```
func(a1, a2) 
obj.child.method(a1, a2)
func.call(context, a1, a2)
```

事实上，前两种方法可以转化为第三种。

```
//func(a1, a2) 
func.call(undefined, a1, a2)//obj.child.method(a1, a2) 
obj.child.method.call(obj.child, a1, a2)
```

我们只需要记住`func.call(context, a1, a2)`。

而`this`就是论点中的`context`。

# 让我们再看一次测验

## 直接函数调用

```
var obj = {
  showThis: function(){
    console.log(this)
  }
}var a = obj.showThis
a.call(undefined) //a()
//output: window
```

但是我们得到的`window`不是未定义的，为什么？因为浏览器中有一条规则，如果上下文为空或未定义，则 window 是默认上下文。(在严格模式下，输出是未定义的)

如果你想从这里得到对象，你可以这样写:

```
var obj = {
  showThis: function(){
    console.log(this)
  }
} var a = obj.showThis
a.call(obj)
//output: obj
```

## 对象方法调用

该方法可以转换:

```
var obj = {
  showThis: function(){
    console.log(this)
  }
}//obj.showThis() 
obj.showThis.call(obj)
//output: obj
```

此时，`this` =上下文所以`this`就是`obj`。

# 箭头功能

记住箭头函数中的`this`最简单的方法就是在箭头函数中**没有新建** `**this**` **。**

箭头功能中的`this`等同于外部的`this`。

```
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100)
}var id = 21foo.call({ id: 42 })
// id: 42
```

在全局中，`this` =窗口，在 foo 函数中，`this` = `{id:42}`。

箭头函数在`foo()`里面，所以箭头函数的`this` = `{id:42}`。

如果将箭头函数改为普通函数，`foo.call({ id: 42 })`的结果将是 21。这是因为`var id = 21`实际上是`window.id`,因为它是在顶层声明的，而`this`将引用`window`,因为上下文没有转移到嵌套的`function`。

# 如何强制这个值

我们可以用 apply/call/bind 来强制`this`的值。

```
var a = {
        name : "Cherry",
​
        func1: function () {
            console.log(this.name)
        },
​
        func2: function () {
            setTimeout(  function () {
                this.func1()
            }.apply(a),100);
        }
​
    };
​
    a.func2()            // Cherryvar a = {
        name : "Cherry",
​
        func1: function () {
            console.log(this.name)
        },
​
        func2: function () {
            setTimeout(  function () {
                this.func1()
            }.call(a),100);
        }
​
    };
​
    a.func2()            // Cherryvar a = {
        name : "Cherry",
​
        func1: function () {
            console.log(this.name)
        },
​
        func2: function () {
            setTimeout(  function () {
                this.func1()
            }.bind(a)(),100);
        }
​
    };
​
    a.func2()            // Cherry
```

就是这样！您可以将您的方法调用或函数调用转换为`func.call()`来验证这一点。希望这有所帮助！