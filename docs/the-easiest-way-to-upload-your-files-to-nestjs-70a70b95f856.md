# 将文件上传到 NestJS 的最简单方法

> 原文：<https://levelup.gitconnected.com/the-easiest-way-to-upload-your-files-to-nestjs-70a70b95f856>

![](img/6e21001bfeea6508ea999b71749b3df5.png)

# 介绍

文件上传在网站或应用中非常常见。我们几乎每天都上传图片、视频和文档。

在互联网上，有成千上万的关于如何用多种语言和技术实现文件上传的教程。今天我将写一个简单的指南来演示如何在 NestJS 中处理文件上传

# 履行

为了处理文件上传，Nest 为 Express 提供了一个基于 [Multer](https://github.com/expressjs/multer) 中间件包的内置模块。Multer 处理以`multipart/form-data`格式发布的数据，这主要用于通过 HTTP `POST`请求上传文件。这个模块是完全可配置的，您可以根据您的应用程序需求调整它的行为。

警告:Multer 无法处理不支持多部分格式的数据(`multipart/form-data`)。

为了类型安全，让我们安装多类型软件包:

```
yarn add @types/multer
```

要上传单个文件，只需将`FileInterceptor()`拦截器绑定到路由处理器，并使用`@UploadedFile()`装饰器从`request`中提取`file`。

```
@Post('upload')
@UseInterceptors(FileInterceptor('file'))
uploadFile(@UploadedFile() file: Express.Multer.File) {
  console.log(file);
}
```

`FileInterceptor()`装饰器有两个参数:

*   `fieldName`:提供保存文件的 HTML 表单中的字段名称的字符串
*   `options`:类型`MulterOptions`的可选对象。

## 确认

为了处理验证，我们可以使用带有内置验证器的`UploadedFile()`装饰器:

```
@UploadedFile(
      new ParseFilePipe({
        validators: [
          new FileTypeValidator({ fileType: '.(png|jpeg|jpg)' }),
          new MaxFileSizeValidator({ maxSize: 1024 * 1024 * 4 }),
        ],
      }),
    )
```

我在这里使用了两个验证器

*   我们可以传递一个字符串或者一个正则表达式。在本例中，我只指定了扩展名为`png` `jpeg`和`jpg`的文件
*   MaxFileSizeValidator:我们可以限制的字节数，例如，我将其设置为 4 兆字节

这些验证器非常幼稚，但是对于我们的应用程序来说足够简单

## 服务

虽然您可以将文件直接保存到数据库中，但我建议您不要这样做。因为前端读取你的文件会很慢很难

在这个例子中，我使用第三方图像托管服务上传我的图像，然后只保存在数据库中的网址

在这种情况下，我们可以非常快速地访问文件，并在我们的应用程序中使用它

```
const user = await this.userModel.findById(userId);
    if (!user) {
      throw 'User not found';
    }
    const formData = new FormData();
    formData.append('image', avatar.buffer.toString('base64'));
    const { data: imageData } = await firstValueFrom(
      this.httpService
        .post(
          `https://api.imgbb.com/1/upload?expiration=600&key=${process.env.IMG_API_KEY}`,
          formData,
        )
        .pipe(
          catchError((error: AxiosError) => {
            throw error;
          }),
        ),
    );
    user.updateOne({ avatar: imageData.data.url }).exec();
```

# 结论

有了上面的例子，你将能够在 10 分钟内理解并编写你自己的文件上传功能。

我希望你能赶上这篇文章，如果没有，你可以在这里查看我的完整的[源代码](https://github.com/leduc1901/nestjs-jwt-auth)

# 遗言

虽然我的内容对每个人都是免费的，但是如果你觉得这篇文章有帮助，[你可以在这里给我买杯咖啡](https://www.buymeacoffee.com/kylele19)