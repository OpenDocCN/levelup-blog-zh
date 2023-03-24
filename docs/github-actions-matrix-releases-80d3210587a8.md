# Github 动作:矩阵发布

> 原文：<https://levelup.gitconnected.com/github-actions-matrix-releases-80d3210587a8>

![](img/5bc125a85291557019856deac87e78dd.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

最近，我参与的一个软件项目要求发布多个平台的版本。下面是我如何使用 Github Action 的*矩阵策略实现它的。*

## TLDR

这里可以只看代码[。](https://github.com/nwillc/syncher/blob/master/.github/workflows/RELEASE.yaml)

## 放

我开始了一个只有一个目标平台的项目。我从未在 Github 中使用工件实现过*发布*过程，但是自从我最近在我的 CI/CD 中使用 Github Actions 后，我就在寻找解决方案。我找到了 [actions/create-release](https://github.com/actions/create-release) ，它让我用大约一页的 YAML 对一个版本标签做了如下推送:

*   创建二进制工件
*   创建一个版本
*   将二进制文件添加到版本中

它实现简单，运行完美，我所要做的就是标记我的代码来启动它。

## 然后…

我需要发布第二个目标平台。没问题，我修改了 YAML 如下:

*   为平台 A 创建二进制工件
*   为平台 B 创建二进制工件
*   创建一个版本
*   将二进制文件 A 添加到版本中
*   将二进制文件 B 添加到发行版中

你能看到事情的发展，对吗？当我需要添加第三个目标平台时，我再次剪切和粘贴东西，让它工作，然后开始重构它。

## 进入矩阵

我查看了动作步骤中的循环，但却发现了[矩阵策略](https://docs.github.com/en/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions#jobsjob_idstrategymatrix)，它允许*一个作业*运行*多个配置。*于是，我将我的单一工作分解为三项:

*   工作 1:使用矩阵策略构建所有目标平台*工件*
*   工作 2:创建一个*版本*
*   工作 3:使用矩阵策略将所有工件添加到发布中

我不打算给出最终 YAML 的详细演示，这会有点多，但你可以在这里看到它。让我们只看结构:

```
jobs: # Job 1
  artifacts:
    strategy:
      matrix:
        target: [ 
            { 'os': 'darwin', 'arch': 'amd64' }, 
            { 'os': 'linux', 'arch': 'amd64' }, 
            { 'os': 'linux', 'arch': '386' } 
          ] steps:  
    - name: Create Artifact # Job 2
  release:
    runs-on: ubuntu-latest
    needs: artifacts

    steps:
    - name: Create Release # Job 3
  add:
    needs: [ artifacts, release ]
    strategy:
      matrix:
        target: [ 
            { 'os': 'darwin', 'arch': 'amd64' }, 
            { 'os': 'linux', 'arch': 'amd64' }, 
            { 'os': 'linux', 'arch': '386' } 
          ] steps:
    - name: Upload to release 
```

有了这个结构，我们有三个任务，两个用*矩阵策略*运行，所以它们将为所有的*目标*并行运行，中间有一个发布任务。

## 挑战

这种结构确实带来了一些挑战:

*   首先，在个作业之间共享工件*。有具体的行动。你需要在一个任务中*上传工件*，然后在另一个任务中*下载工件*。*
*   第二，*释放*作业创建一个*添加*作业所需的 url。Actions 为此提供了一个*输出*机制。您在*发布*中捕获输出，并在*添加中通过名称引用它。*

有了这些解决方案，我成功地做了我想做的事情。

**进展如何？**

*   **优点**:我移除了剪切&粘贴，现在添加新目标只是更新目标列表的问题。现在可以并行执行的是，请注意，这是一个后台进程，所以我不会等待它。
*   缺点:作业之间共享文件和值会产生开销。最终的 YAML 比以前更好，但不像我希望的那么简单。