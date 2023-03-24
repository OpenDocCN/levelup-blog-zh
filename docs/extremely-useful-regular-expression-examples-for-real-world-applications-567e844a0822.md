# 对现实世界应用程序非常有用的正则表达式示例

> 原文：<https://levelup.gitconnected.com/extremely-useful-regular-expression-examples-for-real-world-applications-567e844a0822>

## 正则表达式的最佳用例

![](img/b3dce6bb267d276e1ed65f8829d29496.png)

Max Nelson 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将看到一些有用的正则表达式，您可以在现实世界的应用程序中使用它们

**1。电子邮件验证:**

```
^[^@ ]+@[^@ ]+\.[^@ \.]{2,}$
```

*说明:*

`^[^@ ]+` = >检查以非@字符和空格开头的电子邮件(+表示前面的字符出现一次或多次)

`@[^@ ]+` = >然后是一个@符号，其后不允许有@符号和空格(+表示前一个字符出现一次或多次)

`\.[^@ \.]{2,}$` = >然后是一个点，然后是至少两个字符，不包括@、空格和点。请注意，我们在点(.)，就点(。)没有反斜杠意味着它将匹配任何字符。

```
function isEmailValid(email) {
 if(/^[^@ ]+@[^@ ]+\.[^@ \.]{2,}$/.test(email)) {
  console.log('email is valid');
 } else {
  console.log('email is invalid')
 }
}isEmailValid('abc@11gmail.com'); // email is valid
isEmailValid('abc11gmail.com'); // email is invalid
```

**2。密码验证:**

```
(?=.*\d)(?=.*[a-z])(?=.*[A-Z])(?!.*\s)(?=.*[!@#$*])
```

*解释:*

此正则表达式检查至少包含一个大写字母、小写字母、数字和特殊符号的密码

`(?=.*\d)` = >检查任何后跟数字的字符是否出现零次或多次

`(?=.*[a-z])` = >检查任何后跟小写字母的字符的零次或多次出现

`(?=.*[A-Z])` = >检查任何后跟大写字母的字符的零次或多次出现

`(?!.*\s)` = >检查非空格字符

`(?=.*[!@#$*])` = >检查列表中任何字符的零次或多次出现，后跟任何特殊字符！、@、#、$和*

```
function isPasswordValid(password) {
 if(/(?=.*\d)(?=.*[a-z])(?=.*[A-Z])(?!.*\s)(?=.*[!@#$*])/.test(password)) {
  console.log('password is valid');
 } else {
  console.log('password is invalid')
 }
}isPasswordValid('Abc@123'); // password is valid
isPasswordValid('Abc123'); // password is invalid
```

**3。有效日期格式:**

```
\d{4}-\d{2}-\d{2}
```

*解释:*

此正则表达式检查日期是否以 YYYY–MM–YY 格式输入

像 2019–12–18 是 4 位数–2 位数–2 位数的格式

```
function isDateValid(date) {
 if(/\d{4}-\d{2}-\d{2}/.test(date)) {
  console.log('date is valid');
 } else {
  console.log('date is invalid')
 }
}isDateValid('2019-12-18'); // date is valid
isDateValid('2019-2-18'); // date is invalid
```

**4。空字符串验证:**

```
^\s*$
```

*解释:*

这个正则表达式检查字符串是否只包含空格

```
function isEmpty(value) {
 if(/^\s*$/.test(value)) {
  console.log('string is empty or contains only spaces');
 } else {
  console.log('string is not empty and does not contain spaces')
 }
}isEmpty('abc'); // string is not empty and does not contain spaces
isEmpty(''); // string is empty or contains only spaces
```

**5。电话号码验证:**

```
^[\\(]\d{3}[\\)]\s\d{3}-\d{4}$
```

*解释:*

此正则表达式检查(123)456–8911 格式的电话号码，即(3 位数)(空格)(3 位数)(连字符)(4 位数)

```
function isValidPhone(phone) {
 if(/^[\\(]\d{3}[\\)]\s\d{3}-\d{4}$/.test(phone)) {
  console.log('phone number is valid');
 } else {
  console.log('phone number is invalid')
 }
}isValidPhone('(123) 456-8911'); // phone number is valid
isValidPhone('(123)456-8911'); // phone number is invalid
```

**6。信用卡号验证:**

Visa 信用卡:`^4[0–9]{12}(?:[0–9]{3})?$`

美国运通信用卡:`^3[47][0–9]{13}$`

万事达卡:`^(?:5[1–5][0–9]{2}|222[1–9]|22[3–9][0–9]|2[3–6][0–9]{2}|27[01][0–9]|2720)[0–9]{12}$`

发现卡:`^6(?:011|5[0–9]{2})[0–9]{12}$`

```
function isValidVisaCard(cardNumber) {
 if(/^4[0-9]{12}(?:[0-9]{3})?$/.test(cardNumber)) {
  console.log('valid visa credit card number');
 } else {
  console.log('invalid visa credit card number')
 }
}isValidVisaCard('4111111111111'); // valid visa credit card number
isValidVisaCard('2111111111111'); // invalid visa credit card number
isValidVisaCard('411111111111'); // invalid visa credit card number
```

如果你在理解上面的任何正则表达式上有困难，看看我以前的文章[这里](https://medium.com/javascript-in-plain-english/how-to-easily-understand-any-regular-expression-in-the-world-46ff6a5f551c?source=friends_link&sk=b1ace0462bda6a97973a54e1b103c0d7)关于如何容易地理解世界上任何正则表达式。

今天到此为止。我希望你今天学到了新东西。

**别忘了直接在你的收件箱** [**这里**](https://yogeshchavan.dev) **订阅我的每周简讯，里面有惊人的技巧、诀窍和文章。**