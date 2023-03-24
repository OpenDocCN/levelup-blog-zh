# 使用 Conda 管理 Python 虚拟环境

> 原文：<https://levelup.gitconnected.com/manage-python-virtual-environments-with-conda-75360c3fe490>

## 数据科学、编程、Python

## 数据科学家和 Python 开发人员最常用的 7 个命令

![](img/604bf6e944c6c23680a8489fb13828ea.png)

克里斯·里德在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是虚拟环境？

Python 虚拟环境的主要目的是为每个项目创建单独的环境。

让我们通过一个例子来理解对虚拟环境的需求。假设您在同一个系统/机器中从事两个项目- `Project_A`和`Project_B`。`Project_A`要求 spacy = 2 . 3 . 0&Python≤3.6`Project_B`要求 spacy > 2.3.0 和 Python > 3.6。你如何处理这种情况？如果你想在基础环境中安装所有的东西，那么你迟早会遇到问题。最好的方法是为每个项目创建单独的虚拟环境，并相应地使用它。本文讨论如何使用 Python 虚拟环境。

**要运行这些命令，请使用 Anaconda 命令提示符。**

# 1.创造环境

下面的命令将创建一个名为`myenv`的环境。您可以随意使用任何环境名称。默认情况下，在 Windows 机器中，所有的环境都将存储在`C:\Users\<<use_id>>\.conda\envs`中。

```
conda create — name myenv
```

## 使用特定版本的 Python 创建一个环境

这将创建一个安装了 python 3.6 的环境`myenv`。激活环境后，您可以开始安装您需要的任何软件包。

```
conda create --name myenv python=3.6
```

## 用特定的包创建一个环境

这将创建一个带有`spacy`最新版本的环境`myenv`。如果需要安装特定版本的包，可以用`spacy=2.3.0`代替`spacy`。

```
conda create --name myenv spacy
```

## 使用特定版本的 Python & package 创建环境

该命令将创建环境`python 3.6`和`spacy 2.3.0`。您可以根据需要使用任意数量的软件包。如果你需要安装很多包，最好的方法是使用下面将要讨论的`yml`文件。

```
conda create -n myenv python=3.6 spacy=2.3.0
```

## 从 environment.yml 文件创建环境

如果在创建环境时需要安装一个很长的软件包列表，您可以通过包含需要安装的软件包列表来创建一个 yml 文件，并在创建环境时使用它。

```
**environment.yml** name: myenv
dependencies:
- spacy>=2.3.0
- pandas
- seaborn
- scikit-learn
```

一旦你创建了 yml 文件，运行下面的命令将会创建一个名为`myenv`的环境，在这个环境中安装了 yml 文件中的所有包。

```
conda env create -f environment.yml
```

# 2.克隆环境

出于任何原因，如果您需要克隆现有的环境，可以使用下面的命令。这将通过克隆`myenv`创建一个名为`myenv_new`的新环境。这将克隆包括 python 版本在内的所有包。

```
conda create --name myenv_new --clone myenv
```

# 3.激活和停用环境

## 使活动

创建环境后，要激活并开始使用，请运行以下命令。

```
conda activate myenv
```

## 复员

要停用，只需运行命令-

```
conda deactivate
```

# 4.确定和查看您当前的环境

这两个命令用于列出系统中安装的所有环境。

```
conda info --envs
Or
conda env list
```

请注意，在上面的列表中，您当前的环境以星号(*)突出显示。

# 5.共享一个环境

如果您想在同事的系统中设置相同的环境，您可以将环境详细信息导出到 yml 文件中，然后使用该文件在同事的系统中设置类似的环境。

首先，您需要激活环境，然后运行 export 命令来创建包含环境细节的 yml 文件，如下所示。

```
conda activate myenv
conda env export > environment.yml
```

# 6.恢复环境

Conda 跟踪环境的所有变化，以便您可以轻松地回滚到以前的版本。第一个命令列出了环境的每次更改的历史记录，第二个命令用于回滚到特定版本。

```
conda list --revisionsconda install --revision=<<REVNUM>> #replace <<>REVNUM>> with number
```

# 7.移除环境

以下命令将删除`myenv`环境。您可以通过检查`conda info — envs`的结果来验证环境是否被移除。

```
conda remove --name myenv --all
```

# 结论

这些是所有数据科学家和 Python 开发人员使用 Conda 管理不同 Python 环境时最常用的命令。希望这篇文章对你有用。

*阅读更多关于 Python 和数据科学的此类有趣文章，* [***订阅***](https://pythonsimplified.com/) *到我的博客*[***www.pythonsimplified.com***](http://www.pythonsimplified.com)***。*** 你也可以通过 [**LinkedIn**](https://www.linkedin.com/in/chetanambi/) 联系我。