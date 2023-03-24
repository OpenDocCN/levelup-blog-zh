# 如何在 Node 中对街道地址进行地理编码？射流研究…

> 原文：<https://levelup.gitconnected.com/how-to-geocode-a-street-address-in-node-js-eba0c8071f4>

当进行市场调查时，有必要知道你的客户在哪里，以及基于这些信息是否有任何趋势。此外，如果您的业务依赖于精确的地理地图，如拼车或产品交付，使用纬度和经度提供准确的位置数据是关键。以下 API 将允许您对任何街道地址进行地理编码，以返回其纬度和经度。要运行这个 API，您必须输入街道地址、城市和州或省。您也可以选择包括邮政编码、国家和国家代码，以帮助缩小您的领域。

![](img/be9f4a44b1d64e6d7afd4704e4e8e504.png)

我们的第一步将是安装 SDK:

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
Apikey.apiKey = 'YOUR API KEY';var apiInstance = new CloudmersiveValidateApiClient.AddressApi();var input = new CloudmersiveValidateApiClient.ValidateAddressRequest(); // ValidateAddressRequest | Input parse requestvar callback = function(error, data, response) {
  if (error) {
    console.error(error);
  } else {
    console.log('API called successfully. Returned data: ' + data);
  }
};
apiInstance.addressGeocode(input, callback);
```

有了它，您可以立即检索精确的位置信息，以改善您的组织运作。你可以从 [Cloudmersive](https://cloudmersive.com/) 获得你的免费 API 密匙。这将使您可以访问我们 API 库中的 800 个月度调用。