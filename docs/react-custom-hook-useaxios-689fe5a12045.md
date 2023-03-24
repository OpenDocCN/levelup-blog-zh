# React 自定义挂钩:useAxios()

> 原文：<https://levelup.gitconnected.com/react-custom-hook-useaxios-689fe5a12045>

作为前端开发人员，在每个 React 项目中，我们必须调用多个组件中的 API 来获取它们各自的数据。除此之外，我们还希望在后台处理错误场景和设置加载器。

假设我们有多个组件，我们想从服务器获取一些数据并显示在 UI 上。绝对不建议在每个组件中编写相同的代码。为了避免这种情况，我们可以使用 **axios** 作为自定义钩子。

> 让我们创建一个 useAxios(自定义钩子)

```
**import { useEffect, useState } from "react";
import axios from 'axios';
const useAxios = (configParams) => {
    axios.defaults.baseURL = 'https://jsonplaceholder.typicode.com';
    const [res, setRes] = useState('');
    const [err, setErr] = useState('');
    const [loading, setLoading] = useState(true);
    useEffect(() => {
       fetchDataUsingAxios(configParams);
    }, []);
    const fetchDataUsingAxios = async() => {
        await axios.request(configParams)
        .then(res => setRes(res)
        .catch(err => setErr(err))
        .finally(() => setLoading(false));
    }
    return [res, err, loading];
}
export default useAxios;**
```

1.  我们已经设置了默认的基本 URL，因此任何类型的请求(get/post/..)将只进行到此端点(https://jsonplaceholder . typicode . com)。
2.  我们已经创建了三种状态(响应、错误和加载)。
3.  我们已经使用 async 和 await 发出异步 HTTP 请求，这样它就不会阻塞任何其他线程。
4.  axios.request 对于任何类型的 HTTP 请求都非常有用，因为它只需要 config{url，method，body，headers}对象，其余的将自动处理。
5.  我们也可以使用 axios.get 或 axios.post 来代替。选择权在你。

> 让我们转到第二部分，如何在你的组件中使用 useAxios

```
**import { useEffect, useState } from "react/cjs/react.development";
import useAxios from "./useAxios";
const YourComponent = () => {
     const [data, setData] = useState(null);
     const [todo, isError, isLoading] = useAxios({
           url: '/todos/2',
           method: 'get',
           body: {...},
           headers: {...}
     });
     use Effect(() => {
        if(todo && todo.data) setData(todo.data)
     }, [todo]);
     return (
       <> {isLoading ? (
            <p>isLoading...</p>
       ) : (
           <div>
                {isError && <p>{isError.message}</p>}
                {data && <p>{data.title}</p>}</div>
           </div>
        )} </>
      )
}
export default YourComponent;**
```

这里，我们创建了一个由{url、方法、主体和头}组成的 config 对象，并将其传递给 useAxios({…。})，它将进行 HTTP 调用并返回数组值[res，err，loading]，我们通过[析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)将这些值存储在[todo，isError，isLoading]中。

useEffect 对“todo”变量进行依赖检查，如果发生任何变化，它将更新 UI 后面的本地状态。

请在评论中分享你的想法。继续学习！！

> ***感谢阅读。关注更多*。**