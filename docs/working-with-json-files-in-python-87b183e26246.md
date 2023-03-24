# 在 Python 中使用 JSON 文件

> 原文：<https://levelup.gitconnected.com/working-with-json-files-in-python-87b183e26246>

# JSON 是什么？

JSON (JavaScript 对象符号)是一种独立于语言的文件格式，源自 JavaScript。它用于交换数据，特别是在互联网上的客户机和服务器之间。

JSON 文件由扩展名`.json`标记。

> J SON 格式类似于 Python 字典。

```
{
  **"Name"**: "Ashish",
  **"age"**: 26,
  **"address"**: {
    **"street"**: "9, Mall Street",
    **"city"**: "London",
    **"county"**: "London",
    **"postCode"**: "HS3 2WS"
  }
}
```

为了在 Python 中处理 JSON 文件，我们将使用`json`库。

![](img/9fa198d56a518b31648b36343b044498.png)

奥斯卡·伊尔迪兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 导入库

该库可以导入为:

```
import json
```

# 创建 JSON 文件

要创建一个名为`my_details.json`的 JSON 文件，下面的语法可以和`.dump()`方法一起使用:

假设我们有一本叫做`my_details`的字典

```
my_details = {
  **"name"**: "Ashish",
  **"age"**: 26,
  **"address"**: {
    **"street"**: "9, Mall Street",
    **"city"**: "London",
    **"county"**: "London",
    **"postCode"**: "HS3 2WS"
  }
}
```

要创建一个名为`my_details.json`的 JSON 文件，下面的语法可以和`.dump()`方法一起使用。

```
with open('my_details.json', '**w**') as my_details_json:
    json.dump(my_details, my_details_json)#w = write
```

这里的`open`函数用来创建一个可写的数据流。

![](img/e2d7e327193a1a078aafdafcdc2c17ce.png)

照片由[肯尼·埃利亚松](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 读取 JSON 文件

要读取一个名为`my_details.json`的 JSON 文件，下面的语法可以与`.load()`方法一起使用:

```
with open('my_detail.json', '**r**') as my_details_json:
    my_details = **json.load**(my_details_json)#r = read 
```

这将创建一个名为`my_details`的字典来保存来自 JSON 文件`my_detail.json`的数据。

这里的`open`函数用于创建一个可读的数据流。

![](img/ea754e3b0e3b4a3e1565ad50fb74e3de.png)

micha Parzuchowski 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 将 Python 转换为 JSON 字符串

为了将 Python 字典转换成 JSON 格式的字符串，使用了`.dumps()`方法而不是`.dump()`方法。

`.dumps()`方法不允许你用这个数据创建一个`.json`文件。

```
details_dict = {
  "name": "Ashish",
  "age": 34,
  "country": "UK"
}details_json = json.dumps(details_dict)
```

*感谢阅读！*

[](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium-Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)