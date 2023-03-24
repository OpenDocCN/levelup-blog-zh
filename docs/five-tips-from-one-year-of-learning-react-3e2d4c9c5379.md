# 一年的学习反应在一篇文章中:给初学者的 5 个提示

> 原文：<https://levelup.gitconnected.com/five-tips-from-one-year-of-learning-react-3e2d4c9c5379>

![](img/e294f68131d2012fbe1696201f826a51.png)

反应

在过去的一年里，我专注于为我的 PWA 学习 React。这是 12 个月前的事了——今天，我要告诉你我在过去 12 个月里学到的五个技巧，它们帮助我快速发展。

# 1.用 Typescript！

我第一次涉足 React 是使用 JS。虽然 JS 已经从它开始的地方走了很远，但是它的动态类型本质带来了许多问题，这些问题永远无法通过 cool ES 特性来解决。一次又一次，我传入一个属性或变量是`undefined`并得到运行时属性访问错误——不可能期望一个人每次都能发现变量可能是未定义的。

切换到 Typescript 使我的工作效率提高了 10 倍——我不再需要在编程会话中花费数小时进行调试。Typescript 编译器会捕捉我的大多数运行时错误，这些错误在编译时会在 JS 中显露出来。对于那些刚刚开始学习 React 的人来说，跳过一个半月令人毛骨悚然的调试和`console.log('is' + var + ' undefined?')`的——直接进入类型检查的天堂，即 Typescript。

有人认为 Typescript 只是原型化速度较慢。输入变量比写一个简单的`let t = MyObj()`要花更长的时间。我发现，在学习 Typescript 的一个非常短的学习曲线之后，你实际上比使用 JS 更快地原型化*——这是因为你可以确信你的对象上有你需要的属性，并且当你需要它们时它们实际上就在那里，而不是未定义的或绑定到对象的原型。*

如果你要听听这个列表中的一个提示，那么编写 TypeScript 将会使你领先 10 步。

# 2.使用功能组件实现高效、可读的代码

从 Flutter 开始，类组件的学习曲线要比函数快得多。起初，我避免使用函数组件——钩子很难学，将参数传递给函数组件令人困惑，每个状态变量都有`setState`变量也令人困惑。

不过，我确实在 Reddit 上花了一些时间，100%的 r/React 都是在功能组件上售出的。由于对它们的一致喜爱，我决定硬着头皮把我的下一个组件写成一个功能组件。

我再也没有写过一个类组件。

以下面的组件为例:

```
IMyComponentProps {
    prop1: string
    prop2: string
    prop3: string
}

interface IMyComponentState {
    state1: number
    state2: number
    state3: number
}

export class MyComponent<IMyComponentState, IMyComponentProps> {
    this.timer: NodeJs.Interval

    componentDidMount() {
        this.timer = setInterval(() => this.onRefresh, 200)
        fetchApi().then((data) => this.setState({ state3: data }))
    }

    componentDidUnmount() {
        clearInterval(this.timer)
    }

    onRefresh = () => {
        fetchApi().then((data) => this.setState({ state3: data }))
    }

    build(): JSX.Element {
        return <OtherComponent onClick={this.onRefresh.bind(this)} />
    }
}
```

让我们看看这里出了什么问题:

*   每 30 秒进行一次 API 调用需要很多代码。
*   `.bind()`调用很难看，但对代码正常运行是必要的。
*   一个简单的类组件大约 30 行代码。
*   一大堆`this`，在 TS/JS 中是出了名的混乱。(虽然 TS 会让你免去`this`的一些困难)

一个功能组件可以让一切变得更好:

```
IMyComponentProps {
    prop1: string
    prop2: string
    prop3: string
}

export const MyComponent = ({ prop1, prop2, prop3 }: IMyComponentProps): JSX.Element => {
    const [state1, setState1] = useState(0)
    const [state2, setState3] = useState(0)
    const [state2, setState3] = useState(0)

    let timer: NodeJS.Interval

    const onRefresh = () => fetchApi().then(setState3)

    useEffect(() => {
        timer = setInterval(onRefresh, 200)
        return () => clearInterval(timer)
    })

    return <OtherComponent onClick={onRefresh} />    
}
```

*   三分之一的代码
*   更容易理解
*   可以适应更灵活的 ES6 功能
*   不用担心`this`

所有围绕一个更好的交易，老实说，没有那么高的学习曲线。也许更高级的钩子需要更多的学习，比如`useRef`，但是这些不是必需的，只是有帮助——并且没有等价的类组件。

尽快学习功能组件——这会让你成为更好的开发者。

# 3.最佳状态管理解决方案:未陈述-下一个

这一点是争议较大的问题之一。如果你不知道什么是状态管理解决方案，这里是我的一句话介绍:有时，你需要在组件之间共享状态(例如，登录用户的用户名或者他们是否登录)，将用户名传递给数百个组件是不实际的，相反，你有某种更高范围的存储来存储数据，以便许多组件可以访问它。

我见过很多很多 Redux 粉丝——他们喜欢刚性，如果那是你，那很好！但我喜欢在一个地方做出改变，并让它发挥作用。使用 Redux，我发现自己在一个 Redux 存储中填充了一堆状态变量，这样我就不必重写所有的样板文件。

Next 是迄今为止最简单的状态管理解决方案。我不打算写一个完整的未说明的下一个教程，但不会有太多的教程。《T2》是你需要阅读的全部，你知道你需要知道的一切。

未说明的 Next 是有史以来最简单的状态管理解决方案，它做了 redux 做的所有事情。

# 4.Snowpack >创建 React 应用程序

无论如何，Create React App 并不坏——它实际上做得很好。但我确实认为还有其他更好的选择。例如，我最近一直在使用的是 Create Snowpack 应用程序。创建 Snowpack 应用程序有惊人的快编译速度。创建 React 应用程序，对于较大的项目，需要一点时间来编译。创建 Snowpack 应用程序有`O(n)`编译时间，因为它只重新编译你改变的文件。这意味着一个大型项目的编译速度将和一个较小的项目一样——这是 CRA 无法保证的。在感受到 CRA 在大型项目上的痛苦后，CSA 是一个显而易见的选择。我没有回头。

CSA 是一个巨大的改变，它将加速你的下一个项目。

我构建了自己的 CRA 样板——它有我最喜欢的 eslint 配置和一些我喜欢使用的 tsconfig 更改。您可以对样板文件运行以下命令:)

`git clone [https://github.com/antholeole/snowpack-boiler.git](https://github.com/antholeole/snowpack-boiler.git)`

# 5.只管造！

我最好的建议是最大化你的编码时间。虽然所有这些技巧都会促进你的发展，但你必须学习你的方法。做到这一点的唯一方法是自己开发，不需要一个启发你的项目的指导。

例如，我做的一个个人改变是我的 eslint 配置不允许分号。如果你喜欢分号，那是完美的，不要害怕开拓自己的道路。

以下是您在前端旅程中必须决定的一些事情:

*   SCSS 还是 CSS？
*   BEM 范围界定？还是没有？
*   自举还是顺风？还是自制的款式？
*   你将如何包括 SVG 的？引导图标或将图像包含在您的公共文件夹中？