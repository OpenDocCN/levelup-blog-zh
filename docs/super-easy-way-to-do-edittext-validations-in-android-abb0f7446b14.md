# 在 Android 上进行编辑文本验证的有效方法

> 原文：<https://levelup.gitconnected.com/super-easy-way-to-do-edittext-validations-in-android-abb0f7446b14>

![](img/a55fee7f10f0aeab2f441dc9318ad40a.png)

Android 上的表单验证总是比完成这样一个简单的任务需要更多的努力。例如，多个 if-else 语句或一个帮助您减少样板文件的外部库。我们将简单地通过使用 kotlin 扩展函数来实现这一点。这将帮助我们减少样板代码，并使我们的代码更具可读性。

> 注意:你也可以在 Java 中使用相同的扩展方法。

这里，我们使用 this.parent.parent 从 TextInputEditText 中获取 TextInputLayout，因为 TextInputLayout 将 TextInputEditText 包装到 LinearLayout 中。我们使用 **this.onChange** 方法，这样每当用户开始在编辑文本中输入时，错误就会消失。

要在您的活动中使用扩展功能，您可以在单击提交按钮时触发上述要点。它将显示错误，只有当验证失败时才会返回 **true。**这里，我们使用了逻辑“**和“**操作符，这样它将对 **if 语句**中提到的条件执行验证。

> **注意:只有在需要时才使用逻辑“与”运算符。**

这就是我们如何有效而简单地进行验证。请在评论区告诉我你的评论。

感谢阅读。快乐编码。