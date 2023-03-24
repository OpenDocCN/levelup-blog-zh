# 我如何用砖砌我的路由器试图锁门

> 原文：<https://levelup.gitconnected.com/how-i-bricked-my-router-to-manually-lock-an-old-door-935f863fb613>

我以为我有一个聪明的办法来解决一个古老的问题，结果却是一个愚蠢的工程探索。

![](img/6bc914e9758af63d15292ebb29d63588.png)

伊莎贝尔·加格在 [Unsplash](https://unsplash.com/s/photos/door?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

自从我出生以来，我一直住在伊斯坦布尔一个不太好的街区，每隔一段时间就会发生盗窃事件。此外，我们住在一栋家属楼里，这栋楼的三个房间都被我们、我的叔叔和他们的家人住着。事情是这样的，我们的大楼有一扇生锈的旧外门，门上的锁坏了；因此，我们不能锁门，我们只是锁了我们公寓的门，但同时我们把鞋子放在我们公寓的前面，这在家庭建筑中很常见，这个陈旧生锈的门在这段时间里导致了好几双鞋子被偷。在上次事故后，我们决定在门上安装一个手动滑锁，只能从里面锁上，当这个锁被锁上时，即使有钥匙，门也不能从外面打开。这种锁的问题是，在我们用这种新的手动锁锁门之前，我们必须确保每个公寓至少有一个人在家，以防其他人回家，这样他们就可以下楼，打开锁，让其他人进来，然后再锁上，这意味着我们会让门不锁，并希望无论谁在上午 12 点以后来都会锁门，因为很难通过查看鞋子来检查所有公寓以确定是否有人在家。

![](img/f6fb678f0e911dfba572008812da7ce2.png)

照片由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/lock?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在另一个单独的话题上，我不知道它是如何发生的，但在某个时候，我发现自己通过我的小型哑调制解调器支持 10+人的互联网连接，该调制解调器在负载下经常损坏。我想知道谁在什么时候连接，我也想能够不时地阻止我的一些年轻的堂兄弟，他们通过观看全高清《我的世界》视频来劫持我们所有人的带宽。我当时有一个非常简单的华硕调制解调器，看起来像一个宇宙飞船模型。这个调制解调器有一个非常简单的接口，能够做的事情很少，但它完成了工作，并允许我看到所有连接的客户端，以及阻止它们。

![](img/b7b80c47b07b2b5a50658d2da6e58a47.png)

路由器看起来和这个一模一样，但是我不记得确切的型号名称了。

当时，就像我的大多数想法一样，我有一个永远不会变成现实的想法:如果我能获得我的调制解调器的所有连接客户端的列表，将他们的 MAC 地址与他们的真实身份相关联，并使用贴在墙上的旧电话在楼下的门旁边显示这个列表，我可能会允许每个回家的人检查这个屏幕，看看谁在家，他们将能够决定是否锁门。虽然这有点侵犯他们的隐私，但我更感兴趣的是它带来的技术挑战，这就是为什么我没有给他妈的，并开始建立它。

第一个挑战是获得客户端列表，我想我可以扫描本地网络中的端口，看看谁使用 nmap 连接到网络，然后我会以某种方式获得关于客户端的附加信息。

# 使用 nmap

Python 通过 [python-nmap](https://xael.org/pages/python-nmap-en.html) 包绑定了 nmap，这非常容易使用:

```
nm.scan(hosts='192.168.2.0/24', arguments='-sn')
hosts_list = [(x, nm[x]['status']['state']) for x in nm.all_hosts()]
for host, status in hosts_list:
    print('{0}: {1}'.format(host, status))
```

通过使用这个，我希望得到一个 IP 列表，但是猜猜发生了什么？每次我运行脚本时，调制解调器都会死机，我不得不重启它，让它重新活过来。我尝试了许多不同的选项，但我以前没有正确使用 nmap，我也不知道调制解调器有什么问题，这就是为什么我必须找到另一种解决方案。

![](img/b24426f5c343368862a0b410f84dd2e7.png)

Julia Joppien 在 [Unsplash](https://unsplash.com/s/photos/broken?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 使用调制解调器的管理界面

第二个想法是以某种方式抓取调制解调器的管理界面，因为客户端列表可以在界面的家长控制部分获得。在此之前，我检查了网络请求，发现它使用 HTTP basic 与后端通信，后端返回一个完整的 HTML 响应，其值嵌入到 HTML 的脚本部分。当我检查 HTML 主体时，我发现它包含四个主要变量，这些变量以一种奇怪的方式拥有所有必需的值:`leases`、`arps`、`wireless`和`ipmonitor`。这些值中的每一个都保存着数组的数组，简化后，它们就像这样:

```
var leases = [["40:D3:05:G7:BB:F9", "192.168.2.228"]];	// [MAC, ip]
var arps = [["192.168.2.22", "40:0E:85:52:D4:43"]];	// [ip, MAC]
var wireless = [["40:F3:08:F7:BB:F9", "Yes", "Yes"]];	// [MAC, assoc, auth]
var ipmonitor = [["192.168.2.12", "3C:BD:D8:D7:E8:17", "3CBDD8D7E817"]]; // [IP, MAC, DeviceName]
```

我对网络非常无知，因此我不知道什么是`leases`，也不知道为什么这些东西会存储在不同的变量中，比如`leases`和`ipmonitor`，每个变量都有不同的属性和细节；然而，我有预感这些就是我一直在寻找的东西。在摆弄它的时候，我注意到`ipmonitor`变量总是最大的数组，它包含了分散在其他变量中的所有项目，它似乎是关于连接设备的事实的来源。作为世界上最差的 python 程序员，我认为这是 python 的一份好工作。

```
def get_leases_arps_monitors(contents):
    contents = contents.split('\n')
    leases = []
    arps = []
    ip_monitors = []
    wireless_devices = [] # Loop over the lines to find items.
    found_count = 0
    for line in contents:
        if 'var leases' in line and not leases:
            leases = line.split("= ", 1)[1]
            leases = leases.split(";")[0]
            leases = json.loads(leases)
            found_count += 1
        elif 'var arps' in line and not arps:
            arps = line.split("= ", 1)[1]
            arps = arps.split(";")[0]
            arps = json.loads(arps)
            found_count += 1
        elif 'var ipmonitor' in line and not ip_monitors:
            ip_monitors = line.split("= ", 1)[1]
            ip_monitors = ip_monitors.split(";")[0]
            ip_monitors = json.loads(ip_monitors)
            found_count += 1
        elif 'var wireless' in line and not wireless_devices:
            wireless_devices = line.split("= ", 1)[1]
            wireless_devices = wireless_devices.split(";")[0]
            wireless_devices = json.loads(wireless_devices) found_count += 1 if found_count == 4:
            break return leases, arps, ip_monitors, wireless_devices
```

一旦我得到了这些变量的值，就很容易通过遍历它们来构建一个简单的字典数组。我构建了一个简单的类来完成所有这些工作，包括获取 HTML 响应、解析它并构建一个已连接客户端的列表。

以下是完整的代码:

检索连接到路由器的客户端列表的完整代码

最后，我用两行代码列出了客户列表:

```
asus = Asus('192.168.2.1', 'admin_username', 'admin_password')
clients = asus.get_clients()
```

商品列表应该是这样的，准备好供客户消费:

```
[
    {
      "is_wireless": false,
      "ip": "192.168.2.12",
      "mac": "DB:45:D8:C3:38:14",
      "name": "burak's desktop computer"
    },
    {
      "is_wireless": true,
      "ip": "192.168.2.228",
      "mac": "50:33:D8:37:CC:F5",
      "name": "burak's phone"
    }
]
```

到目前为止，一切都很完美。

# 然后呢？

既然获取客户端列表的代码已经准备好了，是时候构建一个简单的前端来显示这些内容了，方法是用`setInterval`连续轮询后端，大约每 5 秒钟一次。在这一点上，一切都准备好了，但猜猜谁不能保持冷静，不要搞砸了？

在我的内心深处，我仍然在思考我以前遇到的 nmap 问题；基本上，调制解调器无法处理扫描，这让我预感到持续轮询其管理界面会消耗其资源，并使每个人的连接饱和，我不想这样。我有数据支持这种说法吗？不。我知道调制解调器是怎么工作的吗？没有。我是否做了任何概念验证来测试实际情况是否如此？当然不是。

我坚信自己的直觉，并专注于这个不存在的、不现实的问题，我决定用 [OpenWrt](https://openwrt.org/) 替换调制解调器的默认固件，这将更加稳定，允许我用更少的代码获得更多的指标，并简化这里的工作。我有数据支持这种说法吗？没有。我知道调制解调器固件是如何工作的吗？没有。我是否做了任何概念验证来测试实际情况是否如此？当然不是。再一次，我只有纯粹的直觉。

最后，我用一个定制的开源固件替换了我路由器的默认固件，以锁住一扇该死的旧门。

—在这里插入一个巨大的掌脸—

# 尝试升级路由器固件

我依稀记得它是如何发生的，但我记得读过路由器关于更新固件的文档，文档中有一个用粗体字写的句子，声明**如果过程不知何故失败或中途中断，设备将会死亡**。我读了这份声明，并立即在我的脑海中将其标记为无关紧要，因为升级这个简单的小设备应该很快很容易。再问一次，我有任何数据来支持这种说法吗？没有。我知道升级过程是如何进行的吗？没有。我是否做了任何概念验证来测试实际情况是否如此？**当然不是。**

猜猜发生了什么？**发生了一些事情，路由器决定在升级过程中自行重启。**本质上，我用砖头砌好了我完美的路由器，用于锁住一扇生锈的旧门，即使路由器工作了，它也永远不会出现；你可以在这里插入另一个巨大的 facepalm。

# 我不得不下结论

在做了所有这些工作之后，我所拥有的只是一个简单的 Python 脚本，这个脚本应该是针对这个已经死了的华硕路由器的。最终，在大约 3 个小时没有互联网连接后，我买了另一台路由器，一切又恢复了正常。因此，我有了:

*   一个 Python 脚本，用于提取连接到我的 Asus 路由器的客户端
*   一个死掉的华硕路由器
*   全新的路由器
*   一个意志消沉的 CS 学生(*提示:这就是我*)

总的来说，这是一个有趣的失败实验，花了我一个路由器，但它很有趣，这是我开始研究这个的主要原因。作为额外的代价，当我解释为什么几个小时没有网络连接时，我的家人开了我一点玩笑，但这没关系，我活该。

如果你想建造类似的东西，我建议以某种方式更换门锁，这样每个人都可以使用好的旧钥匙，而不是像我计划的那样复杂的设置；更换锁可能比购买新路由器更便宜。

![](img/e011040dd8d1fc9764fee5b47caecfbe.png)

我砌好路由器后的样子，照片由[查尔斯](https://unsplash.com/@charlesdeluvio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/animal?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄