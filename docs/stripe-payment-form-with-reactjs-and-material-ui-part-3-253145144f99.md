# 带反应和材料的条纹支付表单-用户界面-第 3 部分

> 原文：<https://levelup.gitconnected.com/stripe-payment-form-with-reactjs-and-material-ui-part-3-253145144f99>

![](img/30a930011a13a3df85bd5fb878fa4b72.png)

本文共分 4 个部分:
**1。** [用户界面与素材-UI](https://t.co/ylkqiFJ64u?amp=1)
**2。** [创建表单](https://t.co/NiWBVDCcg5?amp=1)
**3。**用 React useContext
**4 保存用户输入。** [实现条纹](https://t.co/UArLhAcZDs?amp=1)

> 如果你只对如何实现 Stripe 感兴趣，直接跳到[第 4 部分](https://t.co/UArLhAcZDs?amp=1)。

在第三部分中，我们将实现 React 钩子 useContext 来保存在每个输入字段中输入的数据。

如果你不熟悉，这里有一篇关于这个主题的好文章:

[](https://alligator.io/react/usecontext/) [## 简单地说，React useContext 挂钩

### 2019 年 2 月初，React 推出了 Hooks，作为一种将您的组件重写为简单、更易管理和…

鳄鱼. io](https://alligator.io/react/usecontext/) ![](img/50c8390f206cbb14d492b44c92136807.png)

首先在“src”中创建一个文件夹，调用“stateContext”并创建两个文件:“index.js”和“reducer.js”

```
src/
**├── stateContext/
│   ├── index.js
│   └── reducer.js** ├── components/
│   └── StripeInput.js
├── constants/
│   ├── functions.js
│   └── theme.js
└── views/
    ├── Footer.js
    ├── Header.js
    ├── Main.js
    ├── Stepper.js
    ├── StepConnector.js
    ├── StepIcons.js
    └── Forms
        ├── ContactForm.js
        ├── PaymentForm.js
        └── ServiceForm.js
```

# 还原剂

*src/state context/reduce . js*

Reducer.js 是我们保存表单中输入的所有数据的地方。我们也将在这里创建处理这些数据的函数，比如当用户在一个文本字段中输入内容时会发生什么。

首先创建一个对象“initialState”，这是当站点第一次加载时设置每个值的地方。

在“initialState”对象中，您将创建另一个名为“formValues”对象。我这样做是因为你最有可能将这个 initialState 用于我们的支付表单的其他东西，但是如果你不这样做，你不必特别创建一个子对象，可以将“formValues”的所有值直接放在 initial state 中。

无论如何，我将使用 formValues，在那里我将输入在[第 2 部分](https://t.co/NiWBVDCcg5?amp=1)中创建的每个输入字段(文本字段)的名称，并给它们一个空字符串值。

```
const initialState = {
     formValues: {
          date: "",
          service: "",
          facebook: "",
          twitter: "",
          firstname: "",
          lastname: "",
          email: "",
          line1: "",
          line2: "",
          postal_code: "",
          city: "",
          country: null,
          currency: null,
          amount: "",
     },
};
```

请注意，国家和货币有一个“null”值，而不是一个空字符串，这是因为这两个是自动完成字段，它们的值将是一个对象，而不是一个字符串。

现在我们需要设置我们的“缩减器”(缩减器是一个决定应用程序状态变化的函数)。

在我们的例子中，我们将使用不止一个函数，我们希望一个函数来编辑值，另一个函数在购买完成后将值重置为“初始状态”。

```
const reducer = (state, action) => {
   switch (action.type) {
      case 'editFormValue':        
         state.formValues[action.key.toLowerCase()] = action.value;
      return { ...state };
      case 'emptyFormValue':
      return {
         ...state,
         formValues: initialState.formValues
      };
      default:
   };
   return state;
};
```

要向这个函数发送数据，我必须运行“reducer()”并向它发送一个对象。例如，如果我想更改名字，对象应该是这样的:

```
{
   type: "editFormValue",
   key: "firstname",
   value: value
}
```

然后我们导出两者:

```
export { initialState, reducer }
```

# 指数

*src/state context/index . js*

我们将在这个文件中使用 Reducer.js 和官方的上下文挂钩(createContext、useContext 和 use Reducer)。我在一个单独的文件中这样做，因为我不想在每个需要的文件中都这样做。

```
import React,
      { createContext,
        useContext,
        useReducer }
from './node_modules/react';
import { reducer, initialState } from './reducer';const StateContext = createContext();export const StateProvider = ({ children }) =>
<StateContext.Provider value={useReducer(reducer, initialState)}>
     {children}
</StateContext.Provider>export const useStateValue = () => useContext(StateContext);
```

如您所见，我导入了 reducer.js 并将其添加到“StateContext”的值中。提供商”。这将允许我们在整个应用程序中使用它们。

就是这样！我们现在准备将它连接到我们的应用程序。

# 应用

*src/App.js*

导入新的 StateProvider:

```
import { StateProvider } from ‘./stateContext’;
```

并包装整个应用程序:

```
const App = () =>
**<StateProvider>**
     <ThemeProvider theme={theme}>
          <Header />
          <Main />
          <Footer />
     </ThemeProvider>
**</StateProvider>**
```

现在是激动人心的部分…我们将把每个输入元件连接到我们的减速器上。

![](img/b9cbf9645d00660a14d567b248ac8803.png)

# 联系人表单

*src/views/Forms/contact form . js*

只需导入“useStateValue”:

```
import { useStateValue } from "../../stateContext";
```

添加此变量:

```
const [{ formValues }, dispatch] = useStateValue();
```

并将值和 onChange 侦听器添加到每个 TextField。
以下是“名字”文本字段的一个示例:

```
value={formValues.**firstname**}
onChange={e =>
     dispatch({
         type: "editFormValue",
         key: "**firstname**",
         value: e.target.value
     })
}
```

瞧啊。您只需将它添加到每个文本字段中，当然还要确保根据该文本字段的名称更改键和值。

唯一的区别是对于 Autocomplete，您将把它添加到 Autocomplete 标记，而不是 TextField。

所以 ContactForm 应该是这样的:

# 服务表单

*src/views/Forms/service form . js*

同样用服务表单，导入“useStateValue”，用

```
const [{ formValues }, dispatch] = useStateValue();
```

并将值和 onChange 添加到每个 TextField。

# 支付形式

*src/views/Forms/payment form . js*

对于付款表单，我们将只为货币和金额的文本字段添加值和 onChange 侦听器。在下一个也是最后一个部分，信用卡、到期日和 CVV 将以不同的方式处理。

在付款表单上，我向金额文本字段添加了一个正则表达式，以确保用户只能输入数字:

```
onChange={e =>
     dispatch({
          type: "editFormValue",
          key: "amount",
          value: e.target.value**.replace(/[^0-9,.]/g, '')**
     })
}
```

![](img/4b34c294f7dfa8957ec78647a6f38fec.png)

是啊！！！第三部分完成了！我们现在跟踪在各个输入字段中输入的所有值，并且能够轻松地将它们转发到 Stripe。

如果你已经连续看完了前三部分，你应该在这一点上休息一下。只需冷静几分钟，因为最后一部分将是最后一次运行——如何实现条带化？

[](https://t.co/UArLhAcZDs?amp=1) [## 带反应和材料的条纹支付表单-UI -第 4 部分

### 一个语言很重要的地方

t.co](https://t.co/UArLhAcZDs?amp=1)