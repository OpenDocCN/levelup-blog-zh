# 如何在 CodeIgniter 3 中连接和使用 SQLite

> 原文：<https://levelup.gitconnected.com/how-to-connect-and-use-the-sqlite-database-in-codeigniter-3-48cd50d3e78d>

**深入的 CodeIgniter 3 + SQLite 分步教程**

![](img/194ac3f94b53a6bee9af917f1c0feea3.png)

CodeIgniter 是所有 PHP 框架中最易于使用和最全面的文档之一，也是我个人选择的框架，原因还有很多。

今天，我们将在 CodeIgniter 中创建一个完全可用的 SQLite 数据库连接，并与它进行复杂的交互。

首先，打开记事本或任何文本编辑器，将一个空白文件作为`db.sqlite`保存在`[codeigniter_root_directory]/application/databases/`文件夹中。我们已经准备好了我们的“数据库”。

我们现在需要打开`[codeigniter_toor_directory]/application/config/database.php`文件并修改连接数据数组，该数组默认存储在`$db[‘default’]`变量中:

对于简单的本地连接，您只需修改如上所示的`‘database’`和`‘dbdriver’`条目。

您还需要连接到数据库并管理它，创建新的表和列，修改它们，我通常使用内置的 PHPStorm IDE 工具来实现这些目的，任何其他内置或独立的数据库管理软件也可以。

让我们在`[codeigniter_root_folder]/application/controlers`中创建新的控制器:

在本教程中，我们将处理金融交易，我有一个名为`Full_trans_data.php`(完整交易数据的缩写)的控制器。

这是起始模板，我正在加载`database`(一个负责数据库连接的类，但是你也可以像这样从`[codeigniter_root_folder]/application/config/autoload.php`全局自动加载它们:

`$autoload['libraries'] = array('database','session',’…’);`以此类推……

您可以通过以下方式(从一个控制器)加载多个助手或库:

如果您不知道 CodeIgniter 中的库和助手有何不同，查看它们的一个简单方法是:

*   加载一个助手就像用`include`语句加载另一个`php`文件，然后使用其中的函数和变量，就好像导入文件的内容直接在宿主文件中一样；
*   加载一个库就像加载一个类实例，然后访问它的方法和属性；

有关更多信息，您可以查看关于[助手](https://www.codeigniter.com/userguide3/general/helpers.html)和[库](https://www.codeigniter.com/userguide3/general/libraries.html)的 CodeIgniter 文档。

如果你想知道，为什么我们没有使用`$this->load->library(‘database’)`加载数据库库，那`$this->load->database()`是怎么回事，这是因为 CodeIgniter 支持多个数据库连接，`$this->load->database()`首先更短，可以带参数加载另一个数据库连接:`$another_db_connection = $this->load->database(‘another_db’,TRUE);`

我们用`$this->db`做的事情也可以用`$another_db_connection`来做。

如果您喜欢使用`$this->db`，您可以在需要时使用`$this->db->db_select(‘another_db’)`将连接切换到不同的数据库。

我们还没有在我们的`Full_trans_data`类中创建任何函数，在此之前值得注意的是，CodeIgniter 控制器可以以不同的方式获取数据:

*   **标准发布方法**(适用于大量信息)
*   **使用特定于 CodeIgniter 的 URL 格式`/base_url/controller/function/variable1/variable2/variable3/…`获取方法**，然后服务器根据默认`.htaccess`文件中的指令将其解释为`get`变量
*   **标准 GET 方法**在 URL 中带有标准`?variable1=value1&variable2=value2`，由于 SQL 注入和其他相关的安全漏洞，不推荐这样做，除非我们应用已经包含在 CodeIgniter 中的安全措施，事实上，CodeIgniter 被认为是世界上最安全的 PHP 框架。

因此，为了以高安全级别处理大量数据，我们将使用 **POST 方法**，然后使用内置方法`$this->input->post(“variable”);`访问 CodeIgniter 中的数据

现在让我们在`Full_trans_data`类中创建一个新函数，并将其命名为`add_entry`，这个函数将使用 **POST** 方法获取数据，对其进行处理，然后在数据库表中添加一个条目，我们将使用`$this->db->insert('table_name', $associative_array)`，这是一个内置的方法，允许我们使用 PHP 关联数组，其中包含列名和值:`array( ‘column_name1’=>’value1’, ’column_name2’=>’value2’).` 要了解更多信息，您可以在这里阅读关于`$this->db->insert()`特性[的 CodeIgniter 文档。](https://codeigniter.com/userguide3/database/query_builder.html#inserting-data)

值得注意的是，一旦您在`[codeigniter_root_folder]/application/config/database.php`中配置了任何类型的数据库连接(一个或多个)，所有用于数据库交互的内置方法都将无缝工作，即使您混合使用不同的数据库类型(MySQL、SQLite、MSSQL 等等)。唯一的区别是`$this->db->query(‘SQL QUERY STATEMENT’)`，它必须针对特定的 SQL 方言进行调整，在本例中是 SQLite。

下面是我们使用 **POST** 方法(在 add_entry 函数内部)接收的数据的简化验证和插入过程:

注意:对于 SQLite，您实际上不需要在列类型为`float,` 或`integer`的地方插入`float` 值，在列类型为`integer`的地方，简单的文本就可以了，SQLite 是一种基于文本的数据库格式:值存储为文本，这就是为什么我们直接使用从 POST 获得的任何数据，只要提供了数据，无需额外的验证、转换或转换。

现在让我们创建另一个名为`update_values`的函数来完成这个任务，在这个场景中，我们可能需要适应特定的情况，也许我们需要一次更新整行、一列或多列，我们已经有很多列了，那么我们如何为任何可能的组合创建一个函数呢？

在下面的示例中，我们首先检查是否提供了行 id，以便知道我们正在处理哪一行:

然后我们一列接一列地将它的值添加到`$data_to_update`数组中，前提是提供了相关的值。

我们可以使用一个循环来遍历所有提供的值，有不同的方法可以做到这一点，但首先，我们必须以数组的形式获取 POST 值，为了增加安全性，我们只能使用`$post_values = $this**->**input**->**post(**array**(‘column1’, ‘column2’));`来允许预定义的列名

或者直接过一遍:`$post_values = $this->input->post(NULL);`

更简单、更干净:

现在，为了检索数据，有针对常用请求的内置函数，但健壮的应用程序通常需要高级过滤、搜索、排序甚至更复杂的操作，因为 SQL 语言已经有了这些，我认为最好还是使用 SQL 的能力，直接发送任何请求:`$this->db->query(‘SQL Statement’)`

为了删除特定的行，我们可以使用`$this->db->delete(‘table_name’,$array)`:

感谢阅读！

你想看些魔术吗？点击并按住鼓掌按钮，看看会发生什么:)

**你也想要一些免费的东西吗？**

![](img/55aa66c9693016d909638c41d5252aad.png)

在几秒钟内部署您的下一个应用:**使用此链接从数字海洋**获得 100 美元的云信用:[https://m.do.co/c/8c5a2698b1a2](https://m.do.co/c/8c5a2698b1a2)

![](img/19972b02d7533bdf39c28151d8ca9e56.png)

[**【140 美元来自 FBS**](https://fbs.com/promo/trade-100usd?ppu=193551) **:** 该经纪公司受 IFSC 监管，是历史最悠久的机构之一，自 2009 年开始运营。

**要求:**

*   [注册一个有 140 美元的新账户](https://fbs.com/promo/trade-100usd?ppu=193551)
*   利用 1:500 的杠杆，让你的利润最大化
*   你可以提取所有利润

**可用市场:**加密货币、股票、差价合约、金属、商品、外汇

![](img/fee1ac854ade9c44c41d59bceb59d700.png)

[**【tick mill】30 美元**](https://secure.tickmill.com/redirect/index.php?cii=15604&cis=1&lp=https%3A%2F%2Ftickmill.com%2Fpromotions%2Fwelcome-account%2F) :受 FSA 监管，该经纪商自 2015 年开始运营。

**要求:**

*   注册一个有 30 美元的新账户
*   使用高达 1:500 的杠杆来最大化您的利润
*   在 5 手交易后提取利润
*   最高取款金额是 300 美元

**可用市场:**股票指数，石油，贵金属，债券，外汇。

![](img/4435a5a2e33e3ca1eb8a48b70e4c2387.png)

[**【Roboforex】30 美元**](http://www.roboforex.com/clients/promotions/welcome-program/?a=arag) :受 CySEC 和 IFSC 监管，Roboforex 自 2009 年开始运营，是当今交易者中最受欢迎和信任的经纪商之一。

**要求:**

*   [开立账户](http://www.roboforex.com/clients/promotions/welcome-program/?a=arag)并存入 10 美元以验证您的支付方式(可随时提取)并获得 30 美元作为礼物
*   **利润可无限制提取**
*   如果你交易了必要数量的手，你也可以提取 30 美元

**可用市场:**股票(所有纽交所、纳斯达克和美国证券交易所股票+德国和中国上市公司)、股票差价合约(所有股票的差价合约，*【美国上市股票每笔交易费 1.5 美元)*、指数、ETF、商品、金属、能源商品、加密货币、加密指数、外汇。

![](img/e60c0a5d6b084c99c68244eaa1006780.png)

[**币安的所有交易终身享受 10%的折扣:**](https://www.binance.com/en/register?ref=P5O06MBF) 币安是世界上收费最低的加密货币交易所，支持迄今为止最多样的加密交易或投资方式:

*   现货交易；
*   点对点(P2P)交易；
*   保证金(高达 10 倍杠杆)交易；
*   加密期货交易；
*   加密转换和更多…

当你投资任何加密货币时，你可以通过允许保证金(杠杆)交易的借贷获得额外的无风险被动收入，它带有一个强大的[在线(web)平台](https://www.binance.com/en/register?ref=P5O06MBF)、 [Windows](https://www.binance.com/en/download?ref=P5O06MBF) 、 [Mac](https://www.binance.com/en/download?ref=P5O06MBF) 、 [Linux 软件](https://www.binance.com/en/download?ref=P5O06MBF)，以及面向软件开发者的 [Android](https://www.binance.com/en/download?ref=P5O06MBF) 和 [iOS 应用](https://www.binance.com/en/download?ref=P5O06MBF) + [币安 API](https://www.binance.com/en/download?ref=P5O06MBF) 。

[币安](https://www.binance.com/en/register?ref=P5O06MBF)不仅费用最低，而且是为数不多的支持交易 **Dogecoin** 的平台之一，就交易量和允许交易的加密货币而言，是世界上最大的加密交易所。

**存款选项包括:**

*   将任何上市加密货币直接加密存入您的币安钱包；
*   使用您的信用卡/借记卡购买加密货币；
*   通过银行转账(SWIFT 或 SEN)存入 35 种不同的法定货币。

> ***现在，当您从*** [***此链接***](https://www.binance.com/en/register?ref=P5O06MBF) ***注册时，您可以获得每笔交易额外 10%的折扣。***

祝你有美好的一天！