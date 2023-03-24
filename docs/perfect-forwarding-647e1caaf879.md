# 完美转发

> 原文：<https://levelup.gitconnected.com/perfect-forwarding-647e1caaf879>

## C++中的完美转发

![](img/2b5b0734555e1d84a96c3b425f69a9f7.png)

照片由[迈特·沃尔什](https://unsplash.com/@two_tees?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

完美转发是什么意思？

“转发”是一个函数*将其参数转发给另一个函数*的过程。当它完美时，该函数应该接收从执行转发的函数传递的相同对象。

换句话说，完美转发意味着我们不仅仅转发对象，我们还转发它们的显著属性，不管它们是左值还是右值，常量还是易变的。

不要太担心定义，我们将通过一些简单的例子。

假设我们有一个简单的类如下

```
class Object {
 public:
  Object() = default;

  void SetName(const std::string &name) { name_ = std::move(name); }
  std::string GetName() const { return name_; }

 private:
  std::string name_;
};
```

我们还有几个名为`UseObject`的重载函数

```
void UseObject(Object &) {
  std::cout << "calling UseObject(Object &)" << std::endl;
}

void UseObject(const Object &) {
  std::cout << "calling UseObject(const Object &)" << std::endl;
}

void UseObject(Object &&) {
  std::cout << "calling UseObject(Object &&)" << std::endl;
}
```

现在我们有了主要的

```
int main() {
  Object object;
  const Object const_object;
  UseObject(object);
  UseObject(const_object);
  UseObject(std::move(object));
}
```

这将产生如下输出

```
calling UseObject(Object &)
calling UseObject(const Object &)
calling UseObject(Object &&)
```

现在，假设我们有一个简单的模板函数，它试图将参数传递给`UseObject`函数

```
template <typename T>
void NotForwardToUseObject(T x) {
  UseObject(x);
}
```

现在，运行代码

```
int main() {
  Object object;
  const Object const_object;
  NotForwardToUseObject(object);
  NotForwardToUseObject(const_object);
  NotForwardToUseObject(std::move(object));
}
```

会导致

```
calling UseObject(Object &)
calling UseObject(Object &)
calling UseObject(Object &)
```

其中函数没有像我们之前预期的那样被相应地调用。

这是因为`const`和`rvalueness`被`void NotForwardToUseObject(T x)`的模板推演忽略了。

为了处理引用参数，我们必须使用通用引用，因为只有通用引用参数对传递给它们的参数的左值和右值进行编码。

如果我们使用模板参数的通用引用，

```
template <typename T>
void HalfForwardToUseObject(T &&x) {  // universal reference
  UseObject(x);
}
```

代码运行

```
int main() {
  Object object;
  const Object const_object;
  HalfForwardToUseObject(object);
  HalfForwardToUseObject(const_object);
  HalfForwardToUseObject(std::move(object));
}
```

会导致

```
calling UseObject(Object &)
calling UseObject(const Object &)
calling UseObject(Object &)
```

差不多！转发`const`似乎有效，但参数的`rvalueness`仍未正确转发。

为了实现真正完美的转发，我们必须将`x`转换为其原始类型和左值或右值

```
template <typename T>
void ForwardToUseObject(T &&x) {
  UseObject(static_cast<T &&>(x));
}
```

现在，运行代码

```
int main() {
  Object object;
  const Object const_object;
  ForwardToUseObject(object);
  ForwardToUseObject(const_object);
  ForwardToUseObject(std::move(object));
}
```

会导致

```
calling UseObject(Object &)
calling UseObject(const Object &)
calling UseObject(Object &&)
```

完美！我们已经成功地正确传递了对象。为了简化代码，我们可以使用 C++ `<utility>`库中的`std::forward`，

```
template <typename T>
void PerfectForwardToUseObject(T &&x) {
  UseObject(std::forward<T>(x));
}
```

我希望上面的例子，现在你已经明白什么是完美的转发。像往常一样，上面的代码可以从我的 [github](https://github.com/pllee4/blog-public/tree/master/posts/003/files) 中获得

感谢阅读到帖子末尾！