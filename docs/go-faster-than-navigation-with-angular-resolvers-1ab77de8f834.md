# 使用角度解算器比导航更快

> 原文：<https://levelup.gitconnected.com/go-faster-than-navigation-with-angular-resolvers-1ab77de8f834>

![](img/45fcb435c05c99f082b2bc7900c1d8e6.png)

图片来源:Freepik

我记得第一次读到角旋变器时，它们听起来很酷。让路由器预取数据？是的，请！但是当我试图跟随 Angular 网站上的[教程时，我有点迷路了。](https://angular.io/guide/router#resolve-pre-fetching-component-data)

虽然作者在分解所有内容方面做得非常出色，但我还是不断遇到 null injector 错误，因为当你从一个延迟加载的库到另一个库，而不是直接从一个模块到你的应用程序时，情况会有所不同。你真的需要了解路由器[、激活路由和路由器状态](https://www.smashingmagazine.com/2018/11/a-complete-guide-to-routing-in-angular/)之间的区别，才能真正体会角度解析器有多酷。

# 什么是角分解器？

想象一个有角度的管道，但是你在页面加载之前就使用它了！这意味着您可以转换或获取数据，而不必订阅激活的路由！换句话说，不用再等了！

解析器可以极大地增强你的应用或网站体验，因为它们在用户决定导航到某个地方的那一刻就开始了。事实上，在解析器完成之前，页面甚至不会加载。因此，你不必与比赛条件争夺，可以为他们到来时做好准备。

请记住，我们谈论的是毫秒，所以不要太疯狂。以下是我开始着手的一些用例:

1.  生成和更新元标签
2.  获取博客文章
3.  抓取动态参数进行搜索查询
4.  基于您的路由模块创建导航菜单

# 它们是如何工作的？

想象一下，让一个完全独立的服务存在于你的模块之外，就好像它实际上在你的模块之内一样。它使用 ActivatedRouteSnapshot 和 RouterStateSnapshot 作为其 resolve 方法的参数，这样它就可以使用您通过 ActivatedRoute 和 RouterState 获得的完全相同的路由数据来向您发送数据。

因此，它已经为您准备好了数据，而不是订阅路由事件！您所要做的就是向路由模块添加额外的参数，就像处理路径、组件和数据一样。因此，在同一级别上，只需添加一个 resolve 参数，说明使用什么键以及哪个解析器应该提供数据。然后，获取激活路线的快照以检索数据。

# 能给我举个端到端的例子吗？

当然可以！不久前，我给[写了一篇关于创建自我更新导航菜单](https://medium.com/swlh/how-to-create-a-self-updating-navigation-menu-in-angular-d90ef1b7acf5)的文章。虽然我很喜欢在一个地方更新路线和元信息，但我不喜欢创建多个菜单，即使这样做非常简单快捷。

现在，为了创建导航菜单，我可以将以下内容添加到我的路由模块中:

```
resolve: { 
    displayTitle: MetaTagsResolverRouteDataService, 
    menu: ResolveMenuService 
},
```

**注意:“显示标题”和“菜单”是任意值，仅在此处声明。当需要查找您的路线数据时，它们将被用作密钥。**

您可以选择在模块、路由或组件级别使用解析器。请记住，如果您将它提供给父路由，它的所有子路由都可以轻松访问它。但是，您可能需要记住，每当用户导航到该路线时，它都会触发。

**demos-routing.module.ts**

接下来，我将数据添加到路由模块，以指定使用什么值。

```
 { 
   path: 'select-menus', 
   component: DemoSelectMenusComponent, 
   data: { 
      title: 'Select Menus', 
      description: "Cloud engineering demo of customizable select menus", 
      menu: { icon: 'dropdown', label: 'Select Menus', show: true, activeSelfOnly: true, 
            }, 
        }, 
 },
```

**resolve-menu.service.ts**

我的解析器超级简单。它只是解析出构建导航菜单所需的数据并将其返回。

需要注意的一点是，根据您设置路线的方式，可能存在父/子层级。我的菜单几乎总是在父级，因为我希望它们出现在属于它们库的每个页面上。您可以使用 Angular 的路由器插座创建布局一致的特定于库的模板。

```
import { Injectable } from '@angular/core';
import { Resolve, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';
import { ResolveMenu } from './menus';
import { Observable } from 'rxjs';@Injectable({
   providedIn: 'root'
})export class ResolveMenuService implements Resolve<ResolveMenu[]> { constructor() { } resolve(
         route: ActivatedRouteSnapshot,
         state: RouterStateSnapshot
         ): ResolveMenu[] | Observable<ResolveMenu[]> { let menu: ResolveMenu[] = [];

                 route.routeConfig.children.map(child => {
                        if (child.data.menu)
                            menu.push({
                                 path: child.path,
                                 show: child.data.menu.show,
                                 activeSelfOnly: child.data.menu.activeSelfOnly,
                                 title: child.data.title,
                                 label: child.data.menu.label,
                                 icon: child.data.menu.icon
                            })
                  }) return menu; }}
```

**menus.demo.component.ts**

最后，为了查看解析后的数据，我可以这样调用它:

```
import { Component, OnInit } from '@angular/core'; 
import { ActivatedRoute } from '@angular/router'; 
import { ButtonRoute } from 'projects/buttons/src/public-api'; @Component({ 
    selector: 'ces-demos-menu', 
    templateUrl: './demos-menu.component.html', 
    styleUrls: ['./demos-menu.component.scss'] 
}) export class DemosMenuComponent implements OnInit {      buttons: ButtonRoute[] = [];      constructor( private route: ActivatedRoute, ) {}      ngOnInit(): void { this.buttons = this.route.snapshot.data.menu;      } 
}
```

解析的数据存储在 ActivatedRouteSnapshot 和 RouterStateSnapshot 中。只需查找您在指定路线时添加的任何键，它将以 JSON 格式提供。

**demos-menu.component.html**

为了呈现菜单，我只需将 this.buttons 值传递给我为处理路由而创建的按钮路由组件:

```
<nav>
  <ces-button-route *ngFor="let button of buttons" [link]="button.path" [title]="button.title" [label]="button.label" [icon]="button.icon" [activeSelfOnly]="button.activeSelfOnly" class="nav-button">
  </ces-button-route>
</nav>
```

瞧，这是[导航菜单在起作用](https://cloudengineering.studio/demos)。我通常会把它隐藏起来，因为一个项目有一个导航菜单看起来很傻，但现在我会把它保留下来，以防你想看一眼。这样，将来每当我在这个部分添加另一个演示时，菜单就会自动创建。

如果你想一起看所有的文件，这里有 Github 列表。

# 需要注意的事项

# 循环引用

不能在将使用解析器的同一个库/模块中构建解析器。同样，不能使用库/模块内部的服务，也不能将解析器放在目标库使用的同一个库中。这将创建一个循环参考和 Angular 是足够聪明的让你知道。

例如，我为我的博客服务创建了一个数据库来获取文章。我无法将解析器放入这两个库中，也无法使用博客服务来获取文章。因此，我将我的解析器放在一个路由库中，该库使用数据服务来获取文章。

无论如何，最好的做法是让所有事情都朝着一个方向进行，以免导致不必要的依赖。

# 没有提供程序错误

记住，解析器就像一条浮动的路线。因此，如果您希望在用户导航到某个路线之外使用解析器，请传入 ActivatedRoute 和 RouterState，而不是 ActivatedRouteSnapshot 和 RouterStateSnapshot。

希望你喜欢这篇文章。感谢阅读！

*最初发布于*[*https://cloud engineering . studio*](https://cloudengineering.studio/articles/go-faster-than-navigation-with-angular-resolvers)*。*