# 用电子在 Node.js 中打印 PDF(电子锻造+ pdf-to-printer + webpack)

> 原文：<https://levelup.gitconnected.com/printing-pdf-in-node-js-with-electron-electronforge-pdf-to-printer-webpack-b5c18209cf88>

![](img/f129b2a9c6c0e3cab8125895b0f67880.png)

杰拉尔丁·莱瓦在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

所以你有一个电子应用程序，并要求打印 pdf 的功能？跟我来

在这一点上，您可能已经做了一些研究，并发现静默模式下 pdf 打印的节点库数量并不多。

跨越这个障碍，electron 有一些依赖于库和打印方式(通过文件或原始内容)的警告。因此，我们将看到一个例子，它包含了所有可能的注意事项，因此它在某种程度上对您有用。

## 这个例子

有了一个 [ElectronForge webpack 模板](https://www.electronforge.io/templates/webpack-template)，我们将在我们的项目中安装 [pdf-to-printer](https://www.npmjs.com/package/pdf-to-printer) ，这是一个非常简单的库，它使用 SumatraPDF.exe 来打印 pdf 文件。

现在，这不是一个必需的步骤，但对于这个例子，我们将接收一个 pdf 的 base64 字符串，并将其保存到一个临时文件中，以进行打印，然后将其排除。如果您已经有了 pdf 文件，只需将它放在项目文件夹中，您将提前知道如何在生产中找到该文件。

在 pdf 文件生成之后，您将需要类似这样的东西:

```
import fs from 'fs/promises';await *fs*.writeFile(tmpPdfPath, Buffer.from(*base64String*, 'base64'), 'binary');await printPdf(tmpPdfPath, {
  printer: *nativePrinter*.name,
}).finally(() => *fs*.unlink(tmpPdfPath));
```

如果您运行您的启动脚本，您应该会得到一个错误，类似于“SumatraPDF.exe could ”,这是因为 pdf-to-printer 所依赖的 exe 没有插入到最终的构建文件夹(。webpack)，所以我们这样做:

```
// in main.webpack.jsconst CopyPlugin = require('copy-webpack-plugin');*module*.*exports* = {
  ...
  plugins: [
    **new** CopyPlugin({
      patterns: [
        {
          from: 'node_modules/pdf-to-printer/dist/SumatraPDF.exe',
          to: './',
        },
      ]
    )}
  ],
  ...
}
```

## 在生产中工作

现在它可以工作了。不过，仅限于开发人员。当您运行 make 脚本并执行您的应用程序时，会出现相同的错误，如果您将 asar 配置为 true，它会给您带来更多的错误。首先让我们修复 SumatraPDF.exe 未找到错误，我们的 webpack 配置不工作的原因是我们正在处理一个. exe 文件，它不能被编译，所以我们必须把它作为一个[额外资源](https://electron.github.io/electron-packager/main/interfaces/electronpackager.options.html#extraresource)

```
// in forge.config.js*module*.*exports* = {
  packagerConfig: {
    ...
    extraResource: ['node_modules/pdf-to-printer/dist/SumatraPDF.exe'],
    ...
  },
  ...
}
```

并在生产中更改我们的代码以指向新的路径(在开发模式下不会加载额外的资源)

```
const basePath = process.env.NODE_ENV === 'development' ? __dirname : process.resourcesPath;const tmpPath = './tmp';// eslint-disable-next-line @typescript-eslint/no-empty-function
*fs*.mkdir(tmpPath).catch(() => {});const tmpPdfPath = path.join(tmpPath, `${uuid()}.pdf`);await *fs*.writeFile(tmpPdfPath, Buffer.from(*base64String*, 'base64'), 'binary');await printPdf(tmpPdfPath, {
  printer: *nativePrinter*.name,
  sumatraPdfPath: path.join(basePath, 'SumatraPDF.exe'), // <- here is the change
}).finally(() => *fs*.unlink(tmpPdfPath));
```

## 在生产中工作(使用 asar)

只有当您使用我们的从 base64 生成 pdf 文件的方法时，才需要该步骤。以 asar 模式编译的电子应用程序非常常见，这阻碍了我们直接在项目文件夹中创建文件，因此再次增加了额外的资源。在您的项目根目录中创建一个 tmp 文件夹(记得在其中放入一个. gitkeep，这样在您要推送的更改中将会考虑到它),然后将它添加到您的 packagerConfig 中:

```
// in forge.config.js*module*.*exports* = {
  packagerConfig: {
    ...
    extraResource: ['node_modules/pdf-to-printer/dist/SumatraPDF.exe', 'tmp'],
    ...
  },
  ...
}
```

然后我们更新我们的逻辑来生成 de 临时文件:

```
const basePath = process.env.NODE_ENV === 'development' ? __dirname : process.resourcesPath;const tmpPath = path.join(basePath, 'tmp'); // <- here is the change // eslint-disable-next-line @typescript-eslint/no-empty-function
*fs*.mkdir(tmpPath).catch(() => {});const tmpPdfPath = path.join(tmpPath, `${uuid()}.pdf`);await *fs*.writeFile(tmpPdfPath, Buffer.from(*base64String*, 'base64'), 'binary');await printPdf(tmpPdfPath, {
  printer: *nativePrinter*.name,
  sumatraPdfPath: path.join(basePath, 'SumatraPDF.exe'),
}).finally(() => *fs*.unlink(tmpPdfPath));
```

## 关于 pdf 转打印机的警告

在我写这篇文章的时候，pdf-to-printer 在打印到名称中有空格或横杠的打印机时有一个小问题，请注意，在调用 cli 进行打印时，可能需要在打印机名称周围加一个""

感谢你给了我生命中的一些时间，如果我以任何方式提供了一些帮助，请告诉我。