# 使用 Flask、PyMongo 和 MongoDB 创建一个简单的战舰网页游戏

> 原文：<https://levelup.gitconnected.com/create-a-simple-battleship-web-game-using-flask-pymongo-and-mongodb-2baba714d19a>

![](img/848e08b69615ce94844f1110df2f0b04.png)

多梅尼科·洛亚在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 背景

战舰是一个令人兴奋的游戏，玩家将尝试选择放置船只的位置。飞船位置对玩家来说显然是隐藏的。在这个简单的游戏实现中，玩家将输入游戏网格的 X 和 Y 坐标。每次玩家没有击中隐藏的船，一个“X”将显示在游戏格子上。然而，如果玩家击中了隐藏的船，他/她将被重定向到另一个页面，在那里显示一个获胜横幅和玩家采取的行动的 X 和 Y 坐标列表。

本文将介绍使用 Python 编程语言、NoSQL 数据库和前端 HTML 网页运行这个游戏所需的源代码。

[](https://www.eduelk.com/) [## 教育

### 通过我们的练习题为您的下一次技术认证考试树立信心。我们提供课程来加速…

www.eduelk.com](https://www.eduelk.com/) [](https://www.eduelk.com/about-us) [## 关于我们-教育

### 在爱德克教育，我们相信熟能生巧。我们知道准备参加技术认证是…

www.eduelk.com](https://www.eduelk.com/about-us) [](https://www.eduelk.com/shop-for-practice-questions) [## 通过我们负担得起的练习题获得自信——教育

### 编辑描述

www.eduelk.com](https://www.eduelk.com/shop-for-practice-questions) [](https://www.eduelk.com/book-a-lesson) [## 联系方式 1 -教育

### 编辑描述

www.eduelk.com](https://www.eduelk.com/book-a-lesson) 

# 技术

web 应用程序游戏实现背后的技术如下:

*   码头工人
*   Python 3.6
*   瓶
*   MongoDB
*   皮蒙戈
*   超文本标记语言

本文假设您对所讨论的技术有一些基本的了解。

# Web 应用程序文件结构

因为我们使用 Flask 作为这个游戏的 web 应用程序框架，所以我们需要相应地构建文件和文件夹。以下是所需文件和文件夹的位置:

*   /templates/main.html
*   /templates/won.html
*   /static/winner.jpg
*   战舰. py
*   战舰 _web_app.py

# 战舰

这个 Python 文件由战舰游戏规则和逻辑组成。有一些函数将网格初始化为一个 4 行 5 列的数组，能够在控制台上显示网格，验证行和列以确保玩家的有效动作可以执行，并检查玩家是否成功击中了船。

# 弗拉斯克&皮蒙戈

这是托管 web 应用程序的文件，该应用程序由 Flask routes 和 PyMongo 函数组成。在此实现中，定义了两条路由。

其中一个是根网页，它创建隐藏船只的随机 X 和 Y 坐标，初始化网格，呈现 main.html 模板，并将网格作为参数传入。

另一个是执行计算的 POST 请求。从 HTML 表单中检索 X 和 Y 坐标数据。接下来，检查行和列有效性的条件语句。如果有效，PyMongo 将把 X 和 Y 坐标插入到集合中。checkResult 函数用于检查玩家是否击中了船只。如果玩家成功击中了船只，那么将呈现 won.html 以及 MongoDB 集合中的文档列表。然而，如果没有赢，那么 main.html 继续被渲染和显示。

# 超文本标记语言

以下是网页应用游戏中使用的 HTML 文件。Python 编程语言的 Jinja web 模板引擎允许将 Flask web 应用程序的参数呈现在 HTML 文件上。

# MongoDB

获取最新的 mongodb 图像:

```
docker pull mongo
```

从 mongodb 映像运行一个容器:

```
docker run -it -v `pwd`/mongodata/:/mongodata -p 27017:27017 --name mongodb -d mongo
```

在运行容器之后，web 应用程序能够与 MongoDB 服务器进行交互。

# 思想

好吧！感谢在源代码和附带的解释中经久不衰。我希望这篇文章能够帮助你们中的一些人构建自己的 web 应用程序游戏，或者寻求快速理解这些技术的概念。请查看我的 github 资源库，获取完整的源代码和结构:[https://github.com/leonardyeoxl/pymongo-flask-web-app](https://github.com/leonardyeoxl/pymongo-flask-web-app)。谢谢你，祝你愉快！和平✌️！

[](https://www.eduelk.com/) [## 教育

### 通过我们的练习题为您的下一次技术认证考试树立信心。我们提供课程来加速…

www.eduelk.com](https://www.eduelk.com/) [](https://www.eduelk.com/about-us) [## 关于我们——教育

### 在爱德克教育，我们相信熟能生巧。我们知道准备参加技术认证是…

www.eduelk.com](https://www.eduelk.com/about-us) [](https://www.eduelk.com/shop-for-practice-questions) [## 通过我们负担得起的练习题获得自信——教育

### 编辑描述

www.eduelk.com](https://www.eduelk.com/shop-for-practice-questions) [](https://www.eduelk.com/book-a-lesson) [## 联系方式 1——教育

### 编辑描述

www.eduelk.com](https://www.eduelk.com/book-a-lesson)