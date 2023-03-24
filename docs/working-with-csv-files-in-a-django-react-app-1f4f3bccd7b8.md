# 在 django-react 应用程序中使用 CSV 文件

> 原文：<https://levelup.gitconnected.com/working-with-csv-files-in-a-django-react-app-1f4f3bccd7b8>

![](img/f2545956633d65013a9b4b0db01a19a2.png)

费伦茨·阿尔马西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

有时，您不希望(或不需要)所有数据集都有一个数据库模型，尤其是当它们保持静态时。您可能想简单地提供一些数据集供 webapp /站点的前端使用。如果你使用 react，你不能像使用模板模型那样容易地将数据插入渲染。这里有一个简短的帖子，展示了在使用 django-react 设置时如何简单地管理这个问题。

# **选项 1:直接加载到 React 组件中**

到目前为止最简单的选择。但是，这涉及到将数据直接加载到前端。如果您需要在提供数据集之前运行一些模型或操作，这不是理想的选择。

只需将下面一行添加到 React 组件的导入中:

```
import data from "path-to-file"
```

此处的文件路径应该相对于组件文件的位置。注意，您将需要一个 [csv 加载器](https://www.npmjs.com/package/csv-loader)来以这种方式加载 csv 文件。我更愿意通过使用 json 来避免这种情况。使用 python 的 pandas 包从 csv 转换为 json:

```
import pandas as pddf = pd.read_csv('path-to-csv')
df.to_json('json-filename.json', orient='records')
```

# **选项 2:创建一个视图，该视图加载数据集并将其提供给 API**

这是我喜欢的路线。在“views.py”文件中，您可以简单地将数据集加载到 pandas dataframe 中，转换为 json 并提供对象作为响应。这里有一小段:

```
# views.pyimport pandas as pd
import json
from rest_framework.decorators import permission_classes
from rest_framework import permissions
from rest_framework.views import APIView
from rest_framework.response import Response@permission_classes((permissions.AllowAny,))
class InsertReleventNameView(APIView):
    def get(self, request, *args, **kwargs):
        df = json.loads(pd.read_csv(“path-to-data-file”).to_json(orient=’records’)) return Response(df)#urls.pyfrom django.urls import path
from .views import InsertReleventNameViewurlpatterns += path(‘insert-your-url’, InsertReleventNameView.as_view())
```

最后，要通过 React 组件访问数据，只需发出一个 fetch 请求(通常在 useEffect 钩子内):

```
// inside your react functional component:const [data, setData] = useEffect();useEffect(() => {
    const getData = async () => {
        try {
            const res = await axios.get(‘/insert-your-url’);
            setData(res.data)
        }
        catch (err) {
        }
    }
    getData();
}, []);
```

这里，我使用 axios 请求包从 api url 获取数据集，并将数据存储在 state 中。现在，我可以随心所欲地使用我的数据集，而无需设置和加载数据库模型的麻烦。

尽情享受吧！