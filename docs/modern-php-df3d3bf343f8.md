# 现代 PHP(第 1 部分)

> 原文：<https://levelup.gitconnected.com/modern-php-df3d3bf343f8>

## 使用 Upwork 中最受欢迎的项目语言之一开始您的自由职业生涯。

![](img/d93ff7ae718d6b347b4fac51f87dfc0f.png)

照片由[Cookie Pom](https://unsplash.com/@cookiethepom?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/smash-computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

PHP 是最初的 web 后端编程语言之一。不幸的是，它不是最好的通用语言，原始版本缺乏现代编程语言的组织细节。因此，有很多不太完美的 PHP 运行网站，其中很多需要修复或增强。

你想开始自由职业吗？快速浏览一下 [Upwork](https://www.upwork.com/) 的工作列表，你会发现 PHP 是人们最需要帮助的项目之一。大多数清单会希望你去清理别人留下的烂摊子，一些倒霉的程序员熬了太多的通宵来获得(勉强)工作的东西。不好看。但这就是自由职业者赚钱的地方。你可能去过这些网站中的一个，那里的事情并不完全按照你期望的方式进行，或者缺乏现代水疗网站的无缝链接。这就是你要做的。

PHP 现在有一个名为 [Composer](https://getcomposer.org/) 的依赖管理器，我将使用 [Symfony](https://symfony.com/) 框架。所有这些都需要 PHP 版本 7，如果您必须在旧的 PHP 版本上工作，这可能是一个问题。但是学习新的方式会让你轻松进入你可能遇到的一些过去的石头工具。

让我们把东西装好。首先，如果你还没有 PHP，你应该得到它。由于我在 Mac 上，预装的 PHP 版本是 5，所以你可以使用`brew`来获得 7 版安装。如果你在另一个操作系统上，你可以按照这里的说明。

接下来，您需要安装 Composer。您可以在这里找到安装说明。我移动了运行 Composer 的脚本，这样我就可以使用以下命令全局访问它:

```
mv composer.phar /usr/local/bin/composer
```

现在，您应该能够在命令提示符下键入`composer`并获得帮助列表。

最后，你应该安装 Symfony。用[这些指令](https://symfony.com/download)下载并试用它:

```
randalkamradt@Fuji github % symfony check:requirementsSymfony Requirements Checker
~~~~~~~~~~~~~~~~~~~~~~~~~~~~> PHP is using the following php.ini file:
WARNING: No configuration file (php.ini) used by PHP!> Checking Symfony requirements:...............[WWW](http://WWW)........[OK]                                         
 Your system is ready to run Symfony projectsOptional recommendations to improve your setup
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~* intl extension should be available
   > Install and enable the intl extension (used for validators).* a PHP accelerator should be installed
   > Install and/or enable a PHP accelerator (highly recommended).* short_open_tag should be disabled in php.ini
   > Set short_open_tag to off in php.ini*.
```

现在运行 Symfony 创建一个新的 web 应用程序

```
symfony new phpapp --full
```

这将创建一个名为`phpapp`的目录，并将您的新 web 应用程序放入其中！它还将为您的新应用程序创建一个 git 存储库。我们应该把它连接到 GitHub，这样我们就可以保存我们的初始状态。在 GitHub 中，创建一个名为`phpapp`的空存储库。然后回到命令行，发出以下命令:

```
git branch -M main
git remote add origin [git@github.com](mailto:git@github.com):<yourgithubname>/phpapp.git
git push -u origin main
```

这是我们的底线。但是我们能用它做什么呢？启动它，让我们看看:

```
symfony server:start
```

![](img/8cf4fbd10cdbfc6eae0080502facdb3b.png)

对于 404 屏幕来说相当漂亮。回到命令行，按 Ctrl-c 停止服务器。

现在怎么办？相当多的自由职业者需要将表单添加到现有的应用程序中，或者新的应用程序只提供一个表单。前者将由一个数据库支持，按照传统方式，这个数据库很可能是 MySQL。

## 向云迁移

在过去，你会有一堆灯；Linux、Apache、MySQL 和 PHP。我要让你看起来更现代一点，去 DNMP，这不是一个好的缩写，但在现代集装箱化的世界里会有用。我们将使用 Docker、Nginx、MariaDB 和 PHP。请注意，我说的是使用而不是安装。好处是你只需要在云中的虚拟机上安装 Docker 和一个`docker-compose.yml`文件。所有内容都是从预先构建的映像中加载的。我在 Mac 上安装了 Docker 进行测试，如果你没有，你可以在这里下载。

这就是有点奇怪的地方。PHP 不是为容器化而写的。它期望一个安装了各种好东西并可用的虚拟机，我们必须将这种安装整理成文。这些东西有些是用来建筑的，有些是用来跑步的，两者之间的区别很模糊。在过去，您只需在运行的同一台机器上进行构建，所以这并不重要。现在，我们希望超薄容器只包含运行所需的内容，并且构建过程在我们构建容器时进行。通常，我们会在 Docker 中使用多阶段构建。

然而，我将构建一个胖映像，只需包含我们上面安装的所有内容。我们将创建一个名为 Dockerfile 的文件，并在其中添加我们刚刚浏览过的所有安装说明。我们可以从 PHP 7.3 开始作为基础镜像，然后安装 Composer，最后安装 Symfony。这是我想到的:

这绝不是优化。但是话说回来，当你为那些只想在云中为他们的数据库创建一个表单的人工作时，simple 每次都是赢家。

现在让这个东西建成并运行。这是你需要的一切:

```
docker build -t <yourdockerhubname>/phpapp .
docker run -p 80:8000 <yourdockerhubname>/phpapp
```

现在你可以打开浏览器到 [http://localhost/](http://localhost/) 瞧！你应该看看你之前的页面。请注意，我们不必使用 8000 端口，因为我们将它映射到了 80。在未来，我们将让 Nginx 读取端口 80，并转发到我们运行 8000 的应用程序。Ctrl-c 停止服务器。

我应该提到，这可能不是做这件事的最佳方式。Apache 和 Nginx 都有插件，可以更有效地与 PHP 后端对话。但是，在大多数情况下，简单是正确的。如果有人想要可扩展的高效解决方案，他们不会去找 Upwork，这就是我学习这门古老语言的全部原因。Upwork 的人想要一个新的表单来匹配数据库中的新表，他们明天就要。抛开优雅，今天就把它做完。

## 部署到云

在转到数据库之前，我还有一个技巧要表演，因为它是容器化的。让我们把它移到云上。我正好有一个谷歌云平台的账号，一个域名，还有一个 CloudFlare 账号。让我们把这个东西放到真正的互联网上。如果你没有那三样东西，也不想设置，可以跳过这一节。这应该也适用于大多数其他云服务，这些云服务提供带有标准 Linux 映像的裸机。首先，让我们将创建的图像推送到 DockerHub，这样我们的云计算机就可以找到它

```
docker push <yourdockerhubname>/phpapp
```

在您的 GCP 控制面板上，转到“计算引擎/虚拟机实例”页面并添加一个新虚拟机。使用他们拥有的最小的虚拟机，通常是微型的。我选择了一个 10gb 硬盘的 Ubuntu 20.04 基础镜像，打开 HTTP 访问。安装之后，你应该在你的虚拟机列表中看到类似这样的内容。

![](img/c99e8096b5b9dbca4b0f8fd4c8fe9174.png)

单击 SSH 按钮打开虚拟机窗口。您必须安装 docker，登录 docker，并运行您之前使用的相同命令

```
sudo snap docker
sudo docker login 
sudo docker run -p 80:8000 <yourdockerhubname>/phpapp
```

您应该看到它滚动浏览日志，直到它说它正在监听端口 8000。

虚拟机列表中的第二个 IP 地址是外部地址，您可以转到 CloudFlare DNS 并设置一个包含主机名和该 IP 的 A 记录。现在你的应用程序应该可以作为 http://hostname.yourdomain.com/的

想想看:如果你的虚拟机崩溃了，你需要重新创建你的应用程序，完整的安装只需要三行代码。三条线。如果需要更新 app，就一行。如果你使用像 Kubernetes 这样的编排服务，那就更简单了。容器会让你在云中的生活变得没有重量。

## 添加页面

在连接数据库之前，我们先做几页。首先，我们将创建一个只有简单文本的主页、一个关于页面和一个列表页面。让我们添加一些依赖项:

```
composer require twig
composer require symfony/form
composer require symfony/asset
composer require symfony/orm-pack
composer require --dev symfony/maker-bundle
```

这些应该马上就可以在您的本地环境中使用，并且在您下次重新构建 Docker 映像时会出现在其中。这将允许模板，一些基本的形式符号，容易包含资产，如 CSS 和图像，以及一些数据库访问和效用。

我将从一个基本的 Twitter Bootstrap 开始，我可以在基本模板中设置它。那么从该模板继承的任何东西都将有一个标准的布局和一组样式来使用。这些都放在`templates/base.html.twig`文件中。

带有块`content`的 div 块是你的内容将要去的地方。例如，让我们创建一个简单的关于页面，名为 templates/about.html.twig:

```
{% extends 'base.html.twig' %}{% block content %}
<div class="hero-unit" style="background: LightGreen">
  <h3>This is a list of wine and cheese pairings</h3>
</div>
{% endblock %}
```

如您所见，它扩展了 base.html.twig 并有一个名为 content 的块。您必须向 config/routes.yaml 中添加新页面:

```
index:
    path: /
    controller: App\Controller\Home::index
list:
    path: /list
    controller: App\Controller\Home::list
about:
    path: /about
    controller: App\Controller\Home::about
```

并扩展 src/Controller/Home.php:

```
<?php
// src/Controller/LuckyController.php
namespace App\Controller;use Symfony\Component\HttpFoundation\Response;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;class Home extends AbstractController
{
  public function index(): Response
  {
      return $this->render('home.html.twig', []);
  }
  public function list(): Response
  {
      return $this->render('list.html.twig', []);
  }
  public function about(): Response
  {
      return $this->render('about.html.twig', []);
  }
}
```

由于我们只在每个页面上添加简单的文本，home.html.twig 和 list.html.twig 将是 about.html.twig 的副本，只是做了一些文本更改，所以我们知道我们已经将正确的页面连接到了正确的 URL 路径。当把它放在一起时，它看起来像这样:

![](img/66813e3d308291006a863a2c42ee9dc0.png)

## 添加数据库

不是静态的网站需要某种后台存储。我们将使用 MariaDB。MariaDB 是在 MySQL 被 Oracle 收购后从 MySQL 中分出来的，因为最初的开发人员认为 Oracle 在维护它时可能会有利益冲突。MariaDB 仍然是几乎完全插件兼容的，所以您应该能够在不改变编程的情况下将一个替换为另一个。

对于我们的本地开发，我们不打算安装 MariaDB，我们将使用一个公开可用的 Docker 映像。我们不打算改变 Docker 图像，我们只是设置一个`docker-compose.yml`，它将启动 MariaDB 和一个管理应用程序。它看起来是这样的:

```
version: '3.1'
services:
  mariadb:
    image: mariadb
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
    ports:
      - 3306:3306
  adminer:
    image: adminer
    restart: always
    ports:
      - 7000:8080
```

这包括一个管理控制台。注意，我将管理控制台的外部端口映射到 7000，因为我有一个 Jenkins 实例在 8080 上运行。还要注意，密码只是为了开发，显然，当我们在任何公开的地方部署它时，我们会从环境或其他地方读取它。要开始运行该命令:

```
docker-compose up
```

你会看到很多滚动，最终它会停止。现在你可以进入 [http://localhost:7000/](http://localhost:7000/) ，用 root/example 登录，暂时将数据库留空。您应该看到这个:

![](img/c1b3be3b023efe2d976f5d23028c3dd9.png)

您可以使用 Ctrl-c 停止数据库和管理控制台，它将保留您的数据，然后下次启动它(数据存在于容器中)。但是如果你把容器拿下来:

```
docker-compose down
```

它将丢失数据。这在开发中很方便，但显然是我们在生产中不想要的。我不会在这里讨论如何解决这个问题，也许在以后的文章中。现在，您有了一个可以上下运行的数据库。

在。env 文件中，您将找到数据库的 URL。您可以将其更改为:

```
DATABASE_URL=mysql://root:example@mariadb:3306/winelist?serverVersion=mariadb-10.4.11
```

我们再次使用开发专用密码，这将在生产部署中被覆盖。您还会注意到主机名是`mariadb`。这就是为什么当我们想用 docker-compose 测试它时，它会在 docker 中运行。为了让它在 docker 之外运行，我编辑了我的/etc/hosts 文件，将`mariadb`作为 localhost 的别名。

确保您的数据库和管理控制台仍然运行，我们可以使用与`orm`包一起安装的实用程序来创建一个数据库。它将从 URL 获取数据库名称。

```
php bin/console doctrine:database:create
```

现在刷新您的管理控制台，以确保它在那里

![](img/da46852624a1532a91c8b620eced1002.png)

现在我们可以用另一个实用程序创建一个实体类:

```
php bin/console make:entity
```

该命令将引导您完成这些字段。我们将创建字段`wine`、`wine_description`、`cheese`、`cheese_description`和`pairing_notes`。一旦你完成了，你应该有一个新文件`src/Entity/WinePairing.php`。如果您仔细观察，您会注意到它为您创建了一个`id`字段。它还创建了`src/Repository/WinePairingRepository.php`，将为您提供`findAll`和`persist`方法，这是我们现在要使用的所有方法。现在，您可以在数据库中创建表:

```
php bin/console make:migration
```

这将创建一个脚本来创建数据库。该命令的输出将包含类似如下的下一个命令:

```
php bin/console doctrine:migrations:migrate
```

您可以确认该表是在 MariaDB 管理控制台中创建的。

## 创建 CRUD 页面

我们将只做一个列表和一个添加页面，一旦你掌握了它的窍门，其他的就相对容易理解了。首先是列表页面。我们已经有了路线，我们只需要添加实际的列表。下面将通过 src/Controller/Home.php 将整个列表发送到列表表单:

```
public function list(): Response
  {
    $wineList = $this->getDoctrine()
        ->getRepository(WinePairing::class)
        ->findAll();
      return $this->render('list.html.twig', [
        'wineList' => $wineList,
      ]);
  }
```

务必添加`use App/Entity/WinePairing`以引入课程。我们现在不打算解决分页问题。现在，您可以在 templates/list.html.twig 中填写:

```
{% extends 'base.html.twig' %}{% block content %}
<div class="hero-unit" style="background: LightGreen">
    {% for wine in wineList %}
        <h2>{{ wine.wine }}</h2>
        <p>{{ wine.cheese }}</p>
    {% endfor %}
    <a href="/add" class="btn btn-primary btn-large">Add</a>
</div>
{% endblock %}
```

请注意，我们在列表底部添加了一个“添加”按钮。如果您现在转到列表页面，您将不会看到列表，因为还没有数据，但是您会看到添加按钮。如果你点击它，你会得到一个错误，因为路径没有被定义。所以把它加入你的`config/routes.yaml`:

```
index:
    path: /
    controller: App\Controller\Home::index
list:
    path: /list
    controller: App\Controller\Home::list
about:
    path: /about
    controller: App\Controller\Home::about
add:
    path: /add
    controller: App\Controller\Home::add
```

然后您可以在您的`src/Controller/Home.php`中定义它:

```
public function add(Request $request)
  {
      $wine = new WinePairing();$form = $this->createFormBuilder($wine)
          ->add('wine', TextType::class)
          ->add('wine_description', TextType::class)
          ->add('cheese', TextType::class)
          ->add('cheese_description', TextType::class)
          ->add('pairing_notes', TextType::class)
          ->add('save', SubmitType::class, ['label' => 'Create Pairing'])
          ->getForm();$form->handleRequest($request);
      if ($form->isSubmitted() && $form->isValid()) {
          $task = $form->getData();$entityManager = $this->getDoctrine()->getManager();
          $entityManager->persist($task);
          $entityManager->flush();return $this->redirectToRoute('list');
      }return $this->render('add.html.twig', [
          'form' => $form->createView(),
      ]);
  }
```

这是很多！但是基本上您将使用 Symfony 的内置表单生成器。这个路径通过 GET 和 POST 动词处理表单显示和表单提交。条件`if($form->isSubmitted() && $form->isValid())`将表单数据保存到数据库，然后将您重定向回列表。否则，它将使用`templates/add.html.twig`文件来呈现表单。该模板如下所示:

```
{% extends 'base.html.twig' %}{% block content %}
<div class="hero-unit" style="background: LightGreen">
    {{ form(form) }}
</div>
{% endblock %}
```

您的大部分工作都是在控制器中完成的。最后一件事是告诉它使用引导主题。把你的`config/packages/twig.yaml`修改成这样:

```
twig:
    form_themes: ['bootstrap_4_layout.html.twig']
```

基础模板已经加载了您的引导样式。现在再次点击列表屏幕上的添加按钮，您将看到您的添加屏幕

![](img/dd083ab7a1c6d1a3dba8717d13b6a660.png)

诚然，这有点难看，但大多数时候，当你投入到一个项目中时，已经有更合适的风格为你建立起来了。你所要做的就是复制并粘贴一个相似的表格。

我们已经取得了很多成就，但还有更多的要做。我们需要一种方法在我们的 GCP 服务器上建立数据库。在我的下一篇文章中，我将向您展示如何在服务器上执行所有必要的命令，而无需在每次需要进行更改时上传文件。一切都将随着我们在开发计算机上构建的映像一起传送；我们所需要做的就是推送到图像存储库，然后它将在云上可用。

谢谢你读到这篇相当冗长的文章的结尾，我希望你学到了很多。这个项目的所有代码都可以在这里找到:

[](https://github.com/rkamradt/phpapp/tree/v0.1) [## rkamradt/phpapp

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/rkamradt/phpapp/tree/v0.1)