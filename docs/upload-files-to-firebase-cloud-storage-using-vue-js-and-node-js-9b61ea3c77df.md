# 使用 Vue.js 和 Node.js 将文件上传到 Firebase 云存储

> 原文：<https://levelup.gitconnected.com/upload-files-to-firebase-cloud-storage-using-vue-js-and-node-js-9b61ea3c77df>

文件上传是现在 web 应用程序的重要组成部分之一。在这里，我将向您展示如何使用 Node.js 和 Vue.js 将文件上传到 Firebase 云存储。

![](img/ed06c31e92ea5b8f1df860fb87c882e0.png)

# 建立 firebase 项目

为此，您需要有一个 firebase 帐户。如果没有，可以在这里创建一个[。在 firebase 控制台中，通过单击“添加新项目”创建一个新的 firebase 项目。为您的项目选择一个名称。然后 Firebase 将设置您的项目。要存储文件，请单击左侧导航窗格中的“存储”按钮。单击“开始”按钮，然后将打开一个模式，其中包含您的云存储的安全规则。你可以在这里](https://firebase.google.com/)了解更多安全规则[。点击下一步，选择您的位置，然后点击“完成”。然后 firebase 会为你创建一个默认的 bucket。](https://firebase.google.com/docs/storage/security/get-started?authuser=1#sample-rules)

现在我们需要连接到我们的 bucket，我们需要一个私钥来安全地连接到我们的 bucket。为此，单击左侧导航窗格顶部的设置图标，然后单击项目设置。在“项目设置”页面中，单击“服务帐户”选项卡，然后单击“生成私钥”按钮。这将生成一个 JSON 文件，其中包含我们的 firebase 帐户的凭证。这个文件应该保密，永远不要存储在公共存储库中。我们已经完成了 firebase 项目的设置，现在我们可以构建 Node.js API 来将文件上传到云存储。

# **构建 API**

为了上传文件，我们将使用一个名为 [multer](https://www.npmjs.com/package/multer) 的包。

安装依赖项

```
npm i express multer firebase-admin
```

现在创建一个 web 服务器并导入依赖项。为此，创建一个名为`index.js`的文件，并添加以下代码。

```
const express = require('express')
const multer = require('multer')
const app = express()app.use(express.urlencoded({extended: false}))
app.use(express.json({extended: false}))app.post('/upload', (req, res) => {
    console.log("File upload API")
}app.listen(5000, () => {
    console.log('🚀Server listening on port 5000')
})
```

我们现在有一个运行在端口 5000 上的基本 web 服务器。

现在我们要构建我们的文件上传 API。我们将使用 multer 上传文件。multer 将拥有包含上传文件的`req.file`对象和包含文本字段的`req.body`对象。我们将使用 multer 提供的`MemoryStorage`。内存存储引擎将文件作为`Buffer`对象存储在内存中。

```
const upload = multer({
    storage: multer.memoryStorage()
})app.use(upload.single())
```

现在我们可以使用`req.file`访问上传的文件。如果你有多个文件，使用`upload.any()`而不是`upload.single()`。如果你使用的是`upload.any()`，你应该使用`req.files`，它将是一个文件数组。

现在你的`index.js`会是这样的

**连接到 firebase**

创建一个名为`firebase.js`的新文件。导入 firebase 依赖项并初始化 firebase admin SDK。

我们已经在应用程序中初始化了 firebase。现在我们可以移动到`/upload`端点。

将文件上传到 firebase

将出口桶从`firebase.js`导入到`index.js`

```
const firebase = require('./firebase')
```

首先，我们需要检查传入的请求实际上是否包含文件，如果请求不包含任何文件，我们将发送状态为 400 的响应。我们可以通过以下方式验证该文件是否存在。`/upload`端点内，

```
if(!req.file) {
        res.status(400).send("Error: No files found")
}
```

Firebase 使用 blobs 来存储二进制数据。blobs 是二进制大对象，它是作为单个实体存储在数据库中的二进制数据的集合。我们可以使用`bucket.file()`方法通过传递文件名来创建一个新的 blob。

```
const blob = firebase.bucket.file(req.file.filename)
```

现在我们需要创建一个可写的流来处理传入的数据。流基本上是数据的集合，但它可能不会一次全部可用，我们需要一次处理一个数据块。

您需要将文件的 mimetype 作为元数据传递，否则您将无法以正确的格式读取数据。

```
cosnt blobWriter = blob.createWriteStream({
    metadata: {
        contentType: req.file.mimetype
    }
})
```

我们需要在创建可写流时检查错误。我们可以通过`blobWriter`上的`error`事件做到这一点

```
blobWriter.on('error', (err) => {
    console.log(err)
})
```

在`finish`事件中，我们可以向客户端发送成功响应。

```
blobWriter.on('finish', () => {
    res.status(200).send("File uploaded.")
})
```

当数据处理完成时，将发出`end`事件。

```
blobWriter.end(req.file.buffer)
```

就是这样。我们已经创建了 Node.js API 来将文件上传到 firebase 云存储。现在你的`index.js`文件会是这样的。

# 构建前端

现在我们需要创建前端来选择要上传的文件。我们将在 Vue.js 中进行

使用类型文件和按钮创建一个`input`字段，以调用 API 调用的函数。

```
<input type="file" ref="file" v-on:change="handleUpload()"/>
<button v-on:click="uploadFile()">Upload</button>
```

现在我们已经用 file 类型创建了一个输入字段。我们添加了一个`ref`属性，这样我们就可以访问输入文件。我们还添加了一个`handleUpload`函数，它将在用户选择文件时被触发。这个函数的作用是，我们将创建一个变量来保存用户选择的文件的值。现在我们将使用`FileList`对象将输入文件保存到变量中。当用户选择一个文件时，`handleUpload`将被触发，输入文件将被保存到变量中。为此，在我们的`script`部分添加以下内容:

```
<script>
  export default {
    data() {
        return {
            file: ''
        }
    },
    methods: {
      handleFileUpload(){
          this.file = this.$refs.file.files[0]
      },
      uploadFile(){

      }
    }
  }
</script>
```

我们将使用`FormData`创建一个保存文件的对象。用`uploadFile`方法创建一个新的表单数据对象

```
const formData = new FormData()
```

现在我们需要将文件添加到`formData`对象中。

```
formData.append("file", this.file)
```

后端 API `upload.single(<field name>)`上的字段名必须与我们的`formData`上的字段名相同。

最后，我们可以将数据发布到后端 API。我在用 Axios 发布数据。

```
axios.post('http://localhost:5000/upload', formData, {
    headers: {
        "Content-Type": "multipart/form-data"
    }
}).then(response => {
    console.log(response.data)
}).catch(error => {
    console.log(error)
})
```

您最终的 vue.js 文件将如下所示。

就是这样。我们最终使用 Node.js、Vue.js 和 Firebase 创建了文件上传应用程序。

你可以在这里获得该项目的完整代码。

[](https://github.com/amljs/node-vue-firebase-fileupload) [## AML js/node-vue-fire base-file upload

### GitHub 是超过 5000 万开发人员的家园，他们一起工作来托管和审查代码、管理项目和构建…

github.com](https://github.com/amljs/node-vue-firebase-fileupload) 

如果你有任何疑问或需要任何进一步的澄清，请在评论区告诉我。我很乐意帮助你。

如果你觉得这篇文章有帮助或者有趣，可以考虑鼓掌。

编码快乐！！！