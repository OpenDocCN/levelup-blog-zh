# 使用 Angular ActivatedRoute 在 Ionic 中发送对象和数组

> 原文：<https://levelup.gitconnected.com/using-angular-activatedroute-to-send-objects-and-arrays-in-ionic-78ed2bde9210>

![](img/03ebfa8ac19a43ada7486796e6101daf.png)

像你一样，我有时会怀念以前的 Ionic 2/3，在那里使用一个叫做`NavParams`的神奇发明在页面之间发送对象和数组是非常容易的。

这在 Ionic 4+中已经被否决(在我看来是有争议的),取而代之的是角路由器。使用角路由器，仍然可以在页面之间传送简单的变量(如字符串、数字),但不能传送对象和数组。

但是，有一个简单的方法。如果您希望将对象和数组从一个页面带到另一个页面— *stringify* 它！

假设我们有一个叫做人类的物体:

```
let human={name:'Bob',age:'22',hobby:'fishing'};
```

1.  第一步是`stringify`对象:

```
let human={name:'Bob',age:'22',hobby:'fishing'};let humanStringify=JSON.stringify(human);
//humanStringify is now a string variable with the following value: 
//'{"name":"bob","age":"22","hobby":"fishing"}'
```

2.用激活的路线发送`humanStringify`。修改 **app.routing.module.ts** (我加的东西在**粗体**):

```
...{ path: 'first,
  loadChildren: () => import('./first/first.module').then(m =>  m.FirstPageModule)},
{ path: 'second/**:humanObject**',
  loadChildren: () => import('./second/second.module').then(m => m.SecondPageModule)},...
```

3.在 **first.page.ts** 中添加一个函数，将`humanStringify`发送到 second.page.ts

```
...goToSecondPage()
{
    let human={name:'Bob',age:'22',hobby:'fishing'}; let humanStringify=JSON.stringify(human); this.nav.navigateForward('second/'+humanStringify);}...
```

4.在 **second.page.ts 中，**截取值并解析回一个对象。

```
import {ActivatedRoute} from '@angular/router'
...constructor(public route:ActivatedRoute)
{}...
ionViewWillEnter()
{ 
  let human=JSON.parse(this.route.snapshot.paramMap.get('Object')); //By using JSON.parse, the variable is now successfully parsed as an Object. The object in variable human is:
  //{name:'bob',age:'22',hobby:'fishing'}}
```

这就对了，就这么简单。同样的事情也可以用数组来完成。你所需要做的就是`JSON.stringify`数组并发送*字符串化的*版本。

有趣的附加事实:如果你把一个对象转换成一个字符串，而不是保持它的对象形式，它实际上会更小。字符串比对象和数组小。