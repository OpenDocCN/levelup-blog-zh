# 在 Salesforce 上扩展您的计算资源

> 原文：<https://levelup.gitconnected.com/scaling-your-compute-resources-on-salesforce-bebf1476768>

Salesforce 开发平台提供了许多强大的功能，为您的团队提供了可以解锁新工作流的应用程序。多年来，该平台一直依靠三种技术运行: [Visualforce](https://developer.salesforce.com/docs/atlas.en-us.pages.meta/pages/pages_intro_what_is_it.htm) (查看数据)、 [Apex](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_intro_what_is_apex.htm) (处理数据)，以及 Salesforce 本身(存储数据)。

Apex 像许多其他面向对象的语言一样，它有一个类似 Java 的语法。然而， **Apex 直接在 Salesforce 服务器**上运行，允许开发人员构建和部署应用程序，而不用担心在哪里托管它或如何保护他们的数据。

# Apex 和长时间运行命令的挑战

基于 Salesforce 构建的应用运行在其[多租户架构](https://architect.salesforce.com/fundamentals/platform-multitenant-architecture)上，因此**服务器资源(如 CPU 和内存)在不同组织之间共享**。然而，在共享资源的情况下，一个组织的应用程序的代码会不会消耗太多的处理器时间和内存空间，从而影响同一组织中的其他用户？例如，需要几分钟才能完成的命令。

Salesforce 通过限制应用在其平台上的运行方式来防止此类情况发生。**如果任何应用程序有可能干扰其他用户，它将被限制完成其命令执行。**长时间运行的程序可能会占用有限的可用资源，从而中断其他工作。

**如果是，Salesforce 开发人员如何构建执行长时间运行命令的应用程序，使其能够随着用户增长而扩展，同时不影响性能？**

# Salesforce 起到了拯救作用

进入 [Salesforce 功能](https://developer.salesforce.com/docs/platform/functions/overview)，这是用 JavaScript、TypeScript 或 Java 编写的小程序**，部署在 Salesforce 的服务器上。函数可以执行资源密集型工作，并直接从 Apex 调用。当 Salesforce 函数完成请求时，它可以将其结果发送回原始 Apex 应用程序，然后该应用程序对这些结果执行任何附加任务。**

Salesforce 功能**在它们自己的容器**中运行，而**不会影响 Salesforce 平台上的其他租户**。**内存和 CPU 限制更高**，允许您执行更长时间运行和更复杂的任务。因为 Salesforce 功能在 Salesforce 平台上运行，所以安全性是内在的；没有隐私丢失的风险，因为一切都在相同的信任边界内运行。

在本文中，我们将详细了解如何将长期运行的 Apex 操作迁移到 Salesforce 功能中，以及一些关于 Salesforce DX 套件的一般提示。

# 先决条件

本文要求您对 Apex 和 TypeScript 有所了解，但如果您不是专家，也不用担心。我们将仔细检查每一段代码，解释发生了什么。

尽管我们代码的完整版本作为 GitHub 存储库提供，但您也可能希望下载 Salesforce 提供的一些 CLI 工具，以便跟进(或用于您自己的未来项目)。

1.  首先，安装[sfdx CLI](https://developer.salesforce.com/tools/sfdxcli)，这是一个帮助 Salesforce 开发人员在构建应用程序时简化常见操作的工具。
2.  重要的是，无论您构建和部署什么，都不要影响您“真正的”生产 Salesforce 组织。因此，创建一个单独的 [scratch org](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_scratch_orgs_create.htm) ，这个应用程序可以与之交互。要获得你自己的 scratch org，注册一个免费开发者版账户。
3.  要使用 Salesforce 功能，您需要通过联系您的 Salesforce 客户代表[注册 Salesforce 功能试用版](https://developer.salesforce.com/docs/platform/functions/guide/index.html)。
4.  最后，您还可以安装[sales force VS 代码扩展](https://developer.salesforce.com/tools/vscode)来使部署您的代码变得稍微容易一点。这一步是可选的。

# 浏览现有代码

在`force-app/main/default/classes/Dogshow.cls`打开文件，查看其内容:

```
public class Dogshow {
   public static void updateAccounts() {
       // Get the 5 oldest accounts
       Account[] accounts = [SELECT Id, Description FROM Account ORDER BY CreatedDate ASC LIMIT 5];

       // Set HTTP request
       HttpRequest req = new HttpRequest();
       req.setEndpoint('https://dog.ceo/api/breeds/image/random');
       req.setMethod('GET');

       // Create a new HTTP object to send the request object
       Http http = new Http();
       HTTPResponse res = http.send(req);

       String body = res.getBody();

       Map<String, String> m = (Map<String, String>) JSON.deserialize(jsonStr, Map<String, String>.class);
       String dogUrl = m.get('message');

       // loop through accounts and update the Description field
       for (Account acct : oldAccounts) {
           acct.Description += ' And their favorite dog can be found at ' + dogUrl;
       }

       // save the change you made
       update accounts;
     }
}
```

这 30 行代码执行一个相当简单的功能:

*   它使用 SOQL 获取五个最早的 Salesforce 帐户。
*   它向[https://dog.ceo/dog-api/](https://dog.ceo/dog-api/)发出一个 HTTP 请求，后者是一个返回 JSON 对象的站点，该对象带有一个指向一只非常可爱的狗的随机 URL 链接。
*   然后，Apex 代码更新这五个帐户的描述，以表明*那只*狗是他们的最爱。

这段代码相当无害，但是它引入了两种情况，这两种情况带来了一些真正的问题:

1.  发出 HTTP 请求时，有几个因素会影响您的程序。例如，您向其发出请求的服务器可能会过载并且响应缓慢。虽然您自己的代码可能是高性能的，但是您会受到控制之外的任何东西的支配，比如外部数据源。
2.  虽然我们这里只迭代 5 个账号，但是如果我们构建一个需要迭代 500 或者 5000 以上的 app 呢？逐一循环将是一个缓慢但不可避免的过程。

这是 Salesforce 职能部门接管部分工作的机会。我们的 Apex 类可以提供一个帐户列表来更新 Salesforce 函数。然后，Salesforce 功能可以发出 HTTP 请求并更新帐户。

在看到完整迁移可能是什么样子之前，让我们首先编写 Salesforce 函数。

# 开发 Salesforce 功能

使用 sfdx CLI，我们可以使用单个命令在 TypeScript 中轻松[创建新的 Salesforce 函数:](https://developer.salesforce.com/docs/platform/functions/guide/create-function.html)

```
$ sf generate function -n dogshowfunction -l typescript
```

这将在`functions/dogshowfunction`下创建一组文件。其中最重要的是`index.ts`，这是我们的主要 Salesforce 功能代码将驻留的地方。但是，其他文件也很重要，它们处理测试和林挺代码，以及定义 TypeScript 生成和 Salesforce 部署流程。

让我们关注一下`index.ts`。在这个文件中，您会注意到有一个导出的函数，它有三个参数:

*   `event`:这描述了来自 Apex 代码的数据有效载荷。
*   `context`:这包含与 Salesforce 通信所需的授权逻辑。
*   `logger`:这是一个简单的记录器，也是与 Salesforce 集成的[。](https://developer.salesforce.com/docs/platform/functions/guide/function-logging.html)

CLI 生成的模板代码显示了 Salesforce 功能的强大:

*   他们可以接受从 Apex 发送的任何 JSON 数据。
*   他们可以直接处理 Salesforce 数据。
*   它们直接与平台集成，为您处理所有的身份验证和安全性。

最棒的是，由于这个特殊的函数运行在 Node.js 上，你可以[安装并使用任何 NPM 包](https://www.npmjs.com/)来补充你的代码。让我们现在通过安装[节点获取](https://github.com/node-fetch/node-fetch)来发出我们的 HTTP 请求:

```
$ npm i node-fetch
```

我们的 Salesforce 功能将负责发出我们的 HTTP 请求并更新我们的五个帐户。为了实现该功能，我们的函数可能如下所示:

```
export default async function execute(event: InvocationEvent<any>, context: Context, logger: Logger): Promise<RecordQueryResult> {
   const accounts = event.data.accounts;

   accounts.forEach(async (account) => {
       const response = await fetch('https://dog.ceo/api/breeds/image/random');
       const data = await response.json();
       const message = ` And their favorite dog is ${data.message}`

       const recordForUpdate = {
           type: 'Account',
           fields: {
             id: account.id,
             Description: `${account.Description} ${message}`
           }
         }
       context.org.dataApi.updateRecord(recordForUpdate);
   });
}
```

让我们来分析一下上面的代码片段。

我们的事件参数本质上是一个 JSON 对象，我们将在 Apex 代码中定义它。虽然这个 JSON 对象还不存在，但是根据我们之前的 Apex 代码的行为，我们可以假设它将具有我们想要更新的帐户的`id`和`Description`。

从这里开始，需要注意的是，上下文参数本质上是用于 Salesforce 函数的 Node.js SDK 的实例化[。因为我们可以假设帐户数据是以数组的形式提供的，所以我们可以简单地遍历每一项，插入记录数据作为更新命令的一部分。](https://github.com/forcedotcom/sf-fx-sdk-nodejs)

# 迁移 Apex 代码

定义了 Salesforce 函数后，我们现在可以调用这个新函数来替换之前的 Apex 逻辑。发出对 Salesforce 函数的调用出奇的简单:我们只需要知道我们函数的名称并提供我们想要发送的数据，如下所示:

```
public class Dogshow {
   public static void updateAccounts() {
       // Get the 5 oldest accounts
       Account[] accounts = [SELECT Id, Description FROM Account ORDER BY CreatedDate ASC LIMIT 5];

       // Set up the function
       functions.Function dogshowFunction = functions.Function.get('ApexToSalesforceFunctionsMigration.dogshowfunction');

       // Create a list to hold the record data
       List<Map<String, String>> jsonObj = new List<Map<String, String>>();
       for (Account account : accounts) {
           Map<String, Object> obj = new Map<String, Object>();

           obj.put('id', account.Id);
           obj.put('Description', account.Description);

           // Add an object to the record list
           jsonObj.add(obj);
       }
       // Send the record list to the function
       functions.FunctionInvocation invocation = dogshowFunction.invoke(JSON.Serialize(jsonObj));

       // if the function had a return value, it would be available here
       invocation.getResponse();
     }
}
```

就这样，我们从 Apex 代码中取出了长时间运行的操作，并将其卸载到 Salesforce 功能中。现在，我们可以确定我们的操作将继续运行，即使它会更多地使用资源。

Salesforce 功能的含义不能被夸大。随着 Salesforce 功能处理我们对耗时、资源密集型操作的需求，基于 Salesforce 平台构建的应用程序现在具有更大的无缝扩展潜力。

# 结论

Salesforce 功能提供了一种在构建有效和持久的项目的同时改进工作流程的方法。没有您在 Apex 中遇到的内存和 CPU 限制，您可以利用 Salesforce 功能来执行长期运行的命令，带来可扩展性而不会对性能产生负面影响。

我们刚刚开始讨论 Salesforce 函数提供的惊人功能。为了更好地了解它们，Salesforce Functions 提供了大量详细说明具体任务的[指南](https://developer.salesforce.com/docs/platform/functions/guide/solutions_and_samples.html)，以及各种值得一看的 [Trailhead 食谱](https://github.com/trailheadapps/functions-recipes/)。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)