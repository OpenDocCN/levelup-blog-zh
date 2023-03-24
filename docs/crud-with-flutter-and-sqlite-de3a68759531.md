# 带有 Flutter 和 SQLite 的 CRUD

> 原文：<https://levelup.gitconnected.com/crud-with-flutter-and-sqlite-de3a68759531>

![](img/57cc2afab0c514a4c7c9eab787711ed1.png)

沙哈达特·拉赫曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

您是否正在寻找一种方法，在您的 Flutter 项目中使用移动设备存储作为数据库？只要集成一个 SQLite 包就可以了。本文将提供使用 SQLite 和 Flutter 进行 CRUD 操作的完整指南。我们开始吧！

> 下载[笔记-简易记事本](https://play.google.com/store/apps/details?id=com.dinno.notes):与 SQLite 集成的全功能应用。

![](img/730cdbecf0f5c7060f7e011d81c4da74.png)

## **第一步:添加依赖关系**

在项目的 pubspec.yaml 文件中添加 sqflite 和 path_provider 包。

*   **sqflite** :集成 SQLite 数据库功能的 SQLite 包。(不要拼错单词“sqflite”)
*   **path_provider:** 指定数据库路径的包。

## 步骤 2:创建数据模型

创建数据模型，展示我们希望在数据库内部操作的数据结构。为此，我们应该创建一个具有所需属性的类和一个在插入数据库之前映射数据的方法。

## 步骤 3:添加导入

将所需的导入添加到数据库助手文件中。

## 步骤 4:初始化数据库

声明一个使用上述导入来打开数据库连接的函数。

## 第五步:插入

使用 **insert()** helper 方法创建一个函数来获取数据模型，将其转换为地图并将数据插入数据库。

## 第六步:阅读

使用 sqflite，我们可以通过在 **query()** helper 中使用 *where、groupBy、having、orderBy* 和 *columns* 等参数以多种方式查询数据。

## 第七步:删除

使用 **delete()** 助手中的 *where* 参数从表中删除特定的行。

> *记住使用* whereArgs *将参数传递给* where *语句，以防止 SQL 注入攻击。*

## 第八步:更新

使用 **update()** 助手更新数据库中的任何记录。要更新特定的记录，使用 *where* 参数。

现在我们可以走了！将上述方法集成到您的项目中，使您的项目完全具备 SQLite 数据库和 CRUD 操作的功能。如果在这个过程中你仍然觉得困难，用下面的代码片段测试上面的方法。

我猜你很幸运地找到了一个完整的使用 SQLite 和 Flutter 的指南。在此找到完整的代码[。感谢阅读，等待更多牛逼文章。在那之前，编码快乐！💻](https://github.com/Dulanka-K/flutter_sqflite)