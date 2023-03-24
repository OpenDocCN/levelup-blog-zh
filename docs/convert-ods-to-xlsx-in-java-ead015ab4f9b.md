# 用 Java 将 ODS 转换成 XLSX

> 原文：<https://levelup.gitconnected.com/convert-ods-to-xlsx-in-java-ead015ab4f9b>

有了像 Office Open Document Spreadsheet 这样的开源资产，组织可以访问熟悉的、用户友好的应用程序，而不需要正常的相关成本。但是，当与使用 Office 套件的其他实体共享文档时，可能有必要转换您的文档，以确保他们可以完全访问信息，而不会丢失数据或格式。以下 API 将允许您使用 Java 将 ODS 文件转换为 XLSX，提供一个经济高效的解决方案来帮助发展您的业务和提高您的共享能力。

![](img/268f7b0a9418b1bce311234773756ac3.png)

运行这个 API 的第一步是安装我们的 SDK。为此，在 pom.xml 中添加对存储库的引用:

```
<repositories>
    <repository>
        <id>jitpack.io</id>
        <url>[https://jitpack.io](https://jitpack.io)</url>
    </repository>
</repositories>
```

然后，添加对依赖项的引用:

```
<dependencies>
<dependency>
    <groupId>com.github.Cloudmersive</groupId>
    <artifactId>Cloudmersive.APIClient.Java</artifactId>
    <version>v3.54</version>
</dependency>
</dependencies>
```

现在，我们可以调用我们的函数:

```
// Import classes:
//import com.cloudmersive.client.invoker.ApiClient;
//import com.cloudmersive.client.invoker.ApiException;
//import com.cloudmersive.client.invoker.Configuration;
//import com.cloudmersive.client.invoker.auth.*;
//import com.cloudmersive.client.ConvertDocumentApi;ApiClient defaultClient = Configuration.getDefaultApiClient();// Configure API key authorization: Apikey
ApiKeyAuth Apikey = (ApiKeyAuth) defaultClient.getAuthentication("Apikey");
Apikey.setApiKey("YOUR API KEY");
// Uncomment the following line to set a prefix for the API key, e.g. "Token" (defaults to null)
//Apikey.setApiKeyPrefix("Token");ConvertDocumentApi apiInstance = new ConvertDocumentApi();
File inputFile = new File("/path/to/inputfile"); // File | Input file to perform the operation on.
try {
    byte[] result = apiInstance.convertDocumentOdsToXlsx(inputFile);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling ConvertDocumentApi#convertDocumentOdsToXlsx");
    e.printStackTrace();
}
```

有了它，您现在可以更轻松地共享您的电子表格文件，而不必担心兼容性问题。您可以从 [Cloudmersive](https://cloudmersive.com/) 中检索您的免费 API 密钥，让您可以访问我们的 API 库中的 800 个月度调用。