# 舵-流量控制

> 原文：<https://levelup.gitconnected.com/helm-flow-control-a085a67f22e>

## 使用 if/else、带和范围的舵“流量控制”

![](img/b51970139b28f65ba8d0cf6f2a9838cc.png)

约瑟夫·巴里恩托斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Helm 的模板语言提供了以下控制结构:

> [***If/else***](#d18e)***—****用于创建条件块*[***用***](#eceb)***—****来指定一个范围*

在本文中，我们将讨论**舵流** **控制**以及各种示例。

## If/else

我们可以使用 **If/else** 语句控制舵图的流程，就像任何其他编程语言一样。

```
**{{ if PIPELINE }}
**  # Do something
**{{ else if OTHER PIPELINE }}**
  # Do something else
**{{ else }}**
  # Default case
**{{ end }}**
```

如果值为，管道将被评估为 ***假*** :

> *一个布尔* ***假*** *一个数字* ***零*** *一个* ***空*** *串
> 一个*`***nil***`**(空或**

*现在，让我们看看如何在 helm 模板中使用 **if/else** 。*

*假设我们有包含以下条目的 **values.yaml** 文件:*

*现在我们将创建一个 **configmap.yaml** 模板文件，其中我们将使用 **if/else** 进行决策:*

*使用**舵模板**命令生成模板:*

```
***>>** helm template ~/webserver---
# Source: webserver/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: release-name-configmap
data:
 **mode: light

  env: test***
```

## *控制空白*

*在上面的演示中，我们可以看到有一些**空白行/空格。为了删除任何前导空格，我们可以在 if/else** 语句前使用破折号`{{-`。*

```
***>>** helm template ~/webserver---
# Source: webserver/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: release-name-configmap
data: **mode: light
  env: test***
```

*在前面的演示中，我们使用了“`**eq**`”函数和`**if**`语句。我们可以根据需要使用其他逻辑和流控制功能。有一组[逻辑和流量控制功能](https://helm.sh/docs/chart_template_guide/function_list/#logic-and-flow-control-functions)可用。*

*让我们尝试将`**and**`函数与`**if**`语句一起使用；为此，我们将在 **values.yaml** 文件中添加几个条目:*

*我们想要实现的是，如果 **values.yaml** 文件中定义了`**darkMode: true**`和`**os: mac**` ，那么我们的 **configmap.yaml** 模板文件将打印`**mode: dark**` 否则将打印`**mode: light**`。*

*我们必须相应地修改 **configmap.yaml** 模板文件，以实现上述目标:*

*在上面的演示中，我们根据要求使用了`**and**`功能进行决策。*

*现在，生成模板来验证 **configmap.yaml** 文件是否正常运行:*

```
***>>** helm template ~/webserver/

---
# Source: webserver/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: release-name-configmap
data: 
 **mode: dark**
  env: test*
```

## *使用“with”修改范围*

*我们将讨论的下一个控制结构是`**with**` 动作。这控制变量范围。之前，我们已经看到`**.Values**`告诉模板在图表中的`**Values**`对象中查找对象。这里，`.`表示当前范围。范围可以改变，`with`允许我们将当前范围设置为特定对象。*

```
*{{ **with** PIPELINE }}

  {{- toYaml **.** | nindent 2 }}{{ end }}*
```

*在`with`范围内，`.`不指根对象。在`with`对象中，`.`用于访问当前对象的范围。*

*为了更好的理解，让我们看一些例子。*

***示例 1:** 假设我们有一个包含以下条目的 **values.yaml** 文件:*

*我们将创建一个 **configmap.yaml** 模板，其中`with`将用于变量范围:*

*现在，生成模板:*

```
***>>** helm template ~/webserver---
# Source: webserver/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: release-name-configmap
data: 
  env: test 
 **platfrom:
  - JAVA
  - PYTHON
  - GOLANG***
```

***示例 2:** 让我们向 **values.yaml** 文件添加一些附加条目:*

*然后我们将修改我们的 **configmap.yaml** 模板文件。以便我们可以检索添加到 **values.yaml** 文件中的附加数据:*

*最后，生成模板:*

```
***>>** helm template ~/webserver# Source: webserver/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: release-name-configmap
data: 
  env: test 
  platfrom:
  - JAVA
  - PYTHON
  - GOLANG
 **operating-system: linux
  database-name: mongo***
```

*正如我们前面讨论的，在一个`with`块中`.`引用了一个特定的对象。但是在某些情况下，我们可能需要访问根对象或其他对象，这些对象不属于当前的范围。例如，如果我们写这样的东西:*

```
*{{- with .Values.configMap.data.conf }}
  operating-system: {{ .os }}
  database-name: {{ .database }}
 **k8s-namespace: {{ .Release.Namespace }}** {{- end }}*
```

*那么上面的代码会抛出这样一个错误:*

```
*Error: template: webserver/templates/configmap.yaml:14:28: executing "webserver/templates/configmap.yaml" at **<.Release.Namespace>: nil pointer evaluating interface {}.Namespace***
```

*因为，我们引用的是一个对象，它位于当前范围之外。*

*要解决这个问题，我们可以在**释放**对象前使用 **$** 符号。因为根作用域也用 **$** 符号表示。*

```
*{{- with .Values.configMap.data.conf }}
  operating-system: {{ .os }}
  database-name: {{ .database }}
 **k8s-namespace: {{ $.Release.Namespace }}** {{- end }}*
```

*现在，上面的代码可以正常工作了，因为我们在 **Release** 对象前面添加了一个 **$** 符号。*

## *范围*

*`**range**` 与 **for/foreach** 循环类似，如同其他编程语言。在 Helm 的模板语言中，遍历集合的方法是使用`**range**`操作符。*

*假设我们有包含以下条目的 **values.yaml** 文件:*

*在 **values.yaml** 文件中，我们定义了一个“**平台”**列表，让我们创建一个 **configmap.yaml** 模板文件，使用`**range**`操作符检索该列表:*

*最后，生成模板:*

```
***>>** helm template ~/webserver---
# Source: webserver/templates/test.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: release-name-configmap
data: 
  env: test 
 **platfrom: |
   - "Java" 
   - "Python" 
   - "Golang"***
```

> **如果你觉得这篇文章很有帮助，请* ***别忘了*** *点击* ***拍拍*** *和* ***跟着*** *按钮帮我写更多这样的文章。
> 谢谢🖤**

# *舵图上的所有文章-*

*[掌舵—操作中](https://faun.pub/helm-in-action-652abc10aa1c)
[掌舵—高级命令](https://medium.com/geekculture/helm-advanced-commands-9365097475b)
[掌舵—创建您的第一张图表](https://medium.com/faun/helm-create-your-first-chart-f3aa304c3544)
[掌舵—模板操作、功能和管道](https://medium.com/faun/helm-template-actions-functions-and-pipelines-16ed23ed336f)
[掌舵—流量控制](/helm-flow-control-a085a67f22e)
[掌舵—变量](https://medium.com/codex/helm-variables-df1dca52ed46)
[掌舵—命名模板](/helm-named-templates-de2efc3875d0)
[掌舵—依赖关系](https://medium.com/gitconnected/helm-dependencies-1907facbe410)*

## *参考*

*[](https://helm.sh/docs/chart_template_guide/control_structures/) [## 流控制

### 控制结构(模板术语中称为“动作”)为模板作者提供了…

掌舵。嘘](https://helm.sh/docs/chart_template_guide/control_structures/)*