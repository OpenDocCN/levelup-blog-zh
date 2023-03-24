# 用 Aircrack-ng 套件破解 WIFI 密码。

> 原文：<https://levelup.gitconnected.com/cracking-wifi-passwords-with-aircrack-ng-suite-4e7f105355db>

![](img/ce207befd8f6a06d489b214a823aef9f.png)

米克·豪普特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这篇文章完全专注于破解密码的实用方法、加密类型以及如何暴力破解 WiFi 网络。

所有的练习都应该在安全的环境中进行，这完全是为了教育的目的。根据硬件配置和其他外部条件，你可能会激怒某人，但这不是问题的关键，所以，要冷静，并意识到这可能是有害和非法的。

在这个实践中，我在一台普通的 **Virtualbox** 机器上使用了一个 **Kali Linux 2014.4 64 位操作系统**和一个 **WiFi TP-LINK TL-WN321G 加密狗**，它在虚拟化操作系统上模拟了一个物理无线卡。

太好了！但是我们要破解一个 WiFi 网络的密码，但是什么是**网络**？

**网络**的主要概念概括为**连接在一起的设备，以便在连接的设备之间传输数据**或共享资源。

在简单的网络拓扑中，至少有一个元素充当服务器，这个元素主要负责处理、包含和共享网络中的数据(在大多数情况下)。你已经能算出我说的是哪个元素了，没那么难。服务器是路由器，数据是互联网

简单来说:**唯一连接到互联网的设备是路由器**，网络的其他客户端连接到它以获取共享数据**(互联网接入)**。

客户端向路由器发送请求，请求进入互联网，然后它通过路由器向客户端转发数据包(响应)。

其中:

*   请求和响应是网络数据包
*   我们可以看到通过嗅探器传递的信息(捕捉和读取)信息

## 理论够多了，让我们开始破解吧

在使用 Aircrack 套件之前，我们要先看看 **MAC 地址**的作用以及如何恶搞。让我们稍微保护一下我们的身份。

每个网卡都有一个由网卡制造商分配的物理静态地址，它被称为 MAC(媒体访问控制)地址。该地址用于设备之间相互识别，并将数据包传输到正确的位置。每个数据包都包含用于标识的源 MAC 和目的 MAC。

这也适用于在单个设备上采取行动，如限制等(白名单、黑名单等)

希望 Kali 有一个名为 **macchanger** 的 CLI 工具，可以改变存储在操作系统 RAM 存储器中的 MAC 地址值。

因此，我们可以使用命令`iwconfig`检查连接的物理网络接口。该命令将显示连接到机器的网络接口，我们将使用`wlan0`接口

现在，最后，让我们通过关闭接口并运行`macchanger`工具来更改`wlan0`接口的 **MAC 地址**。

```
ifconfig wlan0 down macchanger --random wlan0 ifconfig wlan0 up
```

`ifconfig`被改成了**新的 Kali Linux 版本**，现在我们必须使用以下:

```
ip set link wlan0 down macchanger --random wlan0 ip set link wlan0 up
```

结果是这样的:

```
Current MAC: ca:79:9c:99:58:1a (unknown)
Permanent MAC: f8:d1:11:14:dd:cc (TP-LINK TECHNOLOGIES CO., LTD.)
New MAC: 1e:92​:CD:​37:69:12 (unknown)
```

我之前说过:MAC 地址的存在是为了**确保**每个数据包都被发送到正确的位置，那么，如何才能将 MAC 地址发送出去呢？

嗯，这是因为无线网卡(接口)有两种模式，**管理模式，**和**监控模式**。

## 管理模式

这是默认行为，它确保如果目标 MAC 是我们的 MAC 地址，将捕获数据包。简单地说，这将得到每个发给我的包(me =我的电脑)。

## 监控模式

此模式将捕获 WiFi 范围内的任何数据包，无论是否定向。

## 启用监视器

当接口关闭时，几乎每个配置都必须被处理，因此，我们必须关闭`wlan0`接口才能完成。

首先，通过做设置关闭界面；`ip set link wlan0 down`

最后，我们将使用`Aircrack-ng`套件中的第一个程序`airodump-ng`

# 用空气嗅嗅

这个程序是一个数据包嗅探器，允许捕获我们的无线网卡范围内的所有数据包。基本上就是扫描我们周围所有的 **WiFi 网络**收集它们的信息。但是首先，我们需要运行以下命令:

`airmon-ng start wlan0`

Airmon-ng 将创建一个名为`wlan0mon`的新虚拟无线网卡，并启用监控模式。该接口将在所有后续练习中使用。

您还可以使用 stop 命令关闭监视器界面，如下所示:

`airmon-ng stop wlan0mon`

我们刚刚完成设置，我们已经准备好 WiFi 卡，可以开始嗅探

因此，通过使用:

`airodump-ng wlan0mon`

该程序将开始嗅探，并显示网络列表和连接的设备，其中包含以下信息:

```
BSSID -> MAC address for the access point (Network device) 
PWR -> Power (the distance of the access point vs the Wifi Card) The closer the network - the fastest is the attack 
BEACON -> heartbeat kind of packet - 
#DATA -> useful packets sniffed 
#/s -> number of packets collected en 10 segs 
CH -> broadcast channel (no interference) 
MB -> Speed 
ENC -> Encryption 
CIPHER -> Decrypt packages (CCMP, TKIP) 
AUTH -> Auth type
```

这将显示所有网络和连接到网络的所有外围设备，并根据其 MAC 地址进行过滤。但是我们可以通过将嗅探器定位到一个或多个特定的网络来以某种方式过滤它

## 目标嗅探

**Airodump-ng** 将是所有嗅探过程的核心命令。通过向`airodump-ng`命令添加标志来实现目标包

查看以下示例:

`airodump-ng --channel 11 --bssid B4:75:0E:A6:16:01 --write davidfile.txt wlan0mon`

这将把网络分段成用 MAC 地址指定的。还将使用**监控模式**界面，用 **—写**命令创建并写入一个文件。对于这次扫描，我使用输出**。txt** 文件扩展名，但是如果你不指定它，程序会显示一些不太熟悉的扩展名的文件，比如**。管帽**或**。netxml**

我们可以使用 **Wireshark** 来分析这些数据，如果目标网络使用加密，收集的数据将没有多大用处

我之前说过 **airodump-ng** 显示所有的外设，对吗？它显示了网络上的**站**(计算机)的状态，例如:

```
STATION MAC: 
MAC address PWR: Distance between Interface and the client's computer LOST: packets lost FRAMES: useful packets
```

有了确定的目标网络，就更容易根据加密文件及其距离向网络发送一些攻击。

# 取消身份验证攻击

这种攻击用于断开我们范围内的任何设备与网络的连接，即使网络受密钥保护，黑客也会伪装成目标机器(欺骗 MAC 地址)向路由器发送去验证数据包。

同时，黑客向目标机器(假装是路由器)发送数据包，告诉它需要重新认证自己。

这次我们将使用 **aircrack-ng** 套件的**air play**程序

`aireplay-ng --deauth 200 --channel 11 -a B4:75:0E:A6:16:01 wlan0mon`

这将适用于连接到网络的所有客户端，但是我们也可以使用 **-c** 标志指定一个目标 MAC 客户端

很简单。

## 到这一步

我们一直在使用虚拟网卡，迄今为止，我们可以在不连接到目标网络的情况下进行所有这些攻击，但如果我们可以连接到目标网络，我们可以获得更准确的信息。

为此，如果它是一个开放的网络，那么我们可以不需要密码就可以连接到它，另一方面，如果目标网络使用密钥，我们将需要解密数据包

常见的加密类型有 **WEP** 、 **WPA、**当然还有 **WPA2** ，破解方法可能会根据加密类型以及硬件组合和到目标站或路由器的距离而有所不同。每次加密的复杂度从 **WEP** 增加到 **WPA2**

让我们开始回顾对 WEP 加密类型的攻击。

# 破解 WEP 加密

在一个基本的情况下，我们必须通过监控模式接口使用 **airodump-ng** 来查看 **WEP** 网络的嗅探过程。

在本次讲座中，我们将路由器称为 AP(接入点)

**【嗅探进程运行】**

会成功的。

一旦我们得到了**密钥**，嗅探过程应该停止，我们将拥有一个**加密的 WEP 密钥**，用于在目标网络中进行身份验证。

## 假身份验证(数据包注入)

这种特殊的方法将是一些场景中的一个常见的集合点，在这些场景中，您不需要计算数据包的数量来执行攻击。

我们有两个场景，第一个让我们想象一个 **AP** (接入点)，客户端连接到常规使用的互联网(基本上没有视频)。第二个是如果网络上没有电台。第一种情况不需要额外的操作，但第二种情况需要将数据包注入流量中，以迫使路由器使用新的 **IV 的**创建新的数据包

`aireplay-ng --fakeauth 100 -a 00:18:F8:BD:5C:F3 -h F8:D1:11:14:DD:CC wlan0mon`

其中:

1.  **—带有 X 个数据包请求的 fakeauth** (命令)
2.  **-a** :目标 BSSID
3.  **-h** :本地欺骗 MAC 地址

如果授权，则 **AUTH** (在表上)列下的值将变为 **OPN** 。输出如下所示:

```
17:42:39 Waiting for beacon frame (BSSID: 00:18:F8:BD:5C:F3) on channel 617:42:39Sending Authentication Request (Open System)17:42:41 Sending Authentication Request (Open System)17:42:43 Sending Authentication Request (Open System)17:42:45 Sending Authentication Request (Open System)[ACK]17:42:47 Sending Authentication Request (Open System)[ACK]17:44:40 Authentication successful17:44:40 Sending Association Request [ACK]17:44:40 Association successful :-) (AID: 1)
```

这种方法将等待 ARP 数据包，捕获它并将其注入流量

这包括强制接入点生成一个带有新的 **IV** 的新的 **ARP 数据包**，我们捕获这个新的数据包并再次将其注入流量中，这一过程必须一直进行，直到数据包的数量足以破解密钥

在此之前，您应该使用 Aircrack 编写一个. cap 文件，并成功地将 **AUTH** 关联到目标网络

```
aireplay-ng --arpreplay 100 -b 00:18:F8:BD:5C:F3 -h F8:D1:11:14:DD:CC wlan0mon
```

其中:

1.  **—带有 X 个数据包请求的 fakeauth** (命令)
2.  **-b** =目标 BSSID
3.  **-h** =本地欺骗 MAC 地址

## Korek Chop Chop(小包注射)

这是增加网络数据包的另一种方式，用于解密流量非常低的网络的密钥。

基本上，在这种方法中，我们将捕获一个 **ARP 数据包**，并尝试猜测其密钥流，并使用 **packetforge-ng** 使用它来伪造一个新的数据包，然后我们可以将这个**新伪造的**数据包注入到流量中，以生成新的 **IV 的**。至少对我来说，第一次尝试有点困难。

使用此命令:

`aireplay-ng --chopchop -b [target MAC] -h [your MAC] [INTERFACE]`

如果我们拆分此代码，我们将有以下操作:

*   抓一堆包(流)求解密-> Y/N
*   将选择的数据包保存在. cap 文件中
*   开始猜测并确定目标 AP 的密钥流(Xor、pt、frames 等),并得出百分比。

但是，如何**伪造**一个**包**？嗯:

`packetforge-ng -0 -a [target MAC] -h [your MAC] -k 255.255.255.255 -l 255.255.255.255 -y [file.xor] -w [output]`

```
-0 -> for arp packets 
-k destination 
-l source 
-y specify the name of the keystream
```

然后，我们再次注入新的 **IV 的**和之前的认证

`aireplay-ng -2 -r [resulting.xor] [interface]`

## 碎片攻击

这种攻击与**的 Korek 斩法**非常相似

您将通过这条命令获得 **PRGA** :

`aireplay-ng --fragment -b [target MAC] -h [your MAC] [interface]`

这是什么？这试图产生一个有用的包，重复多次。一旦它开始有用，我们将使用密钥流通过。xor 文件

伪造数据包与 **chopchop** 相同，但带有— fragment 标志，以便以后将伪造数据包注入流量，如下所示:

`aireplay-ng -2 -r [out from last step][interface]`

最后两种方法都不太简单，你可以通过使用多路复用器终端来练习，以便容易地再现每个步骤。此外，您可能必须说明每个硬件都是不同的，有时不可能实现一种或多种技术。

现在，让我们回顾一下 **WPA** 和 **WPA2** 的破解方法。

# 湿法磷酸裂化

**WPA** 旨在解决 **WEP** 中的问题，并提供更好的加密。

WEP 中的主要问题是作为纯文本发送的简短的 **IV(初始化向量)**，因此它们可以重复，因此通过收集大量的**IV**和 Aircrack-ng，我们可以确定密钥流和 WEP 密钥。

在 **WPA** 中，每个数据包都用唯一的临时密钥加密，我们收集的数据包数量无关紧要。它们不包含破解 **WPA** 密钥的信息。

## WPA/ WPA2 裂化(WPS 功能)

大多数数据包包含对破解(确定密钥)无用的信息

## WPS 功能

**WPS** 是一项功能，允许用户使用 **WPS** 按钮或仅通过点击 **WPS** 功能轻松连接到 **WPS** 启用网络，其中**客户端**将连接到网络，而无需手动输入 **WPA** 键

认证是通过使用一个 8 位数的长 ping(不是 **WPA** 密钥)来完成的，这意味着 pin 组合的数量相对较少，并且使用**暴力**我们可以在不到 10 个小时内猜出 pin。

为此，我们将使用一个叫做**回收器**的工具，这个工具可以从销上回收 **WPA/WPA2** 键。

## 使用 Wash 获取 WPA 网络

使用监视器界面，让我们抓取 **WPA** 网络。通常， **wash** CLI 工具没有安装在 Kali 上，但是你可以在这个 [Kali Linux 工具站点](https://en.kali.tools/?p=341)上找到它

`wash -i wlan0mon`

结果:

```
BSSID - Mac Address 
Channel - Wifi channel 
WPS version - 1.0 
RSSI - distance 
WPS Locked - No => Sometimes can be locked - if yes - you can't do this attack 
ESSID - [network name]
```

然后，我们可以这样使用**金甲虫**:

`reaver -b [target MAC] -c [CHANNEL] -i [Interface monitor]`

这个挺简单的，但是到我实践的时候，有一些理论上的疑惑我根本就没搞清楚。

让我们再回顾一下:

> 捕获 WPA 包并没有多大用处，因为它们不包含任何可以用来破解密钥的信息

但是包中包含信息，这叫做**握手包**

好吧，但它是如何工作的？

每次您连接到任何一个 **AP** 时，客户端和 AP 之间会发生一次 4 次握手(4 个数据包连接到目标网络)。我们需要捕捉握手，然后用 **aircrack** 我们将不得不对握手发起单词列表攻击来确定密钥。

该死的…

同样，为了破解 **WPA/WPA2** 我们需要捕捉握手，并在强力之后通过**单词列表**或**文本字典**来获取

# 捕捉握手(一步一步)

首先，让我们在特定的 **WAP** 目标上运行`airodump-ng`

`airodump-ng --channel --bssid --write interface`

然后，我们需要强制握手，这可以通过强制 un-auth 或简单的网络重新连接来实现。

`aireplay-ng --deauth [4] -a [AP] -c [target][interface]`

**专业提示**:当你这么做的时候，注意右上角的信息，这个包应该说:WPA 握手或者类似这样的东西**【WPA 握手:00:10:18:90:2D:EE】**

# 创建单词列表

太好了，你已经检测到 AP 握手，现在我们将使用 Kali 的 **crunch** 工具生成一个单词列表，或者你可以在互联网上下载一个专业的。

这一步需要制作一个强力列表样式，这样 **Aircrack** 将用来破解 **WPA** 密钥

`./crunch [min][max][characters=lower|upper|numbers|smbols] -t [pattern] -o file (output file)`

这是条件反射的一个例子

`./crunch 6 8 12356!"&$% -o wordlist -t a@@@@b -> chars between a and b`

现在，让我们用这个该死的单词表

用**气锤**撬开钥匙。使用 **pbkdf2** **算法**将单词列表中的每个密码与名称 **ESSID** 组合起来计算出**成对主密钥(PMK)** 。

PMK 被比作握手文件。

`aircrack-ng [handshake file] -w [wordlist][interface]`

然后等着看大部分时间会发生什么(结果可能会有所不同)。

我敢肯定，对于这种易受攻击的加密技术，还有更常见的攻击方式，以及更复杂的实现方式。让我们不断更新这个要点。

快乐编码。