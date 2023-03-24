# 继续滚动，滚动，滚动…

> 原文：<https://levelup.gitconnected.com/just-keep-scrolling-scrolling-scrolling-47c3863fe653>

## *我如何用 React Redux 实现无限滚动功能*

![](img/0ade4b17c9ef785c92b3bfb99dcedff2.png)

对于我与熨斗学校的最后一个项目，我开发了 Osiris，一个用户可以注册并发布他们想要重新安家而不是处理掉的旧物清单的应用程序。

我用 React 和 Redux 构建了这个项目的前端，用 Ruby on Rails 构建了后端。

作为这个项目的一部分，我想挑战自己，编写自己的无限滚动组件，在用户滚动列表索引页面时呈现新的列表。

通过向您介绍所需的变量和函数，我将介绍我是如何做到这一点的；以及如何自己复制这个特征。

## 从哪里开始？

当使用无限滚动特性时，你将需要一个无限(不完全是无限)的条目集合来滚动。网上有很多 API 可供选择，但是对于这个项目，我使用了我自己在 Rails 服务器上运行的 API。

一个重要的注意事项是，所使用的 API 必须能够根据提供给它的页码返回数据“页”中的信息。

有了 API 源，我们现在必须考虑在哪里存储返回的信息。

## 如何储存？

用于确定何时加载更多项目的逻辑将需要使用某些变量。通过合并 Redux，我将它们存储在全局可用的存储中。

这些变量如下:

*   `allLoaded` -一个布尔变量，当从 API 中检索到所有项目时返回 true
*   `pageNumber` -一个整数，用于跟踪要加载的项目的下一“页”
*   `loading` -一个布尔变量，当等待获取请求来检索项目时返回 true
*   `listings` -已经检索到的所有列表的数组

## 行动

为了根据需要操纵这些变量，特定类型的动作被分派给 Redux reducer。

为了能够处理异步操作，使用了 Thunk 节点，并在创建存储时将其合并到 Redux 的中间件中:

```
const store = createStore(rootReducer, applyMiddleware(thunk))
```

`addListings`负责将获取的列表添加到 Redux store 中。在 reducer 中，可以看到当返回少于 21 个清单的响应时,`allLoaded`变量被设置为 true。这个数字必须反映每页返回的项目数。

```
// action
export const addListings = listings => {
    return ({
        type: "ADD_LISTINGS",
        listings
    })
}// reducer case
case "ADD_LISTINGS":
    return {
        ...state,
        pageNumber: state.pageNumber + 1,
        listings: [...state.listings, ...action.listings],
        allLoaded: action.listings.length < 21 ? true : false,
        loading: false,
        show: {}
    }
```

`loadingListings`负责将 loading 变量设置为 *true* ，稍后将用于防止向 API 发送多余的请求。

```
// action
export const loadingListings = () => {
    return ({
        type: "LOADING_LISTINGS"
    })
}// reducer case
case "LOADING_LISTINGS":
    return {...state, loading: true}
```

`fetchListings`操作不同，因为它返回一个接受 dispatch 作为参数的函数，然后利用 thunk 中间件异步操作；必要时调度前面的操作。

```
// action
export const fetchListings = (pageNumber) => {
    return (dispatch) => {
        dispatch(loadingListings()) fetch(listingURL + '?q=' + pageNumber)
            .then(res=>res.json())
            .then(listings => dispatch(addListings(listings)))
    }
}
```

最后，`resetAllLoaded`用于重置`allLoaded`变量，以防更多的列表被呈现。

```
// action
export const resetAllLoaded = () => {
    return ({
        type: "RESET_ALL_LOADED"
    })
}// reducer case
case "RESET_ALL_LOADED":
    return {...state, allLoaded: false}
```

## 成分

有必要具备:

*   一个项目组件，
*   提取项目时呈现的可选加载组件，以及
*   一个容纳这些项目的容器——这是我们将要构建无限滚动特性的组件。

## 钩住

在函数容器组件中，我选择利用 React-Redux 的钩子函数。

这个方法也可以在扩展 React 组件时实现，但是需要使用`componentDidMount`生命周期事件(如果您想一起探索这个问题，请随时与我联系)。

我用的钩子是:

*   `useSelector` -从 Redux 存储中检索上述变量
*   `useDispatch` -利用商场的配送方式
*   `useCallback` -编写将在 useEffect 函数中使用的函数，这些函数将根据其因变量进行更新
*   `useEffect` -允许组件在重新渲染时执行动作

将这些放在一起，使用了以下函数…

第一个通过比较输入元素的边界客户矩形来检查输入元素是否完全呈现在用户屏幕上。

```
const isBottom = (el) => {
    return el.getBoundingClientRect().bottom <= window.innerHeight;
}
```

这在跟踪滚动回调函数中使用，以检查容器组件是否完全在屏幕上，并且当前没有加载其他项目。它通过 id 来查找元素，所以您必须确保为您的容器提供一个 id。

重要的是将 useCallback 函数传递给第二个参数`loading`和`pageNumber`，以便获取的页面是正确的，并且它将包含正确的`loading`值。

```
const trackScrolling = useCallback(() => {
    const el = document.getElementById('listing-container');
    if (isBottom(el) && !loading ) {
        fetchListings(pageNumber)(dispatch)
    }
},[pageNumber, dispatch, loading]);
```

需要一个 useEffect 函数来向文档添加 trackScrolling 函数，因为这是被滚动的元素。

它还应该返回一个函数来删除事件侦听器，这将在卸载组件时执行，或者如果传递给该函数的变量数组在两次呈现之间发生了变化。

与前面的函数类似，需要将`allLoaded`传递到这个变量数组中，以便在没有更多项目要加载的情况下不会附加事件侦听器。

```
useEffect(() => {
    if (!allLoaded) document.addEventListener('scroll', trackScrolling);
    return () => {
        document.removeEventListener('scroll', trackScrolling)
    };
},[trackScrolling, allLoaded, dispatch])
```

最后，可以添加一个额外的 useEffect 来重置`allLoaded`的值。

```
useEffect(() => {
    return () => dispatch(resetAllLoaded())
}, [dispatch])
```

## 所以为了呈现

由于对已经介绍过的函数进行了大量的改进，我选择将我的组件部分呈现如下:

```
<>
    <div id="listing-container" >
        <div className="row row-cols-1 row-cols-md-3 gx-3">
            {listings.map(listing=><Listing listing={listing} />)}
        </div>
    </div>
    {loading && <LoadingListing />}
</>
```

这里，来自商店的列表被映射以呈现它们各自的元素。

如果 loading 变量为 true，将呈现一个加载组件，如下所示。

![](img/04cbb06219c10d7830ad64e419528d33.png)

## 到无限和更远的地方…

实现这个功能并使用 React-Redux 的钩子函数编写它的挑战已经非常令人满意了。有了这些，我期待着进一步探索 React 的功能，并欢迎您的任何建议！

所有的源代码可以在我的 GitHub [前端](https://github.com/Shilcof/osiris-frontend)和[后端](https://github.com/Shilcof/osiris-backend)找到。

一定要让我知道你是如何在你的项目中使用这种技术的——我很想看到任何最终的结果！