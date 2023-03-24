# Firebase:将 JSON 导入云 Firestore

> 原文：<https://levelup.gitconnected.com/firebase-import-json-to-firestore-ed6a4adc2b57>

## 作者:杰夫·刘易斯

![](img/bee3b4a2a93172d3a504db6008634e38.png)

有 JSON 数据吗？是否要上传到 Firebase Cloud Firestore？那么这本指南是给你的。

**注意:**如果需要，将提供一个样本 JSON 文件用于测试。

# 1.JSON 文件

**注意:** JSON 文件在加载文件时必须有一个键，比如“users”。这是因为 **Firebase Cloud Firestore 的根目录中必须有一个** [**集合**](https://firebase.google.com/docs/firestore/data-model#collections) **。**

*   [Github 要点](https://gist.github.com/jefelewis/912f7e7891ef86d5e686bb1109838bae)
*   [下载 Zip](https://gist.github.com/jefelewis/912f7e7891ef86d5e686bb1109838bae/archive/c549bda356b7f06b6f03ed70b003114e51da812d.zip)

**users.json**

```
{
  "users": [
    {
      "id": "1",
      "firstName": "Kristin",
      "lastName": "Smith",
      "occupation": "Teacher",
      "reviewCount": "6",
      "reviewScore": "5",
    },
    {
      "id": "2",
      "firstName": "Olivia",
      "lastName": "Parker",
      "occupation": "Teacher",
      "reviewCount": "11",
      "reviewScore": "5"
    },
    {
      "id": "3",
      "firstName": "Jimmy",
      "lastName": "Robinson",
      "occupation": "Teacher",
      "reviewCount": "9",
      "reviewScore": "4"
    },
    {
      "id": "4",
      "firstName": "Zack",
      "lastName": "Carter",
      "occupation": "Teacher",
      "reviewCount": "4",
      "reviewScore": "5"
    },
    {
      "id": "5",
      "firstName": "Brad",
      "lastName": "Rayburn",
      "occupation": "Teacher",
      "reviewCount": "2",
      "reviewScore": "4"
    }
  ]
}
```

# 2.Firebase 设置

## Firebase:

1.  转到 [**Firebase 控制台**](https://console.firebase.google.com/u/2/)
2.  点击**“添加项目”**
3.  输入**“项目名称”**和**“接受条款”**

![](img/7f0a59b996beb1750d6fc0ead925cfe6.png)

## 项目:

1.  既然已经添加了 Firebase 项目，我们将希望在代码编辑器中将它链接到该项目。在 **Firebase 控制台登陆页面**点击**</>选项**。
2.  在您的项目**中创建文件 **config.js** (将该文件添加到 GITIGNORE 中)。**
3.  将必要的数据复制到粗体字段中(见下文)。我们在导入时需要这个文件。

**config.js**

```
*// Firebase Config* const firebaseConfig = {
  apiKey: **"API_KEY_HERE"**,
  authDomain: **"AUTH_DOMAIN_HERE"**,
  databaseURL: **"DATABASE_URL_HERE"**,
  projectId: "**PROJECT_ID_HERE**",
  storageBucket: "**STORAGE_BUCKET_HERE**",
  messagingSenderId: "**MESSAGING_ID_HERE**"
}*// Exports* module.exports = firebaseConfig;
```

# 3.云 Firestore 设置

1.  导航到左侧边栏，点击“**数据库”**
2.  点击**“创建数据库”**
3.  选择**“测试模式”**，因为我们需要读/写你的数据库。
4.  点击**“启用”**

# 4.Firebase 服务帐户

1.  导航至左侧边栏，点击“**设置”**
2.  选择**“用户和权限”**
3.  导航至**“服务账户”**选项卡。
4.  选择 **Node.js** ，应该是默认选项。
5.  点击**“生成新的私钥”**
6.  点击弹出的**“生成密钥”**。JSON 文件应该已经下载到您的计算机上了。
7.  将该文件重命名为**service account . JSON。**我们在导入时将需要该文件。

**serviceAccount.json**

```
{
  "type": "service_account",
  "project_id": "**PROJECT_ID_HERE**",
  "private_key_id": "**PRIVATE_ID_KEY_HERE**",
  "private_key": "**PRIVATE_KEY_HERE**",
  "client_email": **"CLIENT_EMAIL_HERE"**,
  "client_id": **"CLIENT_ID_HERE"**,
  "auth_uri": **"AUTH_URI_HERE"**,
  "token_uri": **"TOKEN_URI_HERE"**,
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "**CLIENT_CERT_URL_HERE**"
}
```

# 5.NPM 属地

我们将使用一个库来导入 JSON 数据，因此我们将把项目初始化为一个 npm 存储库。

## **初始化 NPM**

```
npm init
```

# 安装依赖项

```
npm install firestore-export-import
```

# 6.示例代码

本例将使用 3 个文件:

1.  配置文件
2.  serviceAccount.json
3.  import.js

**config.js**

```
*// Firebase Config* const firebaseConfig = {
  apiKey: **"API_KEY_HERE",**
  authDomain: **"AUTH_DOMAIN_HERE",**
  databaseURL: **"DATABASE_URL_HERE",**
  projectId: "**PROJECT_ID_HERE**",
  storageBucket: "**STORAGE_BUCKET_HERE**",
  messagingSenderId: "**MESSAGING_ID_HERE**"
}*// Exports* module.exports = firebaseConfig;
```

**serviceAccount.json**

```
{
  "type": "service_account",
  "project_id": "**PROJECT_ID_HERE**",
  "private_key_id": "**PRIVATE_ID_KEY_HERE**",
  "private_key": "**PRIVATE_KEY_HERE**",
  "client_email": **"CLIENT_EMAIL_HERE"**,
  "client_id": **"CLIENT_ID_HERE"**,
  "auth_uri": **"AUTH_URI_HERE"**,
  "token_uri": **"TOKEN_URI_HERE"**,
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "**CLIENT_CERT_URL_HERE**"
}
```

**import.js**

```
// Imports
const firestoreService = require('firestore-export-import');
**const firebaseConfig = require('./config.js');
const serviceAccount = require('./serviceAccount.json');** 
// JSON To Firestore
const jsonToFirestore = async () => {
  try {
    console.log('Initialzing Firebase');
    await firestoreService.initializeApp(**serviceAccount**, **firebaseConfig.databaseURL**);
    console.log('Firebase Initialized');

    await firestoreService.restore('./data-clean/firebase/**users.json**');
    console.log('Upload Success');
  }
  catch (error) {
    console.log(error);
  }
};

jsonToFirestore();
```

# **7。导入**

运行以下命令，执行将 JSON 数据导入 Firebase Cloud Firestore 的功能:

```
node import.js
```

这将需要一些时间，取决于您的文件大小，但您的数据现在正在上传到 Firebase Firestore，并在云中有了一个家。