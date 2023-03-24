# JavaScript 最佳实践—名称、变量和函数

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-names-variables-and-functions-a77c5c6d6e7e>

![](img/31823b955993f0ad24d32e5aa1b81263.png)

贾斯汀·艾金在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

为了使代码易于阅读和维护，我们应该遵循一些最佳实践。

在这篇文章中，我们将看看我们应该遵循的一些最佳实践，以使每个人的生活更轻松。

# 使用有意义且容易发音的变量名

有意义和可发音的变量名是好的。

例如，我们应该写:

```
const currentDate = new Date();
```

而不是:

```
const yyyymmd = new Date();
```

# 对相同类型的变量使用相同的词汇

对于同一类型的变量，我们应该使用相同的词汇。

例如，我们不应该有 3 个用户名。

而不是写:

```
getUserInfo();
getPersonData();
getSubscriberRecord();
```

对于获取用户数据的函数，我们应该编写:

```
getUser();
```

来获取用户数据。

# 使用可搜索的名称

我们应该使用可搜索的名称。

这排除了太短或太普通的名字。

此外，我们不应该有神奇的数字。

例如，我们不应该写:

```
setTimeout(doSomething, 86400000);
```

相反，我们写道:

```
const DAY_IN_MILLISECONDS = 86_400_000;setTimeout(doSomething, DAY_IN_MILLISECONDS);
```

# 用变量来解释

我们应该给变量起个名字来解释它们的内容。

例如，我们应该写:

```
const phone = "555-555-1212";
const [areaCode, exchangeCode, lineNumber] = phone.split('-'); 
```

现在我们知道一个电话号码可以分成几个数字。

# 没有心理映射

我们应该避免用头脑去映射变量的含义。

例如，我们不应该写:

```
phoneNumbers.forEach(p => {
  call(p);
});
```

我们会很快忘记`p`是什么意思。

相反，我们写道:

```
phoneNumbers.forEach(phoneNumber => {
  call(phoneNumber);
});
```

现在我们不用思考就知道回调的参数是电话号码。

# 没有不需要的上下文

我们可以在标识符名称中添加太多的信息。

例如，如果我们有:

```
const car = {
  carMake: "Ford",
  carModel: "Fiesta",
  carColor: "Blue"
};
```

我们不需要在每个属性名前面都加`car`。

相反，我们写道:

```
const car = {
  make: "Ford",
  model: "Fiesta",
  color: "Blue"
};
```

我们知道该对象保存汽车的数据，而没有在每个属性中提及它。

# 使用默认参数

默认参数比设置默认参数值的短路或条件要好的多。

例如，不写:

```
function createPerson(name) {
  const personName = name || "james";
  // ...
}
```

我们写道:

```
function createPerson(name = 'james') {
  // ...
}
```

使用默认参数更简单。

同样，`||`为所有虚假参数返回`'james'`，如空字符串、`null`、`undefined`、0 或`NaN`。

# 理想情况下是 2 个或更少的函数参数

函数参数越多，就越难使用。

如果有更多的数据类型，我们很容易忘记它们的顺序。

如果我们需要更多，我们可以使用带有析构的对象参数，这样我们就不用担心顺序了。

我们可以随心所欲地争论。

例如，不写:

```
function createCar(make, body, color, lengthInMeters) {
  // ...
}createMenu("ford", "fiesta", "red", 5);
```

我们写道:

```
function createCar({ make, body, color, lengthInMeters }) {
  // ...
}createCar({
  make: "ford",
  body: "fiesta",
  color: "red",
  lengthInMeters: 5
});
```

我们传入一个对象并将属性析构为参数中的变量。

# 函数应该做一件事

做多件事的函数不好。

很混乱，可能很长。

因此，他们应该做一件事。

例如，我们应该写:

```
function emailActiveCustomers(customers) {
  clients.filter(customerRecord).forEach(email);
}function isActiveCustomer(customer) {
  const customerRecord = database.lookup(customer);
  return customerRecord.active;
}
```

而不是:

```
function emailClients(customers) {
  clients.forEach(customer => {
    const customerRecord = database.lookup(customer);
    if (customerRecord.active()) {
      email(client);
    }
  });
}
```

`emailClients`功能既检查客户是否活跃，又给客户发电子邮件。

我们应该将它们分配给`isAcriveCustiomer`来检查客户是否活跃，分配给`emailActiveCustomer`来给客户发电子邮件。

# 名字的功能取决于它们做什么

我们应该用一种能告诉我们它们做什么的方式来命名函数。

例如，不写:

```
function add(date, years) {
  // ...
}

const date = new Date();

add(date, 1);
```

我们写道:

```
function addYearsToDate(date, years) {
  // ...
}

const date = new Date();

addYearsToDate(date, 1);
```

`addYearsToDate`比`add`清晰多了。

![](img/80de42d05f442955059ddbaad38decb5.png)

照片由[查理·格林](https://unsplash.com/@charliegreen998?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们应该清楚地命名变量和函数。

还有，我们要让名字有意义。

函数应该做一件事，不能有太多的参数。

如果我们需要更多的参数，那么我们可以接受一个对象并析构它，这样我们就不必担心它们的传入顺序。