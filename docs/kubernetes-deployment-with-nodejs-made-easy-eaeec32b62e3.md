# 使用 NodeJS 简化 Kubernetes 部署

> 原文：<https://levelup.gitconnected.com/kubernetes-deployment-with-nodejs-made-easy-eaeec32b62e3>

## 让我们在 Kubernetes 集群上部署一个 NodeJS 应用程序，并进行现场测试！

![](img/ecaaf46155cc35678914a3cc5406e39a.png)

由亚历山大·德比夫在 [Unsplash](https://unsplash.com/s/photos/technology?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

在之前的一篇文章中，我们学习了 Kubernetes 的基本概念。

今天我们将学习如何在 Kubernetes 集群上部署 NodeJS 应用程序。步骤如下。

*   为 NodeJS 应用程序创建 Docker 映像
*   将图像发布到 DockerHub
*   创建一个 Linode Kubernetes 集群
*   在我们的本地机器上安装 Kubectl
*   将我们的应用部署到 Kubernetes 集群中

## 在我们开始之前…

我用 ExpressJS 和 Typescript 创建了一个[专业样板。本文是这个系列的一部分。你可以在下面找到所有的文章。](https://github.com/Mohammad-Faisal/professional-express-sequelize-docker-boilerplate)

```
***** [**Creating a ExpressJS + Typescript Boilerplate**](https://javascript.plainenglish.io/create-an-express-boilerplate-with-typescript-810eb6c29196) ***** [**How to setup Linter and Formatter for NodeJS**](https://javascript.plainenglish.io/how-to-set-up-linter-formatter-for-node-js-d6b34c0c8be5) ***** [**How to handle multiple environments in NodeJS**](https://blog.devgenius.io/how-to-handle-multiple-environments-in-nodejs-7391d2db2abe) ***** [**Error Handling in NodeJS**](https://javascript.plainenglish.io/error-handling-in-node-js-like-a-pro-ed210baa0600) ***** [**Request validation in NodeJS**](https://medium.com/p/c69f2494cf18) ***** [**Using Docker Professionally with NodeJS**](/use-docker-with-nodejs-projects-like-a-pro-a9e7504e1308) ***** [**Using Docker for Local Development in NodeJS**](https://javascript.plainenglish.io/node-js-database-with-docker-for-local-development-285212c5162f) ***** [**Logging in NodeJS**](https://javascript.plainenglish.io/node-js-logging-for-professionals-6be07c003e7f) ***** [**Kubernetes with NodeJS**](/kubernetes-deployment-with-nodejs-made-easy-eaeec32b62e3)
```

我们开始吧！

# 创建 Docker 图像

在上一篇文章中，我们学习了如何对基本 NodeJS 应用程序进行 dockerize。你可以在这里找到那篇文章:

该文章的存储库可以在[这里](https://github.com/Mohammad-Faisal/express-typescript-docker)找到

所以还是先克隆吧！

```
git clone https://github.com/Mohammad-Faisal/express-typescript-docker.gitcd express-typescript-docker
```

所以我们现在有了一个已经 Dockerized 的 NodeJS 应用程序！

# 在 Dockerhub 上发布图像

Dockerhub 是一个可以发布存储库的地方。这是一种用于 Docker 图像的 Github，可以免费使用！所以放心在这里开户[。](https://hub.docker.com/)

然后，在我们可以发布图像之前，我们必须构建它。

```
docker image build -t 56faisal/learn-kubernetes:1.0 .
```

注意这里，

```
56faisal -> is the Dockerhub Username
learn-kubernets -> is the image name and
1.0 -> is the tag name
```

如果运行 Mac，你默认的 Docker 系统会使用 arm64 架构。但是在 Linode 上，您的虚拟机很可能是使用 amd64 架构构建的。当您部署映像时，这种版本不匹配会导致问题。因此，如果您在 Mac 上，使用以下命令构建您的映像。

```
docker image build  --platform=linux/amd64 -t 56faisal/learn-kubernetes:1.0 .
```

然后在本地看到列表上的图像。

```
docker image ls REPOSITORY                                TAG                   IMAGE ID       CREATED        SIZE
56faisal/learn-kubernetes                 1.0                   13a974972901   2 hours ago    263MB
```

然后登录 Dockerhub。

```
docker login --username docker_hub_id # for me it's 56faisal
```

它会询问你的密码。把那个给我，你就可以走了。

然后把图片推送到 Dockerhub。

```
docker image push 56faisal/learn-kubernetes:1.0
```

你可以去 Dockerhub 验证你的图像在那里。对我来说，URL 看起来像这样:

```
[https://hub.docker.com/repository/docker/56faisal/learn-kubernetes](https://hub.docker.com/repository/docker/56faisal/learn-kubernetes)
```

# 开始部署吧！

但在此之前，我们需要安装`kubernetes-cli`

```
brew install kubernetes-cli
```

你可以在这里获得其他操作系统[的安装说明](https://kubernetes.io/docs/tasks/tools/)

# 在 Linode 上创建 Kubernetes 集群

Kubernetes 有很多服务，但是 Linode 提供了最简单的管理方法。因此，我们将在 Linode 上创建一个带有两个 worker 节点的 Kubernetes 集群。

我不会谈论这方面的细节。你可以[跟随这个链接](https://www.linode.com/docs/guides/deploy-and-manage-a-cluster-with-linode-kubernetes-engine-a-tutorial/)去做。

# 获取 Kubernetes 集群配置。

在 Linode Kubernetes 集群页面上，您会看到一个下载 Kubernetes 配置文件的按钮。

下载并把它放到项目的根目录下。或者任何地方。这将允许我们稍后与 Kubernetes 集群进行交互。

打开一个终端并将 kubeconfig 的路径保存到`$KUBECONFIG`环境变量中。

您可以通过运行`pwd`获得当前目录路径，并使用它来获得您的配置文件的路径。

现在就给我们设定一下语境吧！

```
export KUBECONFIG= /PATH_TO_FOLDER/learn-kubernetes-kubeconfig.yml
```

然后运行下面的命令来查看节点！节点是运行在云上的物理机器或虚拟机。

```
kubectl get nodes
```

我们在 Linode 集群上创建了两台服务器。所以我们会看到两个节点。

```
NAME                          STATUS   ROLES    AGE   VERSION
lke55618-87276-6237a2503845   Ready    <none>   33m   v1.22.6
lke55618-87276-6237a2510769   Ready    <none>   33m   v1.22.6
```

节点已经准备好运行我们的应用程序。

# 创建部署

所以现在我们可以和我们的 Kubernetes 集群互动。但是现在我们需要实际部署一些东西来理解它的力量。Kubernetes 有一个豆荚的概念。pod 是在您的基础设施上运行的独立容器。

您可以创建任意数量的 pod，并且可以通过配置部署来实现。这意味着您的两台机器上可以有 20 个 pod。一般来说，我们在一个舱里有一个集装箱。

让我们创建一个名为`deployment.yml`的部署配置，并粘贴以下配置。

部署. yml

以下是一些需要注意的重要事项:

`selector`->-`matchLabels`->-`app`->-`express-docker-kubernetes`

这个应用程序帮助我们命名我们的豆荚。稍后，它将帮助我们修改部署，并将它们与服务相关联。

# 部署应用程序

然后使用以下命令进行部署。

```
kubectl create -f deployment.yml
```

我们使用`-f`配置传递`deployment.yml`文件。这允许我们为不同的文件夹创建多个部署文件。

您可以通过运行以下命令来查看部署:

```
kubectl get deployments
```

如果您想了解部署的详细信息:

```
kubectl explain deployment
```

这将给出这样的输出

```
NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
learn-kubernetes-deployment   0/2     2            0           7s
```

如果要删除部署，可以运行以下命令:

```
kubectl delete deploy learn-kubernetes-deployment
```

这里最后一部分是部署的名称。

# 看到豆荚了吗

您的部署已经创建了两个 pod，因为我们为部署配置提供了选项 replicas:2。

让我们看看那些豆荚！

```
kubectl get pods
```

它会给出这样的输出。

```
NAME                                          READY   STATUS    RESTARTS   AGE
learn-kubernetes-deployment-6fdf4bf45-pglg8   1/1     Running   0          49m
learn-kubernetes-deployment-6fdf4bf45-s9dsd   1/1     Running   0          49m
```

所以我们两个都在运行！如果您需要扩展，可以将数量从 2 增加到您想要的数量，然后重新部署。就这么简单！

有时你需要看看你的舱里发生了什么。您可以使用以下命令从窗格中获得更多详细信息。

```
kubectl describe pods
```

输出将包含您需要的所有信息，包括 pod 的 IP 地址。但是不幸的是，您还不能访问您的应用程序，因为您的应用程序没有公开！

让我们现在就做吧！

# 向公众展示

让我们使用下面的命令公开部署。

```
kubectl expose deployment learn-kubernetes-deployment --type="LoadBalancer"
```

或者我们也可以为部署创建一个服务

service.yaml

那就跑

```
kubectl apply -f service.yml
```

这将在 Linode 服务器上为我们的应用程序创建一个负载平衡器，我们将获得一个公共端点来调用我们的服务。

成功部署服务后，通过运行以下命令获取负载平衡器的详细信息:

```
kubectl get services
```

您将看到以下输出:

```
NAME                       TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)          AGE
kubernetes                 ClusterIP      10.128.0.1       <none>           443/TCP          4h58m
learn-kubernetes-service   LoadBalancer   10.128.247.181   172.105.44.102   3000:31752/TCP   74s
```

注意`EXTERNAL-IP`这一列给出了您的应用程序的公共 IP 地址。

让我们打开你的浏览器，点击下面的网址

```
[http://172.105.44.102:3000/](http://172.105.44.102:3000/)
```

您将看到以下输出。

```
{ "message": "Hello World!" }
```

我们的应用程序现已上线！我们可以用它做任何我们想做的事！

祝贺您使用 Kubernetes 部署了第一个 NodeJS 应用程序。

```
**Want to Connect?**You can reach out to me via [**LinkedIN**](https://www.linkedin.com/in/56faisal/) or my [**Personal Website**](https://www.mohammadfaisal.dev/blog)
```

# Github 回购:

[](https://github.com/Mohammad-Faisal/nodejs-docker-kubernetes) [## GitHub-Mohammad-fais al/nodejs-docker-kubernetes:用于 NodeJS 应用程序的 Kubernetes 部署

### Kubernetes 是微服务的编排工具。它有助于根据需求扩展服务…

github.com](https://github.com/Mohammad-Faisal/nodejs-docker-kubernetes)