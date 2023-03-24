# helm—命名模板

> 原文：<https://levelup.gitconnected.com/helm-named-templates-de2efc3875d0>

## 深入研究部分模板或子模板

![](img/cb6ef067526cbf8d8dbe83b5e6f14cfd.png)

[边境摄影师](https://unsplash.com/@borderpolarphotographer?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 概观

我们可以在 helm 中创建部分或子模板，主要称为“命名模板”。一个**命名的模板**只是一个在文件中定义并给定名称的模板。

我们可以把命名模板看作函数。命名模板将允许我们在整个舵图中重用语法或逻辑。

通常，helm 图表中的 **templates** 目录下的文件包含创建 Kubernetes 清单的模板。但是文件名以下划线(_) 开头的文件被认为没有清单。这些文件不作为 Kubernetes 对象定义呈现，但在其他图表模板中随处可见。

因此，文件命名约定如下:

```
**_filename.tpl**e.g.**_helpers.tpl**
```

**。tpl** 扩展名被广泛使用，因为文件只包含模板。

## 声明“命名模板”并将其嵌入到其他模板中:

我们可以在前面创建的名为 **_helpers.tpl** 的模板文件中定义一个命名模板。要定义一个模板我们必须使用`**define**` 关键字。由于模板名称是全局的，如果两个模板用相同的名称声明，就有可能发生冲突。因此，维护命名约定是避免重复的明智之举。一个流行的命名约定是在每个定义的模板前面加上图表的名称，例如— `**<CHART_NAME>.<things_the_template_will_do>**`

```
{{- **define** "webserver.selectorLabels" -}}
# body of template here{{- end }}
```

`define`函数应该有一个简单的文档块(`{{/* ... */}}`)描述它们做什么:

```
{{/*
Selector labels
*/}}
{{- **define** "webserver.selectorLabels" -}}
# body of the template here
{{- end }}
```

要在普通模板中嵌入一个命名模板，我们可以使用`**template**` 动作或者`**include**` 函数。

## 模板作用

让我们定义一个命名模板，看看如何在普通模板文件中使用它。

```
# filename:  _helpers.tpl{{/*
Common labels
*/}}
{{- define **"webserver.labels"** -}}
    app: nginx
    generator: helm
{{- end }}
```

将`**webserver.labels**`命名的模板嵌入到普通的模板文件中。(仅显示相关的 YAML)

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webserver-deployment
  labels:
  **{{- template "webserver.labels" }}**
```

现在，如果我们稍微修改一下`**webserver.labels**` 命名的模板，我们会发现一个不同的方面:

```
# filename:  _helpers.tpl{{/*
Common labels
*/}}
{{- define "webserver.labels**"** -}}
    app: nginx
    **generator: {{ .Release.Service }}**
{{- end }}
```

修改后，如果我们像以前一样嵌入`**webserver.labels**` :

```
{{- template "webserver.labels" }}
```

可惜行不通。我们必须在调用命名模板时传递一个作用域。因为我们已经用`**{{ .Release.Service }}**` 对象里面的`**webserver.labels**`命名了模板。由于我们没有传递任何作用域，在命名模板中我们无法访问`.`中的任何内容。换句话说，我们将无法访问当前范围之外的任何内容。解决这个问题并不难。我们只需要向模板传递一个范围:

```
{{- template "webserver.labels" **.** }}
```

**弊端:** 随着`**template**` 动作，不允许使用管道。

```
**# It will not work**
{{- template "webserver.labels" **. | nindent 4** }}
```

要在将命名模板嵌入普通模板时使用管道，我们必须使用`**include**` 函数。

```
{{- include "webserver.labels" .| nindent 4 }}
```

## 包括功能

要使用`**include**`函数将命名模板嵌入普通模板，我们必须传递两个参数。

> *1。命名模板的名称。
> 2。对象范围。*

```
{{- include "webserver.labels" .}}
```

正如我们之前讨论的，我们可以将管道与`**include**`函数一起使用。

```
{{- include "webserver.labels" . | nindent 4 | qoute}}
```

也可以将一个命名模板嵌入另一个命名模板:

```
{{/*
Common labels
*/}}
{{- **define** "webserver.labels**"** }}
{{- **include** "webserver.selectorLabels" . }}
# body of template here
{{- end }}{{/*
Selector labels
*/}}
{{- **define** "webserver.selectorLabels**"** }}
# body of template here
{{- end }}
```

## 演练:

让我们编写两个命名模板，并在将生成 Kubernetes 清单的部署模板中使用它们。

以下是 **_helpers.tpl** 文件的片段:

```
{{/*
**Common labels**
*/}}
{{- define "**webserver.labels**" -}}
{{- include "webserver.selectorLabels" . }}
app.kubernetes.io/managed-by: {{ .Release.Service }}
{{- end }}{{/*
**Selector labels**
*/}}
{{- define "**webserver.selectorLabels**" -}}
app: {{ .Chart.Name }}
{{- end }}
```

我们将把上面显示的两个命名模板嵌入到一个部署模板文件中，下面是 **deployment.yaml** 文件的片段:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Chart.Name }}
  labels:
 **{{- include "webserver.labels" . | nindent 4 }}**
spec:
  replicas: 3
  selector:
    matchLabels:
   ** {{- include "webserver.selectorLabels" . | nindent 6 }}**
  template:
    metadata:
      labels:
   **   {{- include "webserver.selectorLabels" . | nindent 8 }}**
    spec:
      containers:
      - name: {{ .Chart.Name }}
        image: nginx:latest
        ports:
        - containerPort: 80
```

现在，让我们使用****舵模板**命令生成模板:**

```
**>>** helm template ~/webserver---------------------------------------------# Source: webserver/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webserver
 **labels:
    app: webserver
    app.kubernetes.io/managed-by: Helm**
spec:
  replicas: 3
  selector:
 **matchLabels:
      app: webserver**
  template:
    metadata:
 **labels:
        app: webserver**
    spec:
      containers:
      - name: webserver
        image: nginx:latest
        ports:
        - containerPort: 80
```

**这样，我们成功地生成了 Kubernetes 清单。**

## **关闭**

**命名模板可以更容易地维护我们想要在两个或更多资源之间共享的配置，并且它们帮助我们创建一个集中的地方来编辑公共配置。**

> ***如果你觉得这篇文章很有帮助，请* ***别忘了*** *点击* ***拍拍*** *和* ***跟着*** *按钮帮我写更多这样的文章。
> 谢谢🖤***

## **舵图上的所有文章-**

**[掌舵—操作中](https://faun.pub/helm-in-action-652abc10aa1c)
[掌舵—高级命令](https://medium.com/geekculture/helm-advanced-commands-9365097475b)
[掌舵—创建您的第一张图表](https://medium.com/faun/helm-create-your-first-chart-f3aa304c3544)
[掌舵—模板操作、功能和管道](https://medium.com/faun/helm-template-actions-functions-and-pipelines-16ed23ed336f)
[掌舵—流程控制](/helm-flow-control-a085a67f22e)
[掌舵—变量](https://medium.com/codex/helm-variables-df1dca52ed46)
[掌舵—命名模板](/helm-named-templates-de2efc3875d0)
[掌舵—依赖关系](https://medium.com/gitconnected/helm-dependencies-1907facbe410)**

## **参考**

**[](https://helm.sh/docs/chart_template_guide/named_templates/) [## 命名模板

### 是时候超越一个模板，开始创建其他模板了。在这一节中，我们将看到如何定义一个命名的…

掌舵。嘘](https://helm.sh/docs/chart_template_guide/named_templates/)**