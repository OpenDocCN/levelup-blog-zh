# 8 个很棒的 Vue 开发技巧

> 原文：<https://levelup.gitconnected.com/8-awesome-vue-development-tips-661b7631aa47>

![](img/a4927ed39f516dcfd586b4fccb4d5313.png)

## 1.路由参数解耦

一般来说，使用组件中的路由参数，大多数人会这样做。

```
export default {
    methods: {
        getParamsId() {
            return this.$route.params.id
        }
    }
}
```

在组件中使用`$route`会导致与其对应的路由高度耦合，从而通过将组件限制在某些 URL 上来限制组件的灵活性。

正确的方法是通过`props`将其解耦

```
const router = new VueRouter({
    routes: [{
        path:  /user/:id ,
        component: User,
        props: true
    }]
})
```

将路由的 props 属性设置为 true 后，可以通过 props 在组件内接收 params 参数。

```
export default {
    props: [ id ],
    methods: {
        getParamsId() {
            return this.id
        }
    }
}
```

也可以通过功能模式返还道具。

```
const router = new VueRouter({
    routes: [{
        path:  /user/:id ,
        component: User,
        props: (route) => ({
            id: route.query.id
        })
    }]
})
```

## 2.功能成分

功能组件是无状态的，它不能被实例化，并且没有任何生命周期或方法。创建功能组件也很简单，只需在模板中添加功能声明。它通常适用于仅依赖于外部数据变化的组件，并且由于其重量轻，渲染性能得到提高。

组件需要的一切都通过上下文参数传递。它是一个上下文对象，具体属性见文档。这里的 props 是一个包含所有绑定属性的对象。

```
<template functional>
    <div class="list">
        <div class="item" v-for="item in props.list" :key="item.id" @click="props.itemClick(item)">
            <p>{{item.title}}</p>
            <p>{{item.content}}</p>
        </div>
    </div>
</template>
```

父组件使用

```
<template>
    <div>
        <List :list="list" :itemClick="item => (currentItem = item)" />
    </div>
</template>
```

```
import List from  @/components/List.vue
export default {
    components: {
        List
    },
    data() {
        return {
            list: [{
                title:  title ,
                content:  content
            }],
            currentItem:
        }
    }
}
```

## 3.样式范围

在开发中修改第三方组件样式是很常见的，但是由于作用域属性的样式隔离，可能有必要移除作用域或开始另一种样式。这些实践有副作用(组件样式污染，缺乏优雅)，样式渗透在 css 预处理器中使用以生效。

我们可以用`>>>`或者`/deep/`来解决这个问题:

```
<style scoped>
Outer layer >>> .el-checkbox {
  display: block;
  font-size: 26px;

  .el-checkbox__label {
    font-size: 16px;
  }
}
</style>
```

```
<style scoped>
/deep/ .el-checkbox {
  display: block;
  font-size: 26px;

  .el-checkbox__label {
    font-size: 16px;
  }
}
</style>
```

## 4.手表的高级使用

当 listener 属性改变时会触发 watch，有时我们希望在组件创建后立即执行 watch。

可能想到的方法是在创建生命周期中调用它一次，但这并不是一种优雅的编写方式，所以也许我们可以像这样使用它。

```
export default {
    data() {
        return {
            name:  Joe
        }
    },
    watch: {
        name: {
            handler:  sayName ,
            immediate: true
        }
    },
    methods: {
        sayName() {
            console.log(this.name)
        }
    }
}
```

**深度倾听**

当监听一个对象时，当对象内部的属性被改变时，手表不能被触发，所以我们可以为它设置深度监听

```
export default {
    data: {
        studen: {
            name:  Joe ,
            skill: {
                run: {
                    speed:  fast
                }
            }
        }
    },
    watch: {
        studen: {
            handler:  sayName ,
            deep: true
        }
    },
    methods: {
        sayName() {
            console.log(this.studen)
        }
    }
}
```

**触发监听器执行多个方法**

使用数组你可以设置多种形式，包括字符串，函数，对象。

```
export default {
    data: {
        name:  Joe
    },
    watch: {
        name: [
             sayName1 ,
            function(newVal, oldVal) {
                this.sayName2()
            },
            {
                handler:  sayName3 ,
                immaediate: true
            }
        ]
    },
    methods: {
        sayName1() {
            console.log( sayName1==> , this.name)
        },
        sayName2() {
            console.log( sayName2==> , this.name)
        },
        sayName3() {
            console.log( sayName3==> , this.name)
        }
    }
}
```

## **5。手表监听多个变量**

手表本身无法监听多个变量。然而，我们可以通过返回一个带有计算属性的对象，然后监听该对象来“监听多个变量”。

```
export default {
    data() {
        return {
            msg1:  apple ,
            msg2:  banana
        }
    },
    compouted: {
        msgObj() {
            const { msg1, msg2 } = this
            return {
                msg1,
                msg2
            }
        }
    },
    watch: {
        msgObj: {
            handler(newVal, oldVal) {
                if (newVal.msg1 != oldVal.msg1) {
                    console.log( msg1 is change )
                }
                if (newVal.msg2 != oldVal.msg2) {
                    console.log( msg2 is change )
                }
            },
            deep: true
        }
    }
}
```

## 6.事件参数$event

`$event`是事件对象的一个特殊变量，它在某些场景下给了我们更多可用的参数来实现复杂的功能。

本机事件:行为与本机事件中的默认事件对象相同。

```
<template>
    <div>
        <input type="text" @input="inputHandler( hello , $event)" />
    </div>
</template>
```

```
export default {
    methods: {
        inputHandler(msg, e) {
            console.log(e.target.value)
        }
    }
}
```

自定义事件:在自定义事件中表示为捕获从子组件抛出的值。

```
export default {
    methods: {
        customEvent() {
            this.$emit( custom-event ,  some value )
        }
    }
} 
```

```
<template>
    <div>
        <my-item v-for="(item, index) in list" @custom-event="customEvent(index, $event)">
            </my-list>
    </div>
</template>
```

```
export default {
    methods: {
        customEvent(index, e) {
            console.log(e) //  some value
        }
    }
}
```

## 7.编程事件侦听器

例如，在装入页面时定义计时器需要在页面被销毁时清除计时器。这似乎不是问题。但是仔细观察一下，this.timer 的唯一目的是能够在 beforeDestroy 内获取计时器号，否则就没用了。

```
export default {
    mounted() {
        this.timer = setInterval(() => {
            console.log(Date.now())
        }, 1000)
    },
    beforeDestroy() {
        clearInterval(this.timer)
    }
}
```

如果可能的话，最好只有生命周期挂钩可以访问。这不是一个严重的问题，但可以认为是杂乱。

我们可以通过使用`$on`或`$once`监听页面生命周期破坏来解决这个问题

```
export default {
    mounted() {
        this.creatInterval( hello )
        this.creatInterval( world )
    },
    creatInterval(msg) {
        let timer = setInterval(() => {
            console.log(msg)
        }, 1000)
        this.$once( hook:beforeDestroy , function() {
            clearInterval(timer)
        })
    }
}
```

用这种方法，即使我们同时创建多个定时器，也不影响效果。这是因为它们将在页面被销毁后以编程方式自动清除。

## 8.倾听组件生命周期

通常我们使用$emit 来监听组件生命周期，父组件接收事件进行通知。

子组件

```
export default {
    mounted() {
        this.$emit( listenMounted )
    }
}
```

父组件

```
<template>
    <div>
        <List @listenMounted="listenMounted" />
    </div>
</template>
```

事实上，有一种简单的方法可以使用 [@hook](http://twitter.com/hook) 来监听组件的生命周期，而不需要在组件内部做任何更改。类似地，创建、更新等。也可以用这个方法。

```
<template>
    <List @hook:mounted="listenMounted" />
</template>
```