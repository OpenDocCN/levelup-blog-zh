# Docker 中的 Webpack 捆绑包分析器

> 原文：<https://levelup.gitconnected.com/webpack-bundle-analyzer-in-docker-c4d8a4d4f570>

Webpack-bundle-analyzer 扫描包并构建包内内容的可视化。使用这种可视化来查找大量或不必要的依赖项。它有很棒的插件来优化你的产品包。

有一天，一个错误会让你的包裹增加 20%甚至更多。在我看来，它应该由每个开发人员在完成一项任务之后和发布之前运行，这样我们就不会有大量不必要的依赖。

我们的应用程序运行在 Docker 中，所以我想展示我们如何添加 Webpack Bundle Analyzer，以便为每个开发人员轻松执行。开始时，我们需要安装:

```
npm install —-save-dev webpack-bundle-analyzer
```

在我们的 package.json 中，我们可以添加命令:

```
"profile": "webpack --config webpack.client.js --profile --json > stats.json""analyzer": "webpack-bundle-analyzer ./stats.json ./path/to/your/bundle -h 0.0.0.0"
```

分析需要大量的内存和 cpu，所以我们应该为 docker 添加更多的资源(Docker-> Preferences-> Resources—CPU 最少 4 个内核和 2Gb 内存，但这更多地取决于您的项目)。

接下来，我们可以运行分析:

```
docker-compose run your-service-image-name npm run profile
```

完成分析后:

```
docker-compose run -p 8888:8888 your-service-image-name npm run analyzer
```

在 **localhost:8888** 上的浏览器中，您应该看到:

![](img/32c44fff422110094f27e11d34ee68b8.png)

现在我们可以优化您的包，重复我们的命令，并检查我们的结果。祝你的优化好运！