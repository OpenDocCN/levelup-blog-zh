# 使用自定义钩子| useLocalStorage 处理 React 中的本地存储

> 原文：<https://levelup.gitconnected.com/handling-local-storage-in-react-using-a-custom-hook-uselocalstorage-a9df55d46eee>

作为 React 开发人员，我们一直都在使用钩子。因此，我们可以像这样创建一个自定义钩子，而不是一直调用像`localStorage.getItem()`和`localStorage.setItem`这样的函数:

在代码中实现这个挂钩非常简单:

```
const [state, setState] = useLocalStorage("keyIdentifier", "defaultValue")// Amd use the variables just like you use variables of an useState return
```