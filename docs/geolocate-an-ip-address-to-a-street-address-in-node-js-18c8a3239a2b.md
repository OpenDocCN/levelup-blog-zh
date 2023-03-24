# 将 IP 地址地理定位到节点中的街道地址。射流研究…

> 原文：<https://levelup.gitconnected.com/geolocate-an-ip-address-to-a-street-address-in-node-js-18c8a3239a2b>

我们之前发布了一篇关于使用我们的 API 之一执行 IP 地址的基本地理定位的文章。现在，我们有了 API，它将走得更远。理想的 UX 和安全应用，这一新功能将允许您地理定位一个 IP 地址到它的实际街道地址。通过输入 IP 地址字符串(即 55.55.55.55 ),您可以返回国家和国家代码、街道地址、城市名称、地区名称和邮政编码。这将使您能够更好地了解您的客户群，并跟踪任何对您的网站的威胁。

![](img/d70085c1f4493b309ddf49b20ac3c862.png)

我们将从安装我们的 SDK 开始:

```
npm install cloudmersive-validate-api-client --save
```

您也可以将这个代码片段添加到您的包中。

```
"dependencies": {
    "cloudmersive-validate-api-client": "^1.2.4"
  }
```

然后，我们可以调用我们的函数:

```
var CloudmersiveValidateApiClient = require('cloudmersive-validate-api-client');
var defaultClient = CloudmersiveValidateApiClient.ApiClient.instance;// Configure API key authorization: Apikey
var Apikey = defaultClient.authentications['Apikey'];
Apikey.apiKey = 'YOUR API KEY';var apiInstance = new CloudmersiveValidateApiClient.IPAddressApi();var value = "value_example"; // String | IP address to geolocate, e.g. \"55.55.55.55\".  The input is a string so be sure to enclose it in double-quotes.var callback = function(error, data, response) {
  if (error) {
    console.error(error);
  } else {
    console.log('API called successfully. Returned data: ' + data);
  }
};
apiInstance.iPAddressGeolocateStreetAddress(value, callback);
```

现在，您既可以改善用户体验，又有助于减轻您的组织可能面临的任何网络威胁。你可以从 [Cloudmersive](https://cloudmersive.com/) 获得你的免费 API 密匙。这使您可以访问我们整个 API 库中的 800 个月度调用。