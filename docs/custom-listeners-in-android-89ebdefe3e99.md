# Android 中的自定义监听器

> 原文：<https://levelup.gitconnected.com/custom-listeners-in-android-89ebdefe3e99>

![](img/be7ed68f4487107806c03ae30b132939.png)

监听器是 Android 开发的主要部分。它们是创建异步回调的一种非常流行的方式。侦听器通常用于实现事件发生时运行的代码。

如果你在 Android 开发方面有点经验，你可能会遇到监听器的一个常见用法是按钮的内置`onClickListener`。我们通常将`onClickListener`设置在一个按钮上，带有按钮被点击时应该运行的代码，在本例中是事件。在 Java 中，通常是这样的:

```
Button button = findViewById(R.id.example);
button.setOnClickListener(new View.OnClickListener(){
    @Override
    public void onClick(View view) {
        //Do some work here
    }
});
```

但是除了我上面所描述的，我们可以创建我们的自定义侦听器，回调附加到从我们代码的特定区域触发的事件。

**但是为什么呢？**

![](img/6271f1c7a9a29f086e26b65c69a942ae.png)

以下是一些我们可能需要创建自定义侦听器的情况:

*   当我们需要从一个片段向一个活动发出一个事件时
*   当我们需要从适配器内部发出一个事件到一个活动或片段时

一般来说，当您有一个“子对象”和一个“父对象”或处理程序时，监听器是有用的，其中父对象是创建子对象的新实例的对象。并且我们有一些工作需要在父对象中完成，但也需要在子对象中发生某个事件时才完成。因此，我们必须找到一种方法，将正在讨论的事件已经在子对象中发生的事实传达给父对象。

那么我们如何创建一个定制的监听器呢？

1.  首先，在子对象中定义一个接口(适配器、片段、POJO)。然后定义将触发到父级的事件。这些事件由接口中的方法表示。Java 中的一个例子是:

```
public interface CustomListener{ void onDataReady(Data data); void onSubmitForm();}
```

在上面的例子中，`onDataReady(Data data)`和`onSubmitForm()`是表示可能发生在子对象中的事件的方法签名/回调。

2.接下来，设置一个侦听器变量来存储接口中回调的特定实现。回调的实现将由父对象定义。因此，在子类中，您可以创建变量以及公共 setter 方法，该方法允许从父类定义侦听器回调，如下所示:

```
private CustomListener mListener;public void setCustomListener(CustomListener listener){
    mListener = listener;
}
```

您不必使用 setter 方法，因为有几种方法可以将侦听器回调实现传递给子对象，比如通过构造函数传递或者通过生命周期事件传递(比如在处理活动/片段通信时片段的`onAttach()`事件)。

3.现在我们已经创建了 listener 变量，我们可以在父类上实现接口，覆盖这些方法，并放入这些方法的实现，然后在子对象上设置 listener 实现(这是实现接口的父类)。

```
public class Parent implements Child.CustomListener{//Some code...@Override
    public void onDataReady(Data data){
        //some fancy implementation
    }public void onSubmitForm(){
       //code we want to run when this event occurs
    }childObject.setCustomListener(this);}
```

以上是在父类中创建监听器实现的一种非常常见的方式。另一种方法是在父类中创建自定义侦听器的实例(而不是让父类本身实现接口),并将该实现设置为子对象的自定义侦听器，如下所示:

```
Child childObject = new Child();Child.CustomListener listener = new Child.CustomListener(){
    @Override
    public void onDataReady(Data data){
        //some fancy implementation
    } public void onSubmitForm(){
       //code we want to run when this event occurs
    }}childObject.setCustomListener(listener);
```

![](img/fee5a837889934b04813f74ab3cfca44.png)

开个玩笑，差不多了。

4.现在，当事件发生时，子对象可以使用侦听器向父对象触发事件，并将数据(如果有)传递给父对象。例如，在子对象中，可能是这样的:

```
//The event "onDataReady" has occurred in the child object and we //fire up the event to the parent using the listener public void OnSuccess(Response response){ Data data = response.getData; listener.onDataReady(data);}
```

就这样，我们完成了定制监听器的设置！如果您还没有这样做，我希望您能够开始制作和使用您的定制监听器。或者至少对它们有了更好的理解。