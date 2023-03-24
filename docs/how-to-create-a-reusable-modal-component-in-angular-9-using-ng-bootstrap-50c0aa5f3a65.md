# 如何使用 ng-bootstrap 在 Angular 9 中创建可重用的模态组件

> 原文：<https://levelup.gitconnected.com/how-to-create-a-reusable-modal-component-in-angular-9-using-ng-bootstrap-50c0aa5f3a65>

当我作为初级 Web 开发人员开始我的第一份全职工作时，我意识到我的同事正在为应用程序中需要的每个对话框创建一个单独的组件。对我来说这是不可接受的，所以我决定开发一个可重用的模态组件来满足应用程序的需求。

![](img/f3945fadc53c7c273d132b77e69116f0.png)

照片由 [Florian Olivo](https://unsplash.com/@florianolv?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在开始之前，我想感谢[*modal* [modalConfig]="modalConfig">
<!-- body of the modal -->
</modal>](https://medium.com/u/14c6e677f21e#<strong class=)

[其中`modalConfig`是`ModalConfig`的实例，我们在这里指定了我们在步骤 1 中讨论的行为。](https://medium.com/u/14c6e677f21e#<strong class=)

[为了打开 modal，我们使用它的 id，在我们的例子中是`#modal`来获取它，作为。ts 文件并调用其函数。我们是这样做的:](https://medium.com/u/14c6e677f21e#<strong class=)

```
@ViewChild('modal') private modalComponent: ModalComponentasync openModal() {
  return await this.modalComponent.open()
}
```

[将调用异步函数`openModal()`来打开 id 为`#modal`的模态。当然，这意味着您可以在同一个组件中包含多个模态，只要您给它们惟一的 id。](https://medium.com/u/14c6e677f21e#<strong class=)

# [结论](https://medium.com/u/14c6e677f21e#<strong class=)

[关于如何在 Angular 9 和 ng-bootstrap 中创建可重用的模态组件的教程到此结束。如果您对如何改进有任何意见或建议，请告诉我。](https://medium.com/u/14c6e677f21e#<strong class=)

[编码快乐！](https://medium.com/u/14c6e677f21e#<strong class=)

[](https://medium.com/u/14c6e677f21e#<strong class=)