# 如何创建 Javascript HTML 生成器

> 原文：<https://levelup.gitconnected.com/how-to-create-a-javascript-html-generator-439f52b88ccc>

![](img/d2e720f11a1592584a9d1ab9d12e6821.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我最近用 vanilla Javascript 构建了一个文本编辑器 web 应用程序，在编码几个小时后，我很快厌倦了不断创建 DOM 元素。我正在动态地创建元素，因为每次渲染时它们都需要动态 id、事件侦听器和内容。我很快意识到模板系统会更好地满足我的需求，并清理我的类。

我决定建立自己的模板引擎，我决定称之为支配者只是为了好玩。我将介绍我所采取的基本步骤和我用来创建它的代码。

首先，我想为模板创建一个简单的格式，所以我创建了一个名为`templates.js`的新文件，其中有一个对象`Templates`填充了我使用的每个模板。我将模板构建为函数，这样我就可以向它们传递变量。这是我的文档图标的模板。

```
const Templates = {
  docIcon: function(obj) {
    return {
      tag: 'div',
      id: obj.id,
      classes: ['doc-icon'],
      properties: {
        title: obj.name,
        tabIndex: 0
      },
      children: [
        {
          tag: 'div',
          classes: ['doc-icon-header'],
          children: [
            { tag: 'h3', content: obj.name }
          ]
        },
        {
          tag: 'p',
          content: obj.exerp
        },
        {
          tag: 'div',
          id: 'docControls',
          classes: ['doc-controls'],
          children: [
            {
              tag: 'button',
              classes: ['doc-control'],
              id: 'moveDoc',
              children: [
                {
                  tag: 'i',
                  classes: ['fa', 'fa-folder']
                }
              ]
            },
            {
              tag: 'button',
              classes: ['doc-control'],
              id: 'deleteDoc',
              children: [
                {
                  tag: 'i',
                  classes: ['fa', 'fa-trash']
                }
              ]
            }
          ]
        }
      ]
    }
  }
```

正如您所看到的，标签、id 和类被明确定义为我们的对象的键，但是其他任何东西都在 properties 键中传递。我们还有一个子数组，每个子数组都有和父数组相同的格式。理论上，我们可以用这种格式无限嵌套 DOM 元素。

一旦我们的模板有了这个简单的 Javascript 对象格式，很容易很快地构建大量的模板，但是我们如何转换成 HTML 并添加到 DOM 中呢？

我创建了一个名为`Dominator`的类，用实例将一个 DOM 元素表示为一个对象。一个实例看起来非常像我们的模板，除了应用动态值。我只是使用`Object.assign()`来复制模板。

```
class Dominator {
  constructor(object) {
    Object.assign(this, object)
  }
}
```

接下来，我们需要弄清楚如何创建孩子。我们可以使用 for 循环递归地为孩子创建类的新实例。然后，我们将孩子推入一个空的初始化数组，在循环之后，我们将`this.children`赋值为等于我们的数组。

```
----
  constructor(object) {
    Object.assign(this, object)
    let childObjects = []
    if (object.children){
      for (const child of object.children) {
        let childObject = new Dominator(child)
        childObjects.push(childObject)
      }
    }
    this.children = childObjects
    this.element = this.domElement
  }
----
```

到目前为止，我们有一个对象，它是模板的副本，我们的内容应用于它。还没用。接下来，我们需要将它们转换成 DOM 元素。我创建了一个名为`domElement()`的 get 函数来返回一个生成的 DOM 元素。我使用 get，这样我们就可以随时更新我们的属性并重新生成元素。

该函数的第一部分创建元素并设置基本属性。

```
get domElement() {
    const domElement = document.createElement(this.tag)
    if (this.id) domElement.id = this.id
    if (this.content) domElement.innerText = this.content

  ----- return domElement
}
```

接下来需要做的三件事是迭代并分配属性、迭代并设置类，以及迭代并向我们的 domElement 追加子元素。

```
get domElement() {
    const domElement = document.createElement(this.tag)
    if (this.id) domElement.id = this.id
    if (this.content) domElement.innerText = this.content if (this.properties) for (const prop in this.properties) {
      domElement[prop] = this.properties[prop]
    }
    if (this.classes) for (const cssClass of this.classes) {
      domElement.classList.add(cssClass)
    }
    if (this.children) for (const child of this.children) {
      domElement.append(child.domElement)
      if (child.id) this[child.id] = child.domElement
    }
    this.element = domElement return domElement
  }
```

如您所见，我们有条件地检查所有内容，以确保它存在于我们的模板中，然后将这些内容应用于我们的 domElement，然后返回它。

到目前为止，我们的整个类是这样的:

```
class Dominator {
  constructor(object) {
    Object.assign(this, object)
    let childObjects = []
    if (object.children){
      for (const child of object.children) {
        let childObject = new Dominator(child)
        childObjects.push(childObject)
      }
    }
    this.children = childObjects
    this.element = this.domElement
  }
  get domElement() {
    const domElement = document.createElement(this.tag)
    if (this.id) domElement.id = this.id
    if (this.content) domElement.innerText = this.content
    if (this.properties) for (const prop in this.properties) {
      domElement[prop] = this.properties[prop]
    }
    if (this.classes) for (const cssClass of this.classes) {
      domElement.classList.add(cssClass)
    }
    if (this.children) for (const child of this.children) {
      domElement.append(child.domElement)
      if (child.id) this[child.id] = child.domElement
    }
    this.element = domElement
    return domElement
  }
}
```

让我们看看它的实际效果吧！

```
get docIcon() {
  const dominator = new Dominator(Templates.docIcon(this))
  const div = dominator.domElement
  div.addEventListener('click', this.openDoc.bind(this))
  div.querySelector('#deleteDoc').addEventListener('click', this.deleteDoc.bind(this))
  div.querySelector('#moveDoc').addEventListener('click', this.moveDoc.bind(this))
  this.icon = div
  return div
}
```

我们如何简化事件监听器过程？让我们添加一个名为`findChildById()`的新函数。

```
findChildById(id) {
  if (this.children) {
    for (const child of this.children) {
      if (child.id === id) {
        return child
      } else if (child.children) {
        let found = child.findChildById(id)
        if (found) return found
      }
    }
  }
  return false
}
```

通过一点递归，我们可以找到 Dominator 的子实例，并对其进行修改。现在，我们可以在构造函数中创建一个名为`this.eventListeners = []`的数组，我们将添加事件侦听器，稍后将其分配给 DOM 元素。

为了添加事件，我希望它尽可能简单，所以首先是回调，然后是可选 ID，最后是事件类型，为了方便起见，默认为 click。ID 参数假设我们正在寻找具有该 ID 的子节点。

```
event(action, id, type = 'click') {
    if (id) {
      const node = this.findChildById(id)
      if (node) {
        node.eventListeners.push({type: type, action: action})
      }
    } else {
      this.eventListeners.push({type: type, action: action})
    }
  }
```

最后，在我们的`get domElement()`函数中，我们需要以下内容:

```
if (this.eventListeners) for (const eventListener of this.eventListeners) {
   domElement.addEventListener(eventListener.type, eventListener.action)
}
```

现在我们可以重构之前的代码了:

```
get docIcon() {
  const dominator = new Dominator(Templates.docIcon(this))
  dominator.event(this.openDoc.bind(this))
  dominator.event(this.deleteDoc.bind(this), 'deleteDoc')
  dominator.event(this.moveDoc.bind(this), 'moveDoc')
  const div = dominator.domElement
  this.icon = div
  return div
}
```

维奥拉。干净多了。

这是我们支配者的全部代码:

```
class Dominator {
  constructor(object) {
    Object.assign(this, object)
    let childObjects = []
    if (object.children){
      for (const child of object.children) {
        let childObject = new Dominator(child)
        childObjects.push(childObject)
      }
    }
    this.eventListeners = []
    this.children = childObjects
    this.element = this.domElement
  }
  get domElement() {
    const domElement = document.createElement(this.tag)
    if (this.id) domElement.id = this.id
    if (this.content) domElement.innerText = this.content
    if (this.properties) for (const prop in this.properties) {
      domElement[prop] = this.properties[prop]
    }
    if (this.classes) for (const cssClass of this.classes) {
      domElement.classList.add(cssClass)
    }
    if (this.children) for (const child of this.children) {
      domElement.append(child.domElement)
      if (child.id) this[child.id] = child.domElement
    }
    if (this.eventListeners) for (const eventListener of this.eventListeners) {
      domElement.addEventListener(eventListener.type, eventListener.action)
    }
    this.element = domElement
    return domElement
  }
  findChildById(id) {
    if (this.children) {
      for (const child of this.children) {
        if (child.id === id) {
          return child
        } else if (child.children) {
          let found = child.findChildById(id)
          if (found) return found
        }
      }
    }
    return false
  }
  event(action, id, type = 'click') {
    if (id) {
      const node = this.findChildById(id)
      if (node) {
        node.eventListeners.push({type: type, action: action})
      }
    } else {
      this.eventListeners.push({type: type, action: action})
    }
  }
}
```