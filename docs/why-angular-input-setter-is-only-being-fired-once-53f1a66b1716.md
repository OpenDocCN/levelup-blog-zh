# 为什么角度输入设置器只被触发一次

> 原文：<https://levelup.gitconnected.com/why-angular-input-setter-is-only-being-fired-once-53f1a66b1716>

## 研究在组件之间传递数据的各种方法

![](img/4cadcf9a201945fb8bdffe6dea121e67.png)

照片由 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [AltumCode](https://unsplash.com/@altumcode?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

使用 Angular 应用程序需要将不同的组件组合在一起，并在它们之间传递数据。将数据从父组件传递到子组件的一种常见方法是使用输入属性 setter。我们都知道 getter 和 setter，它们只是用来拦截对属性的访问。

令人惊讶的是，与“标准”TypeScript setter 相比，Angular component input setter 的行为有细微的差别。我是在处理以下问题时发现的。

## 原始问题

为了说明这个问题，我设置了一个虚构的[例子](https://stackblitz.com/edit/ng-input-setter-issue)，如下所示。

```
// Parent Component
<button (click)="reset()">set to Parent</button>
<app-child [data]="message"></app-child>reset() {
  this.message = 'Parent';
}//Child Component
<p>Value  is: {{ data }}</p>_val = '';
@Input()
set data(val: string) {
   this._val = val;
}
```

我们有两个组件:上面例子中的父组件和子组件。`data` 是子组件中的简单输入设置器。当用户从父组件中点击“`set to Parent`”按钮时，一个字符串值将被设置为“parent ”,子组件设置器将被调用。

其实上面的说法只是第一次成立。如果多次点击`set to Parent`按钮，则从第二次开始不会触发子组件设置器。

因此，如果 setter 的值没有改变，看起来输入 setter 只被触发一次。

## 根本原因

为了理解这种行为的根本原因，我深入研究了 angular 源代码。在下面的`[bindingUpdated](https://github.com/angular/angular/blob/d1ea1f4c7f3358b730b0d94e65b00bc28cae279c/packages/core/src/render3/bindings.ts#L52)` 函数行 7 中，Angular 通过比较新值和其组件逻辑视图中的旧值来检查值的变化。它只在值实际改变时更新绑定。

如上所示，Angular Input setter 被设计为在值没有改变的情况下忽略绑定更新，而普通 TypeScript 类中的 setter 将在每次调用时被触发，不管值是否改变。

对于这个用例，我想在每次单击父组件中的“`set to Parent`”按钮时触发子组件的重置。我怎样才能实现这一点？

## 工作区

我尝试的第一个解决方法是使用`ngOnChanges`。我将下面的代码添加到子组件中，希望它能够在单击“设置为父组件”按钮时捕获数据。

```
ngOnChanges(changes: SimpleChanges) {
   console.log('ngOnChanges', changes);
}
```

但是控制台日志只在按钮第一次点击时打印。显然，`ngOnChanges` 的下划线绑定代码与输入设置器相同。

第二个尝试是在子组件中公开一个新的公共方法`setParent`。从父组件，我们用`ViewChild` decorator 创建对子组件的引用，并在点击按钮时调用`setParent` 方法。

```
// Child Component
setParent() {
   this.data = 'Parent';
}
// Parent Component
@ViewChild('childComp', { static: true })
childComp: ChildComponent;

resetChild() { 
  this.childComp.setParent();
}// Parent Component template
<button (click)="resetChild()">call reset to Parent</button>
```

这种方法行得通。但是我对它不是很满意。公开一个方法并直接调用另一个组件并不是一种干净的方式。它在两个组件之间创建了一个紧密的耦合。当方法被多个父组件使用时，情况会变得更糟。

我仍然喜欢使用 input 属性，因为它在组件之间创建了一个契约。我们还能通过绕过限制来利用输入属性 setter 吗？

## 使用可观察值的更好方法

如果我们回到 Angular 源代码，它使用`[Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)`来检测值的变化。

```
If(Object.is(oldValue, value)) 
```

比较两个对象时，如果“`both the same object (meaning both values reference the same object in memory)`”，则`Object.is`将返回 true。

因此，如果我们将包含相同值的可观察对象传递到输入属性中，Angular 将把它作为一个更改的值！

下面是我们使用 observable 作为输入属性的新实现。

```
// Parent Component
eventsSubject: Subject<string> = new Subject();resetByObservable() {
   this.eventsSubject.next('Parent');
}// Parent Component template
<button (click)="resetByObservable()">set to Parent by observable</button><app-childObservable [events]="eventsSubject"></app-childObservable>// Child Component
_val: Subject<string> = new Subject();
@Input()
set events(val: Subject<string>) {
  this._val = val;
}
ngOnInit() {
  this.eventsSubscription = this.events.subscribe((x) => {
     this.data = x;
  });
}
```

在上面的实现中，我们使用一个输入设置器作为`Subject` 类型，并订阅子组件中的数据更改。子组件订阅捕获“设置为父”按钮的每次点击。

您可以在 [stackblitz](https://stackblitz.com/edit/ng-input-setter-issue) 项目中尝试上面的例子。

## 输入属性 setter 与 ngOnChanges

在前面的小节中，我们尝试了输入属性 setter 和`ngOnChanges`生命周期挂钩。

它们是检测和响应数据变化的两种方式，可用于在组件之间传递参数。只有当绑定值改变时，才会触发这两个事件。

它们可以在不同的用例中使用。使用`ngOnChanges` ，您可以在一个地方访问所有的属性更改。对于输入属性设置器，它仅适用于单个属性。

在组件之间共享数据还有其他常见的方法

*   通过与[行为主体](https://www.learnrxjs.io/learn-rxjs/subjects/behaviorsubject)的共享服务
*   通过来自子组件或兄弟组件的`@Output`装饰器和`EventEmitter`

## 摘要

尽管 TypeScript 中的属性 setter 简单明了，但问题在于细节:Angular input 属性 setter 在变化检测方面有细微的差别。在本文中，我们将探讨如何解决在两个角度分量之间发送重复值的问题。

我们可以选择不同的方式在组件之间传递数据。使用`@Input` 和 `@Output`是我的首选方法，因为它们充当组件之间的契约，并使组件更加可重用。

如果你喜欢这篇文章，你可能也喜欢看我下面的另一篇有角度的文章。

[](https://javascript.plainenglish.io/angular-state-management-with-observable-service-pattern-27b18538f4c3) [## 具有可观察服务模式的角度状态管理

### Angular 状态管理是任何 Angular 应用程序的核心，但没有放之四海而皆准的解决方案。

javascript.plainenglish.io](https://javascript.plainenglish.io/angular-state-management-with-observable-service-pattern-27b18538f4c3) 

希望这篇文章对你有用。您可以在这个 [stackbliz](https://stackblitz.com/edit/ng-input-setter-issue) 项目中找到示例代码。