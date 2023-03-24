# 如何用 Githooks 标准化 Python 代码

> 原文：<https://levelup.gitconnected.com/how-to-standardize-python-code-with-githooks-d81a73b481b4>

实现预提交钩子来转换我自己的网站代码

![](img/7515862a5bdc820b94bba8f13ed60b24.png)

作者网站截图

一组数据科学家和工程师在同一个存储库上工作。每个团队成员之间的变量命名会发生变化，其中一个使用 CamelCase，另一个使用下划线。如果未使用的导入仍在代码中，或者导入未排序，因此导入不容易识别，该怎么办？我亲眼目睹了这种效率低下的情况，这种情况是由之前发现的一些小事造成的。

数据开发人员可以提高效率的一种方法是将所有代码提升到一个标准并遵循一个协议。标准化简化了协作，允许开发更容易的过程，并使程序员能够同时处理同一项目的不同部分。

一个简单的方法是添加预提交挂钩，这样标准化就会作为提交过程的一部分自动发生。

## 下载回购

在这篇文章中，我将使用我的网站 dutchengineer.org 的一个示例模板。主分支存储了[可怕的代码](https://github.com/sdf94/flask-dash-blog)，而更干净的版本在[预提交清理分支](https://github.com/sdf94/flask-dash-blog/tree/pre-commit-cleaned)。

第一步是克隆我的存储库。如果您已经设置了 gh，您可以快速运行这些命令来获得本地目录。

```
gh repo clone sdf94/flask-dash-blog
```

*或*

可以本地拉[跟随 repo](https://github.com/sdf94/flask-dash-blog) ，下载 zip 文件等。

接下来，您需要输入目录:

```
cd flask-dash-blog
```

## 设置预提交

让我们开始吧。

1.  安装软件包

```
pip install pre-commit
```

或者

```
brew install pre-commit #if you are on macOS
```

2.在中添加预提交配置。预提交配置文件

我们有两个选择。要么我们可以

*   从另一个 repo 添加. pre-commit-config.yaml

或者

*   从他们的示例配置中创建一个(通过运行`pre-commit sample-config`)

我的第一版。pre-commit-config.yaml 文件如下所示:

```
repos:
-   repo: [https://github.com/pre-commit/pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)
    rev: v4.2.0
    hooks:
    -   id: check-yaml
    -   id: end-of-file-fixer
    -   id: trailing-whitespace
```

3.在中安装定义的 git 挂钩。预提交配置文件

```
pre-commit installpre-commit installed at .git/hooks/pre-commit
```

这个命令将预提交挂钩安装到 git。

4.对所有文件进行比对

```
pre-commit run --all-filesINFO] Installing environment for [https://github.com/pre-commit/pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks).
[INFO] Once installed this environment will be reused.
[INFO] This may take a few minutes...
[INFO] Installing environment for [https://github.com/pycqa/pylint](https://github.com/pycqa/pylint).
[INFO] Once installed this environment will be reused.
[INFO] This may take a few minutes...
```

然后它会给你一个很长的错误列表。

现在，一切都过去了。我知道我的代码不是最棒的，所以我会加一个 python linter 我个人喜欢 Pylint，所以这是我要添加的一个。

```
repos:
-   repo: [https://github.com/pre-commit/pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)
    rev: v4.2.0
    hooks:
    -   id: check-yaml
    -   id: end-of-file-fixer
    -   id: trailing-whitespace
-   repo: [https://github.com/pre-commit/mirrors-mypy](https://github.com/pre-commit/mirrors-mypy)
    rev: 'v0.942'  # Use the sha you want to point at
    hooks:
    -   id: mypy
-   repo: [https://github.com/pre-commit/mirrors-pylint](https://github.com/pre-commit/mirrors-pylint)
    rev: ''  # Use the sha / tag you want to point at
    hooks:
    -   id: pylint
```

如果我现在运行这个命令

```
pre-commit autoupdate************* Module main
main/__init__.py:1:0: C0114: Missing module docstring (missing-module-docstring)
main/__init__.py:1:0: E0401: Unable to import 'flask' (import-error)
main/__init__.py:2:0: E0401: Unable to import 'flask_flatpages' (import-error)
main/__init__.py:3:0: E0401: Unable to import 'flask_frozen' (import-error)
main/__init__.py:4:0: E0401: Unable to import 'flask_flatpages.utils' (import-error)
main/__init__.py:5:0: E0401: Unable to import 'markdown2' (import-error)
main/__init__.py:6:0: E0401: Unable to import 'dash' (import-error)
main/__init__.py:9:0: E0401: Unable to import 'bs4' (import-error)
main/__init__.py:10:0: W0404: Reimport 'markdown2' (imported line 5) (reimported)
main/__init__.py:10:0: E0401: Unable to import 'markdown2' (import-error)
main/__init__.py:11:0: E0401: Unable to import 'pandas' (import-error)
main/__init__.py:22:0: C0116: Missing function or method docstring (missing-function-docstring)
main/__init__.py:28:0: C0116: Missing function or method docstring (missing-function-docstring)
main/__init__.py:37:0: C0116: Missing function or method docstring (missing-function-docstring)
...
```

我想删除无法导入的错误，可以通过在。预提交配置. yaml 文件:

```
-   repo: [https://github.com/pre-commit/mirrors-pylint](https://github.com/pre-commit/mirrors-pylint)
    rev: ''  # Use the sha / tag you want to point at
    hooks:
    -   id: pylint
        args: ["--disable=W0142,W0403,W0613,W0232,R0903,R0913,C0103,R0914,C0304,F0401,W0402,E1101,W0614,C0111,C0301"]
```

我可以通过使用这里列出的选项进一步配置 Pylint。我们使用的是 mirror-pylint 回购，与 T2 的回购分支略有不同。

我看到了一长串需要解决的错误。我们一直在运行的命令使它检查所有的文件，但我现在只想一次关注一个文件。

通过使用以下关键字，我可以只关注一个文件或多个文件:

```
pre-commit run --files "main/content/about_website.md" "main/das
h/__init__.py" "main/dash/dashboard.py"
```

我在结果中看到了许多未使用和未排序的导入，所以我需要在提交代码之前删除它们。因此，我添加了 autoflake 来移除未使用的导入，并添加了 isort 来对导入进行排序。

```
repos:
-   repo: [https://github.com/pre-commit/pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)
    rev: v4.2.0
    hooks:
    -   id: check-yaml
    -   id: end-of-file-fixer
    -   id: trailing-whitespace
-   repo: [https://github.com/myint/autoflake](https://github.com/myint/autoflake)
    rev: ''
    hooks:
    -   id: autoflake
        args: ["-i","--remove-all-unused-imports"]
-   repo: [https://github.com/PyCQA/isort](https://github.com/PyCQA/isort)
    rev: ''
    hooks:
    -   id: isort
-   repo: [https://github.com/pre-commit/mirrors-pylint](https://github.com/pre-commit/mirrors-pylint)
    rev: ''  # Use the sha / tag you want to point at
    hooks:
    -   id: pylint
        args: ["--disable=W0142,W0403,W0613,W0232,R0903,R0913,C0103,R0914,C0304,F0401,W0402,E1101,W0614,C0111,C0301"]
```

我还可以做一些改变，但这至少会给我们现在一个良好的基础。

如果你想要其他挂钩，他们有他们支持的所有挂钩列表[在这里](https://pre-commit.com/hooks.html)。

## Git 提交

运行此命令

```
git commit -m "Adding pre-commit changes"
```

不让我提交，因为 pylint 挂钩会失败。

如果我想保存这些更改，尽管有那些失败，我可以在 git 钩子中卸载预提交。

```
pre-commit uninstall
```

然后，使用相同的提交命令，我们现在可以提交了。

```
git commit -m "Adding pre-commit changes"
```

## 结论

现在，您离让您的代码符合 python 标准或与您的团队成员保持一致又近了一步。

我真的希望你喜欢这篇文章，就像我喜欢写它一样！请随时在这里或在 Linkedin 上联系我。