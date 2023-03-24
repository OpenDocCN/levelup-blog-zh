# 使用无服务器功能创建安全的 Wordle

> 原文：<https://levelup.gitconnected.com/creating-a-secure-wordle-using-serverless-functions-6a5e6f280d40>

![](img/5fe7cb0e828fdc609d9244aa25983106.png)

# 介绍

过去几周，Wordle 已经在 Twitter 上疯传。这是一个非常简单的游戏，而且非常成功。但是，[人们能够对它进行逆向工程](https://reichel.dev/blog/reverse-engineering-wordle.html)，找出未来的单词。我想试着做一个不能这样逆向工程的东西。我确实制作了一个，但是由于有数百个“如何制作 Wordle”的教程，我将只关注“如何使它更安全”的部分。我将为它使用无服务器功能。

*注意:如果你的博客病毒式传播，无服务器功能可能会有点贵。*

# 沃尔多怎么了？

我不会说 [Wordle](https://www.powerlanguage.co.uk/wordle/) 有什么问题。只是它的开发者做的一个选择。我确信如果乔希·沃德尔想要的话，他会让它变得更安全，如果我处在他的位置，我也会像他一样成功。

为什么？因为如果流量太大，使用无服务器功能的成本很高。

[Robert Reichel](https://reichel.dev/) 写了一篇关于逆向工程 word 的好文章，解释了 [Josh Wardle 的 word](https://powerlanguage.co.uk/wordle/)如何在客户端确定单词。

> 至此，我们已经做了足够的挖掘来了解 Wordle 是如何选择每日一词的。我们知道，Wordle 使用客户端基于日期的算法来确定从静态单词列表中使用哪个单词。只要我们把所有的代码拼凑在一起，每一天都是可以预测的
> 
> *—* [*逆向工程*](https://reichel.dev/blog/reverse-engineering-wordle.html)

# 我说的安全是什么意思？

所谓“安全”，我的意思是没有人能够知道明天(或后天，或未来的任何一天)的单词是什么。玩一次游戏，看到单词，然后在不同的浏览器中再玩一次，就可以知道今天的单词是什么。或者如果你知道怎么做，你可以向 API 发送一个请求，它会告诉你今天的单词。这有什么用？这将防止像"[Wordlinator](https://www.thegamer.com/wordle-twitter-bot-the-wordlinator-spoler/)"这样的机器人破坏其他人的游戏。

此外，使用这种方法的另一个优点是。无论你在世界的哪个地方玩，每个人都将同时获得新单词，因为选择将基于服务器的时钟，而不是客户端的时钟。许多 Wordle 游戏都有这个问题，有些人比世界上其他人更早开始学习新单词，因为对他们来说这是新的一天的 12:00，而世界上其他人仍然是前一天。

# 怎么做？

我不会写如何制作整个 Wordle 游戏，只写 API /无服务器功能部分。它可以部署在您喜欢的任何平台上。我在 [Next.js](https://nextjs.org/) 和 [Vercel](https://vercel.com/) 上部署了地雷。

# 要求:

*   每天要选择的单词列表。如果这是一个更大的单词列表的子集，决定哪个单词被接受，哪个不被接受，那就更好了。单词接受逻辑和大列表可以是客户端的。(这样会更好，因为这样可以减少 API 的负载，还可以省钱)。较小的单词列表永远不会加载到客户端。

# 逻辑:

它的逻辑非常简单。我们将制定一条 API 路线:

1.  加载单词列表，我们每天从中选择一个单词
2.  计算自某些固定数据以来的天数(例如，应用程序/游戏启动的日期)。
3.  使用所计算的差异从单词列表中选择单词并作出响应。

# 代码:

```
// Next.js API route support: https://nextjs.org/docs/api-routes/introduction  
import { DateTime } from 'luxon';  
import type { NextApiRequest, NextApiResponse } from 'next'  
import { GameData } from '../../lib/interfaces';  
import gameWords from "../../data/selected.json"; // The list of words// Function to calculate the difference between today and and a fixed date  
function getIndex():number {  
    let start = DateTime.fromFormat("31/01/2022","dd/mm/yyyy").setZone("UTC+5:30").startOf("day")  
    let today = DateTime.now().setZone("UTC+5:30").startOf("day")  
    return today.diff(start,'days').get('days');  
}export default function handler(req: NextApiRequest,res: NextApiResponse<GameData>) {  
    let id = getIndex();  
    res.status(200).json({  
        id: id,  
        word: gameWords\[id\]  
    });  
}
```

此外，如果您愿意，可以创建另一个 API 端点来返回下一个单词的剩余时间。

```
import { NextApiRequest, NextApiResponse } from "next/types";  
import {DateTime} from "luxon";export default function handler(  
    req: NextApiRequest,  
    res: NextApiResponse<number>  
  ) {  
    let t = DateTime.now().setZone("UTC+5:30").startOf('day').plus({days:1}).valueOf()  
    res.status(200).send(t)  
  }
```

有了这两个 API，你可以制作一个更加安全的 Wordle 游戏。

# 参考资料:

*   乔希·沃德尔的文字
*   [逆向工程 Wordle — Robert Reichel](https://reichel.dev/blog/reverse-engineering-wordle.html)
*   [Next.js API 路由](https://nextjs.org/docs/api-routes/introduction)

# 也

*   [在我的博客上读吧](https://blog.haideralipunjabi.com/posts/creating-a-secure-wordle-using-next.js-api-routes-and-vercel-serverless-functions/)
*   [请我喝杯咖啡](https://www.buymeacoffee.com/HAliPunjabi)