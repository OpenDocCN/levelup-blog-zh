# 使用带有 React SWR 的 CRUD 操作来改变 REST API 缓存

> 原文：<https://levelup.gitconnected.com/using-crud-operations-with-react-swr-for-mutating-rest-api-cache-3e0d01774aed>

![](img/ce82a7d4a8428ba0788dacab38dcd479.png)

弗兰基·查马基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

*在 Twitter 上关注我，免费获得这篇文章和其他文章:*[*@ N*ot _ the author](https://twitter.com/Not_TheAuthor)

# **用于发出获取请求的 SWR**

Vercel 过去已经开发了一些很棒的库和框架，所以 SWR 库会有所不同也就不足为奇了。我将向您展示如何使用 Vercel 的 SWR 库从 REST API 中获取和操作数据。这篇文章对 Vercel 库做了一个快速的概述，但是如果你想了解更多关于这个库以及它是如何工作的，你可以在这里阅读完整的文档。

[](https://swr.vercel.app/) [## SWR: React 钩子获取数据

### React 钩子库获取数据“SWR”这个名字来源于 stale-while-revalidate，一个 HTTP 缓存失效…

应用程序](https://swr.vercel.app/) 

# **什么是 SWR？**

SWR 背后的理念代表着在重新验证时过时，在文件中是这样定义的。SWR 是一种策略，首先从缓存返回数据(陈旧)，然后发送获取请求(重新验证)，最后获得最新数据那么这和 CRUD 有什么关系呢？如果您不知道 CRUD 是对数据执行的一组操作，它是创建、读取、更新和删除的简写。默认情况下，SWR 将通过返回获取请求的结果来为您执行读取部分。但是如果您想要扩展它，您将不得不从该请求中改变缓存。这就是为什么我创建了一个 useCrud 钩子来帮助我们做到这一点。我还加入了 Typescript，以确保在更新缓存时使用正确的键，因此您也需要进行设置。

# **设置事物**

所以第一件事是安装 SWR，运行:

```
npm install swr
or
yarn add swr
```

这将把 SWR 图书馆添加到您的项目中。接下来，我们将为我们的应用程序添加一个配置提供程序。当我们发出请求时，这将为 SWR 提供全局配置。我有一个上下文文件夹，在那里我存储这样的上下文。

```
import * as React from ‘react’
import { SWRConfig } from ‘swr’const swrConfig = {
 revalidateOnFocus: false,
 shouldRetryOnError: false
}export const SWRConfigurationProvider: React.FC = ({ children }) => <SWRConfig value={swrConfig}>{children}</SWRConfig>
```

这将需要围绕你的应用程序根，对我来说是在 pages/_app.tsx 文件中，因为我使用 NextJS，但它可以在另一个框架中工作，如 Gatsby，只要它在全局范围内包装你的应用程序。您可以根据项目需要随意更改设置。

# 你准备好阅读一些数据了吗？

现在我们需要开始实现 fetch，这将构成钩子的基础。这里有一个在 SWR 如何抓取的例子。

```
const fetcher = useCallback(
 async (url: string) => {
 const response = await fetch(url)
 return response as T[]
 },
 []
 )const { data, error, isValidating, mutate } = useSWR(url, fetcher, {
 …fetchOptions
 })
```

useSWR 挂钩非常简单，它接受一个 url 和一个“获取器”,获取器是执行请求的函数。url 被传递给获取器以发出请求，您也可以提供一些漂亮的选项。SWR 会返回一些东西给你首先是返回的数据，一个错误状态(如果有的话)，一个变异函数，和一个 isValidating 布尔值，它会告诉你数据是否是新的。您可以将 isValidating 标志视为加载指示器；这不是一回事，但对我来说是一回事。

在放置定制钩子的地方创建一个 use-crud.tsx 文件，并将其添加到 start。

```
import useSWR, { ConfigInterface } from ‘swr’
import { useCallback } from ‘react’// T is the response type
// K is the request type which defaults to T
export function useCrud<T, K = T>(url: string, key: keyof T, fetchOptions?: ConfigInterface) {
 const fetch = useCallback(
 async (url: string) => {
 const response = await fetch(url)
 return response as T[]
 },
 []
 )const { data, error, isValidating, mutate } = useSWR(url, fetch, {
 …fetchOptions
 })return {
 fetch: {
 data,
 error,
 loading: isValidating,
 mutate
 }
 }
}
```

# 使其对用户友好

稍后我将介绍参数和类型，但现在您需要知道的是，我们将能够向这个钩子传递一个 url，它将为我们提供数据和方法来对这些数据执行 CRUD 操作。我遇到了一个问题。有时我的应用程序响应太快，因为我们有缓存的数据可以依靠，所以我添加了一个加载状态和超时，使请求至少需要半秒钟。这将改善用户体验。

```
import { useCallback, useEffect, useState } from ‘react’
import useSWR, { ConfigInterface } from ‘swr’// T is the response type
// K is the request type which defaults to T
export function useCrud<T, K = T>(url: string, key: keyof T, fetchOptions?: ConfigInterface) {
const [loading, setIsLoading] = useState(true)const loadingTimeout = () => {
 setIsLoading(false)
 }const fetch = useCallback(
 async (url: string) => {
 const response = await fetch(url)
 return response as T[]
 },
 []
 )const { data, error, isValidating, mutate } = useSWR(url, fetch, {
 …fetchOptions
 })useEffect(() => {
 if (isValidating) {
 setIsLoading(true)
 return
 }setTimeout(loadingTimeout, 500)
 }, [isValidating])return {
 fetch: {
 data,
 error,
 loading,
 mutate
 }
 }
}
```

我需要提一下 SWR 的一个小怪癖。当请求中没有数据时，返回一个空对象；这不是我真正想要的，所以我增加了一个额外的步骤来检查数据是否为空。为此，我将使用 lodash，如果您还没有安装它，请继续安装。如果对象是空的，我将返回一个空数组，更新你的导入来添加这个。

```
import { isArray, isEmpty } from ‘lodash’
import { useCallback, useEffect, useMemo, useState } from ‘react’
```

稍后我们将需要 isArray 方法进行 CRUD 操作，并且我们将记住数据检查的结果。将此添加到 return 语句的上方。

```
const memoizedData = useMemo(() => (!isEmpty(data) ? data : []), [data])
```

然后返回 memoizedData 而不是 Data。

```
return {
 fetch: {
 data: memoizedData,
 error,
 loading,
 mutate
 }
 }
```

# 我在那里做了什么

现在，您一直在等待的时刻到了，我们将开始修改数据，但在此之前，让我解释一下这个函数的 Typescript 参数。T 泛型类型是我们期望得到的数据类型，K 泛型类型是我们将用来执行创建操作的数据类型。在大多数情况下，这将是相同的，但如果我们需要在发送数据之前对其执行一些操作，我们将使用不同的类型。如你所见，如果我们不传递任何东西，它默认为 T。参数中的键是 T 类型的键，这意味着可以使用该类型上的任何属性，但是我们需要告诉 typescript 索引键是什么，以便我们可以从 fetch 中改变缓存的数据。创建操作将如下所示。

```
const create = useCallback(
 async (newObject: K, shouldRevalidate = false) => {
 const response = await fetch(url, {
 body: newObject,
 method: ‘POST’
 })const result = response as Tif (data && mutate) {
 let newData = data
 if (isArray(data)) {
 newData = data.concat(result)
 }await mutate([…new Set(newData)], shouldRevalidate)
 }return result
 },
 [url, data, mutate]
 )
```

# **两个比一个好**

这将在我们的 url post 方法中创建一个新对象。如果我们有数据，它将改变它的缓存，如果我们没有，我们将只返回 post 的结果。还有一个额外的检查，看看数据是否是一个数组，如果是，我们将添加新的对象到数据数组，如果不是，我们将添加一个新的数据集，并跳过重新验证。我继续添加了一个用于重新验证的参数，如果我们需要新数据而不仅仅是缓存，可以覆盖这个参数。这将调用我们之前得到的 mutate 函数，并允许我们用新数据改变缓存，并返回新数组应该是什么样子的乐观响应；所有这些都不需要再次获取数据。但是这个方法只能创建一个实例，所以我们也需要一个来创建多个对象。

```
const createMultiple = useCallback(
 async (newObjects: K[], shouldRevalidate = false) => {
 const response = await fetch(url, {
 body: newObjects,
 method: ‘POST’
 })const result = response as T[]if (data && mutate) {
 await mutate([…data, …result], shouldRevalidate)
 }return result
 },
 [url, data, mutate]
 )
```

# 给我 D

这个单独的方法将处理创建多个对象。一个改进是将这些结合起来，但这也是本教程的目的。接下来，我们将处理 CRUD 的移除操作。函数应该是这样的。

```
const remove = useCallback(
 async (body: number, shouldRevalidate = false) => {
 const response = await fetch(url, {
 body,
 method: ‘DELETE’
 })
 const result = response as Tif (data && mutate) {
 if (isArray(result)) {
 const updatedObjects = […data].filter((current) => {
 const isDeleted = result.find((result) => result[key] === current[key])
 return !isDeleted
 }) await mutate(result.length === 0 ? [] : updatedObjects, shouldRevalidate)
 } else {
 const deletedIndex = data.findIndex((object) => object[key] === result[key])if (deletedIndex >= 0) {
 const updatedObjects = […data]
 updatedObjects.splice(deletedIndex, 1) await mutate(updatedObjects, shouldRevalidate)
       }
    }
 }return result
 },
 [url, data, key, mutate]
 )
```

这将为您正在修改的键取一个数字，这样您就可以从最初获取的数据中得到这个数字，并根据您要删除的项解析它。如果这个操作的结果是一个数组，那么我们将在数据中找到与键匹配的每一项，并将其从列表中删除。否则，我们必须找到被删除对象的索引，如果它在列表中，则删除该索引。有一点很重要，每个请求都应该返回被操作对象的值，这样我们就可以更新缓存。移除多个对象非常相似。

```
const removeMultiple = useCallback(
 async (ids: number[], shouldRevalidate = false) => {
 const response = await fetch(url, {
 body: ids,
 method: ‘DELETE’
 })
 const results = response as T[]if (data && mutate) {
 const updatedObjects = […data].filter((current) => {
 const isDeleted = results.find((result) => result[key] === current[key])
 return !isDeleted
 }) await mutate(updatedObjects, shouldRevalidate) return results
       }
   },
 [url, data, key, mutate]
 )
```

# 你知道接下来会发生什么

CRUD 的更新部分略有不同，因为如果被更新的行没有不同，SQL server 可能会抛出错误。对于这一点，你可能应该在前端进行一些验证，以确保不会发生这种情况，但为了以防万一，我将在这里使用我偷来的方法进行检查。创建一个名为 get-object-difference.ts 的助手方法，放在一个容易访问的地方。

```
/*
 * Compare two objects by reducing an array of keys in obj1, having the
 * keys in obj2 as the initial value of the result. Key points:
 *
 * — All keys of obj2 are initially in the result.
 *
 * — If the loop finds a key (from obj1, remember) not in obj2, it adds
 * it to the result.
 *
 * — If the loop finds a key that are both in obj1 and obj2, it compares
 * the value. If it’s the same value, the key is removed from the result.
 */
export function getObjectDifference(obj1: any, obj2: any) {
 const diff = Object.keys(obj1).reduce((result, key) => {
 if (!obj2.hasOwnProperty(key)) {
 result.push(key)
 } 
 return result
 }, Object.keys(obj2))return Object.fromEntries(
 diff.map((key) => {
 return [key, obj2[key]]
 })
 )
}
```

此方法将返回两个对象之间差异的对象，否则如果没有，它将返回一个空对象。继续将它导入 useCrud 文件并添加更新方法。

```
const update = useCallback(
 async (updatedObject: T, shouldRevalidate = false): Promise<T> => {
 const currentObjectIndex = data.findIndex((object) => object[key] === updatedObject[key])
 const currentObject = data[currentObjectIndex]
 const diff = currentObject ? getObjectDifference(currentObject, updatedObject) : nullif (!diff) {
 throw new Error(‘Update Failed’)
 }if (isEmpty(diff)) {
 return currentObject
 }const response = await fetch(url, {
 body: { …diff, id: updatedObject[key] },
 method: ‘PATCH’
 })if (data && mutate) {
 const updatedObjects = […data]
 updatedObjects.splice(currentObjectIndex, 1, response)
 await mutate(updatedObjects, shouldRevalidate)
 }return response as T
 },
 [url, data, mutate, key]
 )
```

这将检查您正在修改的当前对象的缓存，并获得旧对象和新对象之间的差异。如果当前对象不在缓存中，它将抛出一个错误。否则，如果没有差异，它将只返回当前对象，而不执行获取补丁的请求。如果有差异，它会将差异和更新对象的 id 作为您之前在更新对象上指定的任何键进行传递。然后，它将继续对缓存的数据执行 mutate，更新多个对象略有不同。

```
const updateMultiple = useCallback(
 async (updatedObjects: T[], shouldRevalidate = false): Promise<T[]> => {
 const currentObjects = data.filter((object) => updatedObjects.find((updated) => object[key] === updated[key]))if (!currentObjects || currentObjects <= 0) {
 throw new Error(‘Update Failed’)
 }const diffs = currentObjects.map((currentObject) => {
 const updatedObject = updatedObjects.find((updated) => updated[key] === currentObject[key])
 return { …getObjectDifference(currentObject, updatedObject), id: updatedObject[key] }
 })if (diffs.length <= 0) {
 return currentObjects
 }const response = await fetch(url, {
 body: { …diffs },
 method: ‘PATCH’
 })if (data && mutate) {
 const updatedObjects = […data].map((current) => {
 if (current[key] === response[key]) {
 return response
 } return current
 }) await mutate(updatedObjects, shouldRevalidate)
 }return response as T[]
 },
 [url, data, mutate, key]
 )
```

这将在所有对象上运行差异检查，而不是在主体中传递一个对象差异数组。当然，所有这些实现都是特定于我的 API 路由的，但是它们可以很容易地被修改以适合您的用例。

# 结束这节拼写课

唷！如果你做到了这一步，我欠你一杯酒，但因为我现在不能给你买一杯，我会给你完整的代码。

```
import { isArray, isEmpty } from ‘lodash’
import { useCallback, useEffect, useMemo, useState } from ‘react’
import useSWR, { ConfigInterface } from ‘swr’
import { getObjectDifference } from ‘../where-ever-you-put-this-earlier’// T is the response type
// K is the request type which defaults to T
export function useCrud<T, K = T>(url: string, key: keyof T, fetchOptions?: ConfigInterface) {
const [loading, setIsLoading] = useState(true)const loadingTimeout = () => {
setIsLoading(false)
}const fetch = useCallback(
async (url: string) => {
const response = await fetch(url)
return response as T[]
},[])const { data, error, isValidating, mutate } = useSWR(url, fetch, {…fetchOptions})useEffect(() => {
if (isValidating) {
setIsLoading(true)
return
}setTimeout(loadingTimeout, 500)}, 
[isValidating])const create = useCallback(
async (newObject: K, shouldRevalidate = false) => {
const response = await fetch(url, {
body: newObject,
method: 'POST'
})const result = response as T
if (data && mutate) {
let newData = data
if (isArray(data)) {
newData = data.concat(result)
} await mutate([…new Set(newData)], shouldRevalidate)
  } return result
},[url, data, mutate])const createMultiple = useCallback(async (newObjects: K[], shouldRevalidate = false) => {
const response = await fetch(url, {
body: newObjects,
method: 'POST'
})const result = response as T[]
if (data && mutate) {
await mutate([…data, …result], shouldRevalidate)}
return result
},[url, data, mutate])const remove = useCallback(async (body: number | unknown, shouldRevalidate = false) => {
const response = await fetch(url, {
body,
method: 'DELETE'
})const result = response as T
if (data && mutate) {
if (isArray(result)) {
const updatedObjects = […data].filter((current) => {
const isDeleted = result.find((result) => result[key] === current[key])
return !isDeleted
})await mutate(result.length === 0 ? [] : updatedObjects, shouldRevalidate)
} else {const deletedIndex = data.findIndex((object) => object[key] === result[key])
if (deletedIndex >= 0) {
const updatedObjects = […data]
updatedObjects.splice(deletedIndex, 1) await mutate(updatedObjects, shouldRevalidate)
      }
   }
}return result
},[url, data, key, mutate])const removeMultiple = useCallback(async (ids: number[], shouldRevalidate = false) => {
const response = await fetch(url, {
body: ids,
method: 'DELETE'
})const results = response as T[]
if (data && mutate) {
const updatedObjects = […data].filter((current) => {
const isDeleted = results.find((result) => result[key] === current[key])
return !isDeleted
}) await mutate(updatedObjects, shouldRevalidate) return results
   }
},
[url, data, key, mutate])const update = useCallback(async (updatedObject: T, shouldRevalidate = false): Promise<T> => {const currentObjectIndex = data.findIndex((object) => object[key] === updatedObject[key])const currentObject = data[currentObjectIndex]
const diff = currentObject ? getObjectDifference(currentObject, updatedObject) : nullif (!diff) {
   throw new Error(‘Update Failed’)
}if (isEmpty(diff)) {
   return currentObject
}const response = await fetch(url, {
body: { …diff, id: updatedObject[key] },
method: 'PATCH'
})
if (data && mutate) {
const updatedObjects = […data]
updatedObjects.splice(currentObjectIndex, 1, response)
  await mutate(updatedObjects, shouldRevalidate)
}
return response as T
},[url, data, mutate, key])const updateMultiple = useCallback(async (updatedObjects: T[], shouldRevalidate = false): Promise<T[]> => {
const currentObjects = data.filter((object) => updatedObjects.find((updated) => object[key] === updated[key]))if (!currentObjects || currentObjects <= 0) {
  throw new Error(‘Update Failed’)
}const diffs = currentObjects.map((currentObject) => {
  const updatedObject = updatedObjects.find((updated) => updated[key] === currentObject[key])return { …getObjectDifference(currentObject, updatedObject), id:   updatedObject[key] }
})if (diffs.length <= 0) {
  return currentObjects
}const response = await fetch(url, {
body: { …diffs },
method: 'PATCH'
})if (data && mutate) {
  const updatedObjects = […data].map((current) => {
   if (current[key] === response[key]) {
  return response
}
  return current
}) await mutate(updatedObjects, shouldRevalidate)
}
  return response as T[]
},[url, data, mutate, key])const memoizedData = useMemo(() => (!isEmpty(data) ? filterDeleted<T>(data) : []), [data])return {
    create,
    createMultiple,
    fetch: { data: memoizedData, error, loading, mutate },
    remove,
    removeMultiple,
    update,
    updateMultiple
  }
}
```

# **结论**

恭喜你已经完成了本教程，这个钩子应该给你提供了使用自定义 restful API 执行 CRUD 操作所需的所有功能。这个实现是特定于我的 API 的，所以您可能需要根据您的使用目的修改它，但是它足够通用，可以在大多数情况下使用。感谢你的加入，我希望你喜欢这些垃圾。