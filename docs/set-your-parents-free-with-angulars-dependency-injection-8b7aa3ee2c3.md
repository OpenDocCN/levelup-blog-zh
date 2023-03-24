# 用 Angular 的依赖注入释放你的父母

> 原文：<https://levelup.gitconnected.com/set-your-parents-free-with-angulars-dependency-injection-8b7aa3ee2c3>

![](img/2c372b762920be91bb993b62c8650d15.png)

资料来源:Freepik

如果您使用模块化的、组件驱动的架构，您将面临的最大挑战之一是如何在父组件和子组件/指令之间实现通信。一方面，您希望每个组件/指令独立地专注于它们的用途。另一方面，您需要它们一起工作，就好像它们是作为一个有凝聚力的单元构建的一样。

幸运的是，Angular 提供了您在任何情况下所需的一切！

如果您的父视图包含子视图，一种常见的方法是使用[@ Input()和@Output()装饰符](https://angular.io/guide/inputs-outputs)。要与父级通信，您可以通过子级发出自定义事件。若要与子节点通信，可以传递绑定到父节点的属性。

还可以用 [@ViewChild/@ViewChildren](https://angular.io/api/core/ViewChild#description) 和[@ content child/@ content children](https://angular.io/api/core/ContentChild)来打破亲子墙。(我会在以后的文章中讨论这些强大的装饰者。)

如果您的父视图不直接包含子视图，或者根本没有关系，您可以在服务中使用 observables 来传递数据。这样，实际的关系并不重要，因为任何注入服务的人都可以更新和订阅 observables。

# 为什么要使用依赖注入来寻找父节点

我创建了一个任务系统，在表格行组件中显示每个任务。我还创建了按钮组件来对表行进行操作。但是，我不想在表行组件中包含按钮组件，这样无论它们是否在表中，都可以使用它们。

我面临的问题是如何给按钮组件分配一个特定的 ID，而不必一遍又一遍地传递 ID 作为输入。例如，下面是表行组件的 HTML，它遍历一组任务。

```
<ces-table-row *ngFor="let task of tasks; let index = index;" [id]="task.id" [index]="index">
    <ces-table-column class="justify-center">
        <ces-view-pin [active]="task.pinned"></ces-view-pin>
        <ces-view-delete></ces-view-delete>
    </ces-table-column>
    <ces-table-column class="justify-center">{{ task.id }}</ces-table-column>
    <ces-table-column class="justify-center">
        <ces-task-menu-statuses [default]="task.status"></ces-task-menu-statuses>
    </ces-table-column>
    <ces-table-column>{{ task.dateUpdated | date: 'short' }}</ces-table-column>
    <ces-table-column>{{ task.title }}</ces-table-column>
    <ces-table-column>{{ task.notes }}</ces-table-column>
</ces-table-row>
```

我提到的按钮组件使用了选择器‘ces-view-pin’和‘ces-view-delete’。我希望这些组件只负责按钮交互的状态，并在它们被按下时向服务发送消息。这样，我可以避免每次使用它们时重复多余的代码，而且无论谁在使用它们，它们的行为都是一样的。

因为表行组件正在获取任务 ID，所以我需要一种方法在层次结构中向上导航来访问它。幸运的是，Angular 提供了一种简单的方式来搜索层次结构。

# 搜索父接口

## 步骤 1:创建一个抽象类

我首先创建了一个抽象类，它包含了我希望按钮组件能够访问的属性。

```
export abstract class View {
  abstract id: number;
  abstract index: number;
}
```

## 步骤 2:实现抽象类并添加提供者

下一步是实现抽象类，并为任何试图注入它的人提供一个参考。

```
import { Component, forwardRef, Input } from '@angular/core';
import { View } from 'projects/view/src/public-api';@Component({
  selector: 'ces-table-row',
  templateUrl: './table-row.component.html',
  styleUrls: ['./table-row.component.scss'],
  providers: [{
    provide: View, useExisting: forwardRef(() => TableRowComponent)
  }]
})
export class TableRowComponent implements View {
  @Input() id!: number;
  @Input() index: number = -1; constructor() { }
}
```

## 步骤 3:将接口注入子组件/指令

最后一步是使用 [@Optional() decorator](https://angular.io/api/core/Optional) 将视图类注入到任何需要访问抽象类中定义的父类属性/方法的子类中。

```
export class ViewDeleteComponent { constructor(
    protected _viewService: ViewService,
    @Optional() public view: View
  ) { } delete() {
    const message: ApiChange = {
      id: this.view.id,
      index: this.view.index,
      action: 'delete',
    } this._viewService.setChange(message);
  }
}
```

如果你想看到所有三个组件的代码，查看这个 [github 要点](https://gist.github.com/yokoishioka/0fc3a8e1794a33657060ae4a8cbe73b5)。