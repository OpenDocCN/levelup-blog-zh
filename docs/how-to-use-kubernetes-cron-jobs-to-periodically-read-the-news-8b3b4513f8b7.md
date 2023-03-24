# 如何使用 Kubernetes Cron Jobs 定期阅读新闻

> 原文：<https://levelup.gitconnected.com/how-to-use-kubernetes-cron-jobs-to-periodically-read-the-news-8b3b4513f8b7>

## 看新闻的微服务。

![](img/c813a2acd39feb250b7f5e06bd8e9c8d.png)

图片由 [StockSnap](https://pixabay.com/users/StockSnap-894430/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2566677) 来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2566677)

大约二十年前，我发现我的股票经纪人查尔斯·施瓦布发布了一个 API 来获取报价。文档很少，据我所知，请求的数量没有限制。我不相信我必须使用我的在线凭证来访问它。所以我构建了一个小的 Java 应用程序，它可以读取 API，并每五分钟在一个小窗口中给我提供报价，我可以把它放在屏幕的角落里。

当然，这样的数据现在已经被锁定了。Schwab API 早已不复存在，大多数经纪公司都不会免费提供 API(有传言称罗宾汉经纪公司有一个秘密 API)。这并不是说没有任何公共 API。我最近看了看，发现了几个提供免费服务但有收费限制的新闻来源。你也可以找到免费天气和有不同程度限制的股票的来源，但我们今天要看新闻。

在本文中，我将选择一个新闻 API，编写一个小型 Node.js 微服务来读取它，并将其转储到 MongoDB 数据库中。我还将创建一个小的 Java 微服务来读取数据库，这样我就可以阅读新闻了。最后，我将把服务部署到 Kubernetes。这将展示一些东西；首先，微服务可以用不同的表达语言来完成，这取决于它所做的工作类型。我不想为定期读取 API 的服务启动 Java/Spring，所以我使用 Node.js。但是我想为我的读取服务提供 Java/Spring Boot 附带的附加功能。还将演示在 Node.js 和 Java 中创建微服务的异同。

我可能会决定在这个想法的基础上，创建其他服务来筛选收集的数据或更好地呈现数据，但是对于本文，我将重点关注上面描述的两个服务。为了能够跟上，您应该对 Java、JavaScript、Docker 和 Kubernetes 有所精通。所有开发的代码将在我的 [GitHub](https://github.com/rkamradt) 账户上可用。如果你想跳过软件开发，你可以直接进入本文的第三部分“在 Kubernetes 上运行一切”,它将使用我发布到 [DockerHub](https://hub.docker.com/) 的图片。我打算把所有的事情都挤在一个周末，所以我最好停止喋喋不休，开始开发。

我选择的新闻 API 是 NewsAPI.org。我需要通过注册从他们那里获得一个密钥。给他们你的电子邮件地址和密码来创建一个帐户，他们会给你一个密钥。将它保存在某个地方，因为您稍后将需要它来访问 API。

# 构建新闻阅读器微服务

Node.js 最大的优点之一就是没有脚手架和样板文件。我们应该能够在三个主文件中完成这个应用程序，`app.js`、`package.json`和`Dockerfile`。第一个文件是我们的应用程序代码，第二个文件描述了项目，第三个文件描述了如何将它构建成一个映像。还会有一些类似自述文件的文件，以及各种忽略文件，但应该就是这样了。我将在 GitHub 中创建一个名为`newsreader`的新存储库，并从一个类似的 Node.js 项目中复制文件。

首先，我们来看看我为`app.js`想出了什么:

我们将 [axios](https://github.com/axios/axios) 用于 HTTP 客户端和官方的 MongoDB Node.js 客户端。两者都使用基于承诺的 API，可以使用 async/await 模式。这给人一种“做这做那”编程的自然感觉，而不是多层回调，但仍然保持非阻塞，这在单线程 JavaScript 中始终是关键。

没什么，读取 NewsAPI URL，将响应插入 MongoDB。大约 50 行我认为是不言自明的代码。大部分内容原封不动地复制自 axios 和 MongoDB 参考指南中的示例。应用程序代码应该是这样的，简单明了。

我的年轻读者可能不熟悉这本书，但是 Brian Kernigham 和 Rob Pike 的书*Unix 编程环境(Prentice-Hall 软件系列)*不仅仅是关于编程环境，而是一种哲学。其中一个核心理念是，一个程序应该做一件事，并把它做好。他们讨论了如何将`cat`、`ls`和`grep`这样的命令组合在一个命令或脚本中，以执行更复杂的功能。我在微服务上看到了同样的理念。你的微服务要做好一件事，把它做好。

我们可以把它们打包成一个`package.json`文件。这个文件应该描述我们正在生产的应用程序，包括所有的外部依赖。在我们的例子中，我们依赖于 axios、MongoDB 和 NPM。当构建应用程序时，这个文件将被读取，外部包将被复制到`node_modules`目录，在运行时可以在那里读取它们。下面是我想到的`package.json`文件:

接下来，我们可以用 Docker 创建一个图像文件。`Dockerfile`看起来是这样的:

创建 Node.js 应用程序的 Docker 映像有一个技巧。命令`npm install`可能会创建二进制代码，或者使用特定于平台的模块。因此，与其从命令行构建然后只是复制`node_modules`目录，还不如将`package*.json`复制到映像中，然后在 Docker 构建中运行`npm install`，以便它为 Docker 的平台 Linux 创建特定于平台的项目。事实上，您应该创建一个专门排除本地`node_modules`目录的`.dockerignore`文件，以防它存在。

最后，仅出于测试目的，我将创建一个`docker-compose.yaml`文件。它将允许我快速构建和部署应用程序以及 MongoDB，这样我就可以在将映像提交给 Kubernetes 之前看到它的运行。

为了测试它，首先`export`您从 newapi.org 获得的密钥，构建映像，启动 MongoDB，然后运行应用程序

```
export NEWSAPI_KEY=<your-newsapi-key>
docker-compose build
docker-compose up -d mongodb
docker-compose up app
```

您应该会看到类似如下的日志:

```
app_1      | Connecting to mongodb://admin:admin@mongodb:27017
app_1      | Connected correctly to server
app_1      | Reading https://newsapi.org/v2/top-headlines
app_1      | Inserting news to server
app_1      | Closing mongoDB connection
app_1      | Ending
```

您必须单独启动 MongoDB，因为应用程序会在开始监听之前寻找它。通常，在输入命令启动应用程序之前，MongoDB 已经启动了。但是它们必须在同一个`docker-compose.yaml`文件中，这样它们才能共享一个内部网络。经过测试，可以用`docker-compose down`停止 MongoDB。

我们如何知道 API 被正确读取？在我最初的实现中，我将输出转储到日志中，因此我对连接正确返回感到满意。我清理了日志，因为我不想用我粘在数据库中的东西来填充它们。但是请随意记录您自己看到的回复。

你会注意到没有单元测试。随着我的服务变得越来越简单，我越来越少使用单元测试。像我们刚刚创建的这样的服务，几乎所有的代码都在库中，我希望它们都经过了很好的测试。因此，如果 axios 和 MongoDB 驱动程序有 90%的代码覆盖率，那么整个应用程序也是如此。为拥有一个 90%代码覆盖率的应用程序而自豪吧！一般来说，库应该进行单元测试，应用程序和服务应该在环境中运行时进行集成测试。

现在，把图片放到 DockerHub 或者你最喜欢的图片库中。如果你运行了`docker-compose build`命令，它已经为你创建了一个图像。运行`docker images`找到它，应该是你所在目录的名称和`docker-compose.yaml`中服务的名称。我的情况是`newsreader_app`。所以我可以贴标签:`docker tag newsreader_app rlkamradt/newsreader`。然后我可以推:`docker push rlkamradt/newsreader`。我稍后使用的所有图片都将引用`rlkamradt/*`。如果您正在编码，您可以用您的存储库和存储库用户名替换`rlkamradt`。

# 构建 readnews 微服务

Java read 服务将使用基于[项目反应器](https://projectreactor.io/)的 Spring Boot 和 WebFlux HTTP 控制器。我将引入日期参数，这样我就可以基于日期进行过滤，但除此之外，它只会从 MongoDB 中转储数据，这只是来自新闻 API 的原始数据。

我要拿出我的老朋友 [Apache NetBeans](https://netbeans.apache.org/) 来做开发。我更喜欢 NetBeans，因为它与 Maven 紧密集成。我更喜欢 Maven，因为如果你只是从 Java 构建 jar，它非常简洁。如果你能原谅丑陋的 XML。幸运的是，使用 NetBeans 来创建和修改您的项目，您不必过多地处理 XML。你可以使用任何你觉得舒服的 IDE，但是当我使用 NetBeans 完成某件事情时，你可能必须弄清楚如何应用我正在做的事情。

打开 NetBeans 后，从“文件”菜单中选择“新建项目”。这将把你带到一个“新项目”对话框。在那里，我选择“Java with Maven/Java Application ”,单击“next ”,输入一个项目名“readnews ”,它会计算出其余字段的合理值。到目前为止，一切顺利，还没有接触过 XML。它创建了填充了几项的`pom.xml`文件，并创建了 Maven 使用的目录结构。

我将使用 Spring Boot 作为这个应用程序的平台。因此，我要做的第一件事是将该模块的父模块设置为 Spring Boot 启动器父模块。这将使所有版本协调一致，使它们能够很好地协同工作。不幸的是，NetBeans 没有 GUI 工具来设置模块的父模块，这意味着我必须使用 XML。打开 pom.xml(在 NetBeans 中，它隐藏在“项目文件”文件夹中)并添加以下内容:

这将导致您的 pom.xml 从 Spring parent 继承所有内容，这主要是依赖关系管理，因此您不必填写您作为依赖关系列出的任何内容的版本。因为这只是依赖关系管理，它不会引入任何依赖关系——您仍然可以进行挑选。但是你会知道你所有的依赖都在一个版本上，它们应该一起工作。

既然我们已经打开了`pom.xml`文件，我们可以添加一些依赖项，以及 Spring Boot 运行所需的其他项目。以下是依赖关系:

我们可以添加的另一件事是一些构建插件。Spring Boot 需要一个插件来制作一个有阴影的罐子。我们还将使用插件来创建 Docker 图像，并从 Git 获取信息以在服务中使用。这是构建部分:

我不知道你是否真的需要`spring-boot-maven-plugin`,因为`jib`插件用原始片段而不是阴影罐子构建图像。但是我会把它放在那里，以防万一我们想把服务作为测试用的 jar 来运行。

Google 的`[jib](https://github.com/GoogleContainerTools/jib)` [插件创建了一个 Docker 镜像，没有使用 Docker(也没有`Dockerfile`)。如果您打算在容器中进行构建，这一点很重要，就像在 Kubernetes 中运行的 Jenkins 管道一样，这样您就不必处理 docker-in-docker。它还为依赖 jar、应用程序类和资源文件创建了单独的层。这意味着如果你不改变你的依赖关系，你不必构建第一层，这一层几乎总是最大的。这使得后续构建速度更快。](https://github.com/GoogleContainerTools/jib)

`[git-commit-id-plugin](https://github.com/git-commit-id/git-commit-id-maven-plugin)`获取关于构建和最后一次提交的信息，并将其放入 Spring Boot 知道的属性文件中。稍后我们会看到更多。

这是一个非常完整的`pom.xml`，它不到一百行。在我看来，从 Java 代码构建 jar 文件(现在是图像文件)没有比 Maven 更好的工具了。诚然，XML 有点难看，但它也很容易被其他工具解析和使用来收集信息。当 NetBeans 创建 Maven 项目时，它没有其他配置文件。一切都储存在`pom.xml`里。它自动知道如何插入依赖关系，以及如何运行目标。它创建了一个`nbaction.xml`文件来存储不同的 Maven 目标和参数，比如在 IDE 中运行、调试或分析时使用的主类文件。但是它不像其他 ide 那样必须创建自己的项目文件，然后努力确保它们与`pom.xml`同步，而后者应该是构建项目的真实来源。

让我们来看看代码。因为这个微服务做的不多，所以只有几块。这是主控制器:

其中的主要部分是第 17–21 行:

```
return newsReactiveRepository
                .findAll()
                .flatMap(r -> Flux.fromIterable(r.getArticles()))
                .filter(r -> filterByDate(r, from, to))
                .limitRequest(actualLimit);
```

`newsReactiveRepositoy`是 MongoDB 集合的主要访问点，我不小心将其命名为`inserts`，因为这是我在编写 Node.js 部分时复制的示例中集合的名称。好吧，就叫它`inserts`我会把它留在那里让我保持谦逊。集合名是从类名中推断出来的，所以为了访问`inserts`集合，我们使用了一个`Inserts`类，我将在后面描述。

`newsReactiveRepository.findAll()`返回一个`Flux<Inserts>`。这是[项目反应器](https://projectreactor.io/)的一部分，对于那些熟悉 Java 流的人来说，你会发现`Flux`有点类似于`Stream`。你有一套类似的方法，比如`map`和`filter`。它的作用是允许你设计一个分解成函数的流程，然后这些函数由调度程序执行。

关键是，没有一个函数可以阻塞，因此调度程序可以预期，如果它调用一个函数，它将几乎立即返回，调度程序可以调度其他函数。这样，整个流程和所有请求处理都可以在单个线程(或者任意数量的小线程)上进行。事实证明线程是相对昂贵的，每个请求一个线程，这是 Java 中的传统路线，在处理大量请求时会成为一个很大的瓶颈。尽管 Node.js 是单线程的，但这种非阻塞的特性使得它具有令人惊讶的良好性能。现在同样的概念在 Java 中很容易实现。

上面的代码片段显示了我们正在对从 MongoDB 返回的`Inserts`对象流做三件事。首先，我们将它分成不同的文章，因为新闻阅读器服务将数据作为单个对象与文章集合一起插入数据库。其次，我们根据 URL 的`from`和`to`参数传递的日期进行过滤。最后，我们将根据最少的`limit`参数限制返回的文章数量，如果没有限制参数，则返回 1000 篇文章。

我应该注意到这个订单的一个问题；因为我们是最后限制，如果过滤器丢弃了所有东西(比如过去有一个`to`参数),我们将扫描整个`inserts`集合并且不返回任何东西。有许多方法可以解决这个问题，但是由于我们的数据集现在只有几个项目，我将把它推迟到以后。

我使用 Lombok 库创建了一个名为`Inserts`的 bean 类。Lombok 产生所有需要的 getters 和 setters、constructors 和 builders。它在类前使用一个注释`@Data`,并通过 Maven magic 自动生成带有已填充类的文件。任何严肃的 Java 程序员都必须使用的库。上课来了

由于我们在插入过程中没有指定一个`id`，所以`id`字段是由 MongoDB 生成的。从数据库中读取行需要一个`Repository`接口，Spring 会在运行时为您填写这个接口。这就是你所需要的:

```
@Repository
public interface InsertsReactiveRepository extends
            ReactiveMongoRepository<Inserts, String> { }
```

除此之外，您只需要一些样板 Spring 类，就有了一个完整的应用程序。你可以在这里找到所有的源代码。

同样，我创建了一个`docker-compose.yaml`用于测试目的。它会打开 MongoDB 和我们的新闻阅读器应用程序，只是这次新闻阅读器应用程序是从现在存储在 DockerHub 中的图像中提取的。这里是新的`docker-compose.yaml`

我们不需要额外的构建步骤，因为我们正在使用之前创建的映像。所以我们只需要这两步

```
docker-compose up -d mongodb
docker-compose up app
```

第一行启动 MongoDB，最后一行将在数据库中插入一行。

数据库启动后，您可以从命令行作为 jar 运行应用程序，也可以从您的 IDE 运行应用程序。然后，您可以使用 Postman 点击 URL `http://localhost:8080/v1/headlines?limit=50&to=2021-01-01T00:00:00Z`，并将`Accept`头设置为`text/event-stream`(这是我们的控制器设置为产生的唯一内容)。你应该看到一堆新闻吐槽。您也可以在浏览器中查看它。只是为了好玩，你也可以浏览到`http://localhost:8080/actuator/health`端点和`http://localhost:8080/actuator/info`端点。您可以从最后一个端点看到我们如何使用来自`git-commit-id-plugin`的信息。

完成测试后，运行`docker-compose down`来停止 MongoDB。

要构建映像，您可以运行`mvn`命令:

```
mvn clean install jib:build
```

这将把你的新镜像安装到 DockerHub。

# 在 Kubernetes 上运行一切

本文的其余部分将放在主主机上，我使用主主机通过`kubectl`命令访问 Kubernetes。我将为描述文件创建一个新的 GitHub 存储库，我将用它来将所有这些部署到 Kubernetes。您可能会认为我喜欢创建这么多小的存储库，但是这是有目的的。您希望您的微服务彼此完全独立，最好的方法是创建单独的存储库。您还希望您的微服务独立于它们的部署方式，因此需要一个新的部署脚本存储库。明天，一种新的容器编排服务可能会席卷全球，并且您会很高兴部署文件没有包含在代码库中。在主主机上克隆新的空存储库，并将`cd`复制到新目录中。

我们首先需要在 Kubernetes 上运行一个 MongoDB 实例。如果你一直在关注我，你可能读过我的系列文章 [Kubernetes From Scratch](https://medium.com/better-programming/kubernetes-from-scratch-4691283e3995) ，其中我构建了一个 Kubernetes 系统，而没有使用像 minikube 或 microk8s 这样的预打包设置。在我的文章 [Kubernetes From Scratch 第二部分](https://medium.com/better-programming/kubernetes-from-scratch-part-2-e30b48f7ca6b)中，我提到了一个 MongoDB 数据库，所以如果您遵循这个方法，您已经有了一个 MongoDB 的实例。如果没有，我将带您完成一个非常基本的 MongoDB 设置。

到目前为止，我们的许多部署都是在`default`名称空间中进行的。这是一个不好的习惯，我们真的应该创建名称空间来给自己一点隔离，并组织我们的微服务系统。在一个经过深思熟虑的系统中，我们将为名称空间以及如何组织它们创建一个计划，按项目、按环境或两者都按。但出于本文的目的，我将只为微服务及其所需的外部服务创建一个。我将为 newreader/readnews 创建一个名为`nr`的。我喜欢短的名称空间名称，因为除非您设置一个指定名称空间的上下文，否则您必须在每个`kubectl`命令后面添加`-n <namespacename>`。

创建一个名为`namespace.yaml`的文件，并输入以下内容:

现在我们有了一个名称空间，我们必须创建一个密码，MongoDB 和访问它的应用程序可以使用它作为根用户名/密码。要创建它，请运行以下命令:

```
kubectl create secret generic mongo-secret -n nr \
    --from-literal=username=mongo \
    --from-literal=password=ognom
```

确保在你的生产系统中使用更好的密码，哈哈。

如果您阅读了我的文章 Kubernetes From Scratch Part II，那么您已经有了一个名为`gluster-pv`的持久卷。但是我们需要在新的名称空间下创建一个新的。如果您不明白，您可以跳过这一部分，我们将为您的 MongoDB 使用临时存储。删除`PersistentVolume`和`PersistentVolumeClaim`以及`Endpoints`，如果它们存在于上一篇文章中。我们将在`nr`名称空间中重新创建它们，但略有不同。假设您有一个 GlusterFS 集群在运行(参见我的文章[构建您自己的家庭云存储](https://medium.com/better-programming/build-your-own-in-home-cloud-storage-1aa74b5c6397))，下面是`endpoints.yaml`、`pv.yaml`和`pvc.yaml`的内容:

在`endpoints.yaml`中，确保将 IP 地址更改为 GlusterFS 集群的 IP 地址。将它们按顺序应用到我们的新名称空间中:

```
kubectl apply -f endpoints.yaml -n nr
kubectl apply -f pv.yaml -n nr
kubectl apply -f pvc.yaml -n nr 
```

同样，如果您没有 GlusterFS 集群，您仍然可以稍加修改继续。

您可以使用下面的`mongodb.yaml`部署一个单节点 MongoDB 实例:

如果您没有 GlusterFS 并跳过了上述关于设置永久卷声明的说明，您可以将`spec.template.spec.volumes`从`persistenVolumeClaim`更改为`emptyDir: {}`并移除`claimName`。`emptyDir`卷只是 pod 空间中的一个空目录。如果 pod 消失了，卷及其所有数据也会消失。

使用以下命令应用名称空间为`nr`的`mongodb.yaml`

```
kubectl apply -f mongodb.yaml -n nr
```

现在运行`kubectl get all -n nr`以查看一切都在运行:

```
rkamradt@beast:~/news-deploy$ kubectl get all -n nr
NAME            READY   STATUS    RESTARTS   AGE
pod/mongodb-0   1/1     Running   0          9sNAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
service/glusterfs-cluster   ClusterIP   10.107.89.123   <none>        1/TCP       131m
service/mongodb             ClusterIP   None            <none>        27017/TCP   9sNAME                       READY   AGE
statefulset.apps/mongodb   1/1     9s
```

为了拥有多个副本，需要一个更加动态的卷供应系统，这就要求我们的存储解决方案更加复杂。如果您通过云提供商运行，他们应该有动态卷供应，并且可以添加更多副本。但是我们会处理一个复制品。

MongoDB 服务是“无头”服务，因为它没有 ClusterIP。它为您做的是为每个 pod 创建 DNS 名称，因为所有的 pod 都需要可访问。这对我们来说并不重要，因为我们只有一个副本集。单个 pod 的 DNS 名称应该是`mongodb-0.mongodb`

接下来，我们需要为`newsreader`服务创建一个`CronJob`，这样它将收集整点的所有新闻。但是首先，我将把它部署为一个裸 pod 来进行测试。创建一个名为`newsreader-pod.yaml`的文件，并添加以下内容:

在部署它之前，我们需要为 API 密钥创建秘密。

```
kubectl create secret generic newsapi-key -n nr \
    --from-literal=key=<your-newsapi-key>
```

然后使用以下工具部署 pod:

```
kubectl apply -f newsreader-pod.yaml -n nr
```

然后(假设它起作用了，在几个运行/编辑周期后它对我起作用了)，您可以检查日志:

```
rkamradt@beast:~/news-deploy$ kubectl logs pod/newsreader-pod -n nr
Connecting to mongodb://mongo:ognom@mongodb-0.mongodb:27017
Connected correctly to server
Reading [https://newsapi.org/v2/top-headlines](https://newsapi.org/v2/top-headlines)
Inserting news to server
Closing mongoDB connection
Ending
```

糟糕，这指出了一个问题—密码在日志中是可见的。我将不得不回到`newsreader`微服务，并纠正这一点。但除此之外，它非常有效！注意，pod 名称直接取自`metadata.name`字段，没有附加任何随机字符串。我们可以用`kubectl delete -f newsreader-pod.yaml -n nr`移除吊舱。

现在我们可以创建文件`newsreader-cronjob.yaml`，其中大部分来自于`newsreader-pod.yaml`。以下是我想到的内容:

这将在整点运行作业。然后以正常方式部署它:

```
kubectl apply -f newsreader-cronjob.yaml -n nr
```

我们可以看到正在运行的内容:

```
rkamradt@beast:~/news-deploy$ kubectl get all -n nr
NAME            READY   STATUS    RESTARTS   AGE
pod/mongodb-0   1/1     Running   0          55mNAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
service/glusterfs-cluster   ClusterIP   10.107.89.123   <none>        1/TCP       3h7m
service/mongodb             ClusterIP   None            <none>        27017/TCP   55mNAME                       READY   AGE
statefulset.apps/mongodb   1/1     55mNAME                               SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/newsreader-cronjob   0 * * * *   False     0        <none>          11s
```

还没有创建任何工作或 pod，我们必须等到整点。默认情况下,`CronJob`会保留最近三个成功的作业和最近一个失败的作业，以便您可以查看日志。

最后，我们需要部署`readnews`服务。这应该很简单，除了我们还需要为它创建一个`Ingress`。我们将从创建一个`readnews.yaml`开始，如下所示:

我本来可以添加一个健康检查，因为该服务有健康检查端点，但是我将把它留给另一篇文章。我们可以通过端口转发来测试它:

```
kubectl port-forward service/readnews-service 8080:8080 -n nr
```

在另一个终端窗口中(也是在主机上),看看是否可以访问它

```
curl http://localhost:8080/v1/headlines
```

如果作业已经运行过一次，您应该会看到一串标题流过。如果看不到任何内容，请检查作业是否已运行。

```
rkamradt@beast:~/news-deploy$ kubectl get all -n nr
NAME                                       READY   STATUS      RESTARTS   AGE
pod/mongodb-0                              1/1     Running     0          140m
pod/newsreader-cronjob-1588525200-w9j9s    0/1     Completed   0          56m
pod/readnews-deployment-84774799cf-44nzx   1/1     Running     0          12mNAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
service/glusterfs-cluster   ClusterIP   10.107.89.123   <none>        1/TCP       4h31m
service/mongodb             ClusterIP   None            <none>        27017/TCP   140m
service/readnews-service    ClusterIP   10.99.135.171   <none>        8080/TCP    61mNAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/readnews-deployment   1/1     1            1           61mNAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/readnews-deployment-84774799cf   1         1         1       12m
replicaset.apps/readnews-deployment-d886f5f75    0         0         0       61mNAME                       READY   AGE
statefulset.apps/mongodb   1/1     140mNAME                                      COMPLETIONS   DURATION   AGE
job.batch/newsreader-cronjob-1588525200   1/1           11s        56mNAME                               SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/newsreader-cronjob   0 * * * *   False     0        56m             84m
```

您应该看到带有 1/1 完成的`job.batch`。如果没有，您将不得不等待它运行的整点。

要创建一个`Ingress`，您必须为新的名称空间创建一个`Issuer`。如果您阅读了我的文章[如何使用 Kubernetes Ingress](https://medium.com/better-programming/how-to-expose-your-services-with-kubernetes-ingress-7f34eb6c9b5a) 来公开您的服务，您将已经拥有一个证书和一个私钥。如果没有，并且您不想回去阅读，您可以跳过创建`Issuer`，而`Ingress`将为您创建一个假证书。

如果您仍然有文件`ca.key`、`ca.unencrypted.key`和`ca.crt`，您可以将证书密钥对添加到我们新名称空间中的一个秘密中:

```
kubectl create secret tls ca-key-pair -n nr \
    --cert=ca.crt \
    --key=ca.unencrypted.key
```

用文件`issuer.yaml`为这个名称空间创建一个`Issuer`

然后将其应用于新的名称空间

```
kubectl apply -f issuer.yaml -n nr
```

现在用名为`vhosts-ingress.yaml`的文件创建一个`Ingress`

这将创建一个名为`readnews.local`的虚拟主机，并将所有请求转发给 readnews 服务。它还会为新的主机名生成一个证书，这样你的浏览器就不会变得古怪。将其添加到命名空间中:

```
kubectl apply -f vhost-ingress.yaml -n nr
```

为了能够在您的浏览器上看到它，您应该在`/etc/hosts`文件中添加主主机的同义词(我的名为 beast ):

```
192.168.0.225 beast readnews.local
```

现在你应该可以在浏览器中查看 http://readnews.local/v1/headlines 的网址了。瞧啊。

![](img/e8b5f822b6d68718d28101381ad557ed.png)

你厌倦打字了吗？您可以将名称空间添加到您的上下文中，使其成为默认名称空间。

```
kubectl config set-context --current --namespace=nr
```

哈哈，你不希望我一开始就告诉你吗？有时有一个默认的名称空间是很好的，特别是当名称空间特定于您的工作角色时。但是其他时候，明确地命名它是好的，这是一个好习惯。

所以，我又浪费了一个周末去做一个毫无用处的应用程序。但我学到了很多新技能，磨练了一些老技能，当我终于有所收获时，我仍然会兴奋不已。我想这就是我三十多年后仍在这个行业的原因。我希望你和我一样喜欢学习，这会让你在计算机世界里走得更远。谢谢你又陪我度过了一个周末。你可以在这里找到这篇文章的代码[，在这里](https://github.com/rkamradt/newsreader/tree/v1.1)找到[，在这里](https://github.com/rkamradt/readnews/tree/v1.0)找到