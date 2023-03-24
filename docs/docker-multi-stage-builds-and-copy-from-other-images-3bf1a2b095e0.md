# 关于 Docker 多阶段构建和复制—来自

> 原文：<https://levelup.gitconnected.com/docker-multi-stage-builds-and-copy-from-other-images-3bf1a2b095e0>

## 如何利用多阶段构建和复制来制作小而简洁的图像

![](img/d87e13319e19c6fec812ad99ab507e99.png)

[Artem Labunsky](https://unsplash.com/@labunsky?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/assembly?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

今天，我对 Docker [多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)和`[COPY --from](https://docs.docker.com/engine/reference/builder/#copy)`构建命令有了愉快的体验。Docker 多阶段构建允许您通过在单个 docker 文件中使用多个`FROM`语句来减少最终图像的大小和复杂性。

每个`FROM`语句都引入了一个新的构建阶段，也可以使用`FROM as name`为其命名。在`docker build`之后，除了最后一个阶段之外的所有阶段的层都将被擦除(即最后一个`FROM`开始正在构建的实际图像)。)在构建过程中，工件可以使用`COPY --from=name src dest`(或者一个从 0 开始的数字，用于未命名的阶段)从一个阶段复制到另一个阶段。)但我或多或少偶然发现的一个事实是，`COPY --from=image:tag`也可以用来从其他现有图像中复制工件，这可能使您能够从几个现有图像中创建一个图像:

> 可选地,`COPY`接受一个标志`--from=<name|index>`,该标志可用于将源位置设置为先前的构建阶段(用`FROM .. AS <name>`创建),该构建阶段将代替用户发送的构建上下文。该标志还接受一个数字索引，该数字索引分配给从`FROM`指令开始的所有先前构建阶段。如果找不到具有指定名称的构建阶段，则会尝试使用具有相同名称的映像。[来源](https://docs.docker.com/engine/reference/builder/#copy)

我很喜欢这两个发现，就像我想在本文中分享它们一样；也许我甚至可以给一两个码头工人的工具箱增加一些有价值的东西。

# 使用案例

我需要构建一个 docker 映像，提供一个 MySQL 数据库，其中的数据已经在容器启动时被填充。官方的 [mysql](https://hub.docker.com/_/mysql/) docker 镜像提供了一个目录`/docker-entrypoint-initdb.d`，用户可以将`.sql`文件放入其中，这些文件将在容器启动时加载。但对我来说这还不够。事实上，我必须加载一些`.sql`文件，但之后我需要运行一个 Java 程序，用更多的数据扩充数据库(基于加载的`.sql`文件)，所以这是一个动态任务，可能会随着时间的推移而改变。)

这个最终的数据库，或者更准确地说是它的数据，必须在 docker 映像中捕获，这样开发人员就可以快速构建一个功能完整的填充数据库。我的计划是启动一个 MySQL 服务器，导入`.sql`文件，然后针对该数据库运行我的 Java 程序，最后生成一个 SQL 转储(即将数据库中的所有数据转储到一个文件中)。)如果我有这样的 sql 转储，那么创建一个 [mysql](https://hub.docker.com/_/mysql/) docker 映像并将转储复制到`/docker-entrypoint-initdb.d`将是轻而易举的事情。幸运的是，docker 为我提供了以非常简洁的方式实现整个过程的工具。

# 解决方案

用例中描述的过程可以在`Dockerfile`的上下文中执行，这意味着它可以通过简单地运行`docker build ...`来触发。让我们开始吧！

你应该注意到的第一件事是我们有两个`FROM`语句，一个在最开始，一个在最后。这就是多阶段建造的行动。正如你所看到的，第二个`FROM`语句下面的指令被简化为一个`COPY`，这将是 docker build 生成的最终映像中唯一添加到 mysql 基础映像的层！

让我们来看一下说明。

第一阶段以`mysql:5`图像为基础，命名为`sqldump`。工作目录更改为`home`，这是放置 sql 转储的位置，也是第二阶段复制 SQL 转储的位置。

`WORKDIR`后面是几个`COPY`指令，它们将把需要的工件复制到映像中，例如:`.sql`文件、java 程序和一个`sqldump.sh` shell 脚本，我将很快介绍它们的内容。您可以将 shell 指令直接放入 docker 文件中，而不是将 sql 转储外部化到单独的脚本中。然而，拥有大量的`RUN`语句会降低构建速度，因为每条语句都会创建一个新的图像[层](https://docs.docker.com/storage/storagedriver/#images-and-layers)，而使用`[&&](https://www.gnu.org/software/bash/manual/bash.html#Lists)`将`RUN`语句减少为一条`RUN`语句会使代码难以阅读、理解和维护。

最后两个`COPY`语句是俏皮的！这里的情况是，我的图像基于 MySQL，但是我还需要一个 JDK 和 Gradle 来运行我的 java 程序。实现这一点的一个方法是手动安装它们，使用许多安装 java 所需的`apt-get install`和其他“添加存储库证书”的东西(一点也不好玩。)从现有镜像中复制这些程序的完整安装要简单和快速得多。而这正是我正在做的，我将整个 Gradle 和 Java 安装从`[gradle:jdk11](https://hub.docker.com/_/gradle/)`镜像复制到当前镜像，将这些程序的安装留给那些[维护](https://github.com/keeganwitt/docker-gradle/blob/6596a2c43781107a48acd7520149bb153f10ea06/jdk11/Dockerfile) `gradle:jdk11`的伟人。剩下唯一要做的事情是设置`$PATH`变量来解析`java`和`gradle`可执行文件，这将在 shell 脚本中完成。

`ARG MYSQL_ROOT_PASSWORD=root`是连接 MySQL 服务器的密码。

`sqldump`阶段的最后一步是运行 shell 脚本，为此我们首先使用`chmod`使其可执行。下面是将从现在开始接管的 shell 脚本内容:

大多数说明是不言自明的，我会快速浏览它们，并捕捉值得注意的东西。

`[docker-entrypoint.sh](https://github.com/docker-library/mysql/blob/6952c5d5a9889311157362c528d65dc2e37ff660/5.7/docker-entrypoint.sh)`是由 [mysql 镜像提供的 MySQL 启动脚本。](https://hub.docker.com/_/mysql/)该行负责在后台启动服务器(由末尾的`&`实现)。

将`initial.sql`加载到数据库中，然后使用`gradle run`执行 java 程序。之所以使用 Gradle，是因为该程序依赖于第三方库，使用 gradle 或 maven 之类的工具解决这些依赖似乎比自己下载更简单。

最后`[mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html)`(在 [mysql 镜像](https://hub.docker.com/_/mysql/)中提供)，生成我们想要的转储文件，包含所有需要的 db 模式！

就这样，docker 现在将移动到第二阶段，第一阶段的`dump.sql.gz`文件被复制到`/docker-entrypoint-initdb.d`，仅将单个图像[层](https://docs.docker.com/storage/storagedriver/#images-and-layers)添加到基础图像。当`docker build ...`结束时，第一阶段的所有图层都将从你的磁盘中删除。

# 结论

我习惯于将预先构建的工件复制到我的 docker 映像中，并在`docker build`上下文之外构建这些工件。Docker [多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)和`COPY --from=image:tag`让我意识到，Docker 有能力处理更多的构建过程，从而从想要构建软件的系统中带走更多的责任和约束。我经常以在 CI/CD 管道中编译和准备工件而告终，并冠以一个`docker build`指令，该指令只将这些工件复制到映像中。将更多的工作转移到 other 文件中使得构建对其他开发人员更加透明，并且更容易在不同的机器上执行。CI/CD 流水线减少到单个`docker build`指令。[多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)确保最终图像仍然尽可能精简。

## 编码快乐！✌️