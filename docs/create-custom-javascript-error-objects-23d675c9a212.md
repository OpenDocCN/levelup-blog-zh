# 创建自定义 JavaScript 错误对象

> 原文：<https://levelup.gitconnected.com/create-custom-javascript-error-objects-23d675c9a212>

![](img/c813391039bda5da351b52c3930308d0.png)

[马里德莎](https://unsplash.com/@malidesha?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 JavaScript 中，有一个`Error`类让我们抛出异常。我们可以通过用我们自己的类和定制逻辑扩展`Error`类来创建定制的错误类，以抛出更具体的错误。

`Error`类有我们从它继承的`message`、`name`和`stack`属性。当然，像任何其他类一样，我们可以在其中定义自己的字段和方法。

# 创建新的错误类

例如，为了使数据验证更容易，我们可以创建一个`ValidationError`类，每当我们的代码遇到无效数据时就会抛出这个类。

我们可以创建一个新类，如下所示:

```
class ValidationError extends Error {
  constructor(message, type) {
    super(message);
    this.name = "ValidationError";
    this.type = type;
  }
}
```

正如我们所看到的，我们使用了`extends`关键字来表示我们想要从`Error`类继承。

# 使用我们定义的错误类

在我们定义了我们的错误类之后，我们可以如下使用我们的`ValidationError`类:

```
try {
  let data = {
    email: 'abc'
  }
  if (!/[^@]+@[^\.]+\..+/.test(data.email)) {
    throw new ValidationError('invalid email', 'email error');
  }
} catch (ex) {
  console.log(ex);
}
```

在`console.log`输出中，当我们运行代码时，我们应该看到‘validation error:invalid email’。

我们还可以分别记录`message`、`name`、`stack`和`type`属性:

```
try {
  let data = {
    email: 'abc'
  }
  if (!/[^@]+@[^\.]+\..+/.test(data.email)) {
    throw new ValidationError('invalid email', 'email error');
  }
} catch (ex) {
  const {
    message,
    name,
    stack,
    type
  } = ex;
  console.log(
    message,
    name,
    stack,
    type
  );
}
```

那么我们应该看到:

*   `invalid email`为`message`记录
*   `ValidationError`为`name`记录
*   `ValidationError: invalid email
    at window.onload`为`stack`
*   `email error`登录为`type`

对于另一种类型的对象，我们可以使用`instanceof`操作符来检查它是否是`ValidationError`的实例，如下所示:

```
try {
  let data = {
    email: 'abc'
  }
  if (!/[^@]+@[^\.]+\..+/.test(data.email)) {
    throw new ValidationError('invalid email', 'email error');
  }
} catch (ex) {
  if (ex instanceof ValidationError){
   console.log('validation error occurred');
  }
}
```

然后我们应该看到`‘validation error occurred’`被记录，因为我们抛出了一个`ValidationError`类的实例。

我们还可以查看`name`属性来辨别不同种类的错误。例如，我们可以写:

```
try {
  let data = {
    email: 'abc'
  }
  if (!/[^@]+@[^\.]+\..+/.test(data.email)) {
    throw new ValidationError('invalid email', 'email error');
  }
} catch (ex) {
  if (ex.name === 'ValidationError') {
    console.log('validation error occurred');
  }
}
```

然后我们得到和上面一样的东西。

![](img/1df2d024580a1b89c5166142d631601e.png)

托宾·罗杰斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 包装异常

我们可以包装较低级别的异常，即父异常构造函数的子类的实例，并创建较高级别的错误。

要做到这一点，我们可以捕捉和重新抛出较低级别的异常，并在较高级别的代码中捕捉它们。

首先，我们必须创建一个从`Error`类扩展而来的父类和子类，如下所示:

```
class FormError extends Error {
  constructor(message, type) {
    super(message);
    this.name = "FormError";
  }
}class ValidationError extends FormError {
  constructor(message, type) {
    super(message);
    this.name = "ValidationError";
    this.type = type;
  }
}
```

在上面的代码中，`FormError`类扩展了`Error`类，并作为`ValidationError`类的父类。

然后，我们编写一些代码，利用我们创建的异常类:

```
const checkEmail = (data) => {
  try {
    if (!/[^@]+@[^\.]+\..+/.test(data.email)) {
      throw new ValidationError('invalid email', 'email error');
    }
  } catch (ex) {
    if (ex instanceof ValidationError) {
      throw new FormError(ex.name)
    } else {
      throw ex;
    }
  }
}try {
  let data = {
    email: 'abc'
  }
  checkEmail(data);
} catch (ex) {
  if (ex instanceof FormError) {
    throw new FormError(ex.name)
  } else {
    throw ex;
  }
}
```

在上面的代码中，我们有一个`checkEmail`函数，如果`data.email`属性有一个无效的电子邮件，它会抛出一个错误。如果抛出一个`ValidationError`，那么我们抛出一个新的错误。

然后我们在`checkEmail`函数后创建一个`try...catch`块来包装函数调用，然后我们在`catch`块中捕捉异常。我们检查`FormError`实例，如果被捕获的异常是`FormError`的实例，那么抛出一个`FormError`。

否则，我们直接抛出捕获的异常。我们使用这种模式是为了在顶层只处理一种错误，而不是检查顶层代码中的所有错误。

另一种方法是用`if...else`语句在顶层检查各种错误，这更复杂。

我们可以从`Error`类继承来创建我们自己的错误类。然后我们可以用关键字`throw`抛出新的错误实例。

在我们定义的类中，我们可以添加自己的实例字段和方法。

当我们捕捉到错误时，我们可以使用`insranceof`操作符来检查抛出的错误类型。我们也可以使用`name`属性来做同样的事情。

在我们的低级代码中，我们可以捕捉子类错误类型的错误，然后再抛出父类实例的错误。然后，我们可以捕捉更高级类型的错误，而不是检查更低级类型的错误实例。