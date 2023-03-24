# Helm —从属关系

> 原文：<https://levelup.gitconnected.com/helm-dependencies-1907facbe410>

## 深入探究舵图的依赖性

![](img/554b1089f640e8854fec1e5ba5979746.png)

由 [Ilya P](https://unsplash.com/@swipt?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 helm 中，一个图表可以依赖于另一个图表。例如，WordPress 应用程序需要一个数据库才能开始运行。在 helm 中，我们可以将 WordPress 部署为父图表的一部分，将 MySQL 或任何其他所需的应用程序部署为父图表的依赖项。

舵图表将它们的依赖关系存储在**‘charts/’中。**将相关性图表添加到父图表有两种不同的方式:

1.  列出 **Chart.yaml** 文件中的所有依赖项，helm 会下载这些依赖项并存储在 **'charts/'** 目录**中。**
2.  在 **'charts/'** 目录**中手工填充依赖关系图。**

## 列出 **Chart.yaml** 文件中的依赖关系

在 **Chart.yaml** 文件中列出依赖关系之前，默认情况下 **Chart.yaml** 文件如下所示:

```
**# Chart.yaml**apiVersion: v2
name: webserver
description: A Helm chart for Kubernetes
type: application
version: 0.1.0
appVersion: "1.16.0"
```

现在，让我们看看如何将依赖项填充到 **Chart.yaml** 文件中:

```
dependencies:
- name: mysql
  version: "9.3.4"
  repository: "https://charts.bitnami.com/bitnami"
```

将依赖项添加到 **Chart.yaml** 文件后:

```
**# Chart.yaml**apiVersion: v2
name: webserver
description: A Helm chart for Kubernetes
type: application
version: 0.1.0
appVersion: "1.16.0"**dependencies:
- name: mysql
  version: "9.3.4"
  repository: "https://charts.bitnami.com/bitnami"**
```

使用简单的 helm 命令，我们可以从 dependencies 字段中定义的存储库中提取图表:

```
 helm dependency update **[CHART]** 
>> helm dependency update ~/webserver
```

上面定义的命令将生成`.webserver/Chart.lock`文件，并将所有依赖项下载到`.webserver/charts`目录中

`**Chart.lock**`文件列出了直接依赖项的确切版本以及它们的依赖项等等。

## 版本范围

我们可以定义一个版本范围，而不是定义一个图表的精确版本。Helm 允许我们用多种方式定义版本范围。

我们可以使用以下命令查看图表的所有可用版本:

```
 ** helm search repo [CHART] --versions**
>> helm search repo  mysql  --versions
```

**●连字符范围比较**

`version: 1.2 - 1.4.5`相当于`>= 1.2 <= 1.4.5`

```
dependencies:
- name: mysql
 **version: 9.0.0 - 9.9.9**
  repository: "https://charts.bitnami.com/bitnami"
```

Helm 将下载最新的 MySQL 图表，版本将在`>= 9.0.0 <= 9.9.9`范围内。在撰写本文时，最新的 MySQL chart 版本是 `9.4.1`。这就是为什么赫尔姆会拉`9.4.1`的版本。将版本定义为一个范围的主要优点是，如果在下一次更新或安装父图表时有新版本可用。Helm 将自动下载指定范围内可用的最新版本。

**●基本比较** 我们也可以使用基本比较运算符来定义版本。

```
version: "= 9.4.1" 

version: "!= 9.4.1" version: "> 9.4.1" version: "< 9.4.1"version: ">= 9.4.1"version: "<= 9.4.1"version: ">= 9.4.1 and < 10.0.0" 
```

**● Caret(^)范围比较(主要)** 脱字符(`^`)比较运算符用于出现稳定版本(1.0.0)时的主要级别变化。

```
^1.2.3  is equivalent to >= 1.2.3, < 2.0.0^1.2.x  is equivalent to >= 1.2.0, < 2.0.0   # x as a placeholder^2.3    is equivalent to >= 2.3, < 3^2.x    is equivalent to >= 2.0.0, < 3       # x as a placeholder^0      is equivalent to >= 0.0.0, < 1.0.0
```

**●波浪号(~)范围比较(Patch)** 波浪号(`~`)比较运算符用于指定次版本时的补丁级别范围，以及缺少次版本号时的主级别变化。举个例子，

```
~1.3.2  is equivalent to >= 1.3.2, < 1.4.0 ~3.4    is equivalent to >= 3.4, < 3.5~3      is equivalent to >= 3, < 4
```

## 启用或禁用依赖关系

根据用户的需要，可以启用或禁用依赖关系。如果相关性被禁用，则在图表安装期间，相应的相关性将被忽略。

有几种方法可以实现这一点。通过使用条件字段或标签字段。

**●使用条件** 我们可以在**parent chart/charts . YAML**文件中定义依赖关系的同时定义一个条件字段。

```
# **parentChart/Charts.yaml**dependencies:
- name: mysql
  version: "9.3.4"
  repository: "https://charts.bitnami.com/bitnami" **condition: mysql.enabled**
```

只有当用户在父 **values.yaml** 文件中设置`**mysql.enabled**`为**真**时，才会安装上述定义的依赖关系。

```
# **parentchart/values.yaml
...
...**mysql:
 **enabled: false**
```

由于`**mysql.enabled**` 被定义为 **false** ，那么在安装过程中 MySQL 的依赖关系将被忽略。

**●使用标签** 假设 **Charts.yaml** 文件中定义了多个依赖关系。要根据我们的需要启用或禁用它，我们必须做一些类似的事情:

```
# **parentchart/Charts.yaml**dependencies:
- name: mysql
  version: "9.3.4"
  repository: [https://charts.bitnami.com/bitnami](https://charts.bitnami.com/bitnami)
  **condition: mysql.enabled**- name: mongodb
  version: 13.3.0
  repository: [https://charts.bitnami.com/bitnami](https://charts.bitnami.com/bitnami)
  **condition: mongodb.enabled**
```

并且 **values.yaml** 文件将看起来像下面的演示:

```
# **parentchart/values.yaml
....
....**mysql:
 **enabled: true**
mongodb
 **enabled: false**
```

如果我们使用`tags`字段，我们可以做得更简单。标签字段是与图表相关联的标签的 YAML 列表。**通过指定标签和布尔值**，可以启用或禁用所有带标签的图表。让我们看看如何做到这一点:

首先，我们来看看如何在父级的 **values.yaml** 文件中定义**标签**:

```
# **parentchart/values.yaml
...
...**tags:
 **enabled: false**
```

从父文件 **Chart.yaml** 的依赖块中删除`**condtion**`字段。此时我们将使用`**tags**`而不是`**condtion**`。

```
# **parentchart/Charts.yaml**dependencies:
- name: mysql
  version: "9.3.4"
  repository: [https://charts.bitnami.com/bitnami](https://charts.bitnami.com/bitnami)- name: mongodb
  version: 13.3.0
  repository: [https://charts.bitnami.com/bitnami](https://charts.bitnami.com/bitnami)
 **tags: 
    - enabled**
```

在上面的演示中，我们已经用标签定义了 **MongoDB** 依赖关系。因为 tags 字段下定义的值是一个布尔值，并且该值在父项的 **values.yaml** 文件中定义为 **false** 。因此，当我们安装父图表时， **MongoDB** 将被忽略。与 MongoDB 相反， **MySQL** 依赖没有定义`**condtion**`或`**tags**` a。这就是为什么 **MySQL** 将作为依赖关系图安装。

## 列出依赖关系

我们可以列出给定图表的所有依赖项。

```
 helm dependency list  **[CHART]**
>> helm dependency list  ~/webserver/--------------------------------------------------------------
NAME    VERSION REPOSITORY                             **STATUS** 
mysql   9.3.4   [https://charts.bitnami.com/bitnami](https://charts.bitnami.com/bitnami)      ok     
mongodb 2.0.3   [https://charts.bitnami.com/bitnami](https://charts.bitnami.com/bitnami)    **  missing**
```

在上面的演示中，我们可以看到 MySQL 的状态是“ok ”, MongoDB 的状态是“missing”。原因是 MySQL 图表已经从存储库中取出并存储在**‘chart/’**目录中。而 MongoDB 图表不可用。通过执行**依赖关系更新**命令，所有缺失的依赖关系都可以被取出并存储。

> *如果你觉得这篇文章很有帮助，请不要忘记***点击* ***跟随*** *👉******拍手*** *👏* *按钮帮助我写更多这样的文章。
> 谢谢🖤****

## ****舵图上的所有物品-****

**[掌舵—操作中](https://faun.pub/helm-in-action-652abc10aa1c)
[掌舵—高级命令](https://medium.com/geekculture/helm-advanced-commands-9365097475b)
[掌舵—创建您的第一张图表](https://medium.com/faun/helm-create-your-first-chart-f3aa304c3544)
[掌舵—模板操作、功能和管道](https://medium.com/faun/helm-template-actions-functions-and-pipelines-16ed23ed336f)
[掌舵—流程控制](/helm-flow-control-a085a67f22e)
[掌舵—变量](https://medium.com/codex/helm-variables-df1dca52ed46)
[掌舵—命名模板](/helm-named-templates-de2efc3875d0)
[掌舵—依赖关系](https://medium.com/gitconnected/helm-dependencies-1907facbe410)**

# **分级编码**

**感谢您成为我们社区的一员！在你离开之前:**

*   **👏为故事鼓掌，跟着作者走👉**
*   **📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容**
*   **🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)**

**🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)**