# 典型的类型脚本:使用联合强制输入

> 原文：<https://levelup.gitconnected.com/typical-typescript-using-unions-to-enforce-inputs-2413abb12805>

## 用类型表达业务逻辑

![](img/4997fdb8e4ff7656a498b71ea22af5c3.png)

今年早些时候，我被拉进了一些与 CCPA 有关的紧急工作中。简而言之，当相关的票据离开队列时，服务没有正确地关闭它们，这导致了大量手工工作的积压。对任何参与其中的人来说都毫无乐趣。

在调查过程中，我发现我们发送的记录中有几个令人苦恼的错误:

*   必需的`serviceNowTaskId`在几个关键地方被拼错了(启动时不一致)。例如，`servideNowTaskid`😬
*   更糟糕的是，`clientRequestId`(也是必要的)在每次通话中都完全消失了，这加剧了失败💨

```
*// An example of what this looked like in practice* sendRecords({
  records: [
    {
      status: 'OK',
      payload: {
        assetId: 12345,
        servidenowTaskid: 'doomed for failure',
        workNotes: 'What could go wrong?',
      },
    },
  ],
  topic: 'It be like this sometimes',
});
```

请记住，这个项目*是用 TypeScript 编写的*，所以我非常好奇为什么这在一开始就有可能。让我感到恐怖的是，对罪魁祸首接口的快速调查显示:

```
export interface SendRecordRequest {
  readonly records: ReadonlyArray<any>; 🤦‍♂️
  readonly topic: string;
}
```

可怕的`any`！这解释了为什么最初没有类型检查来捕捉错误。很可能是一个仓促的特点，需要一点勤奋。

## 充实类型并获得内心的平静

我们绝对可以在这里的`record`类型上多下一点功夫，为我们自己和未来的任何人省去许多痛苦。为了确保我们发送的所有记录都具有正确的属性，我创建了一个简单的界面，并在我的 IDE 中看到了所有生成的红色曲线…

```
export interface SendRecordRequest {
 **readonly records: SnowRecord[]; // 😀 type-checked records!**  readonly topic: string;
}// Flesh out the type for an individual record
export interface SnowRecord {
  status: 'OK';
  payload: {
    clientRequestId: string;
    serviceNowTaskId: string;
    status: SnowStatus;
    workNotes?: string | null;
  };
};// Idea, move common strings into an enum for easy refactors...
export enum SnowStatus {
  WIP = 'Work in Progress',
  CLOSED = 'Closed Complete',
  ...
};
```

这导致编译器在`serviceNowTaskId`和其他属性被拼错的地方立即发出警告，如果我们缺少一个状态，包括一些意想不到的东西，以及所有其他来自 TypeScript 的一般优点，如自动完成建议。

```
// The new developer experience!
sendRecords({
  records: [
    {
      status: 'OK',
      payload: { **🚨 TS error, no `status`!**
        assetId: 12345,
        clientRequestId,
        servicenowTaskid: snowId, **🚨 TS error, unexpected prop!**
        workNotes: "Wow, that's handy",
      },
    },
  ],
  topic: 'Confidence!',
});
```

最后，世界的这一小块地方一切都很好，很正常。

## 新的要求来了

在我解决了这个令人尴尬的问题后不久，来自管理层的新要求是给我们的有效载荷添加一个强制性的“闭包代码”。这将让他们更深入地了解票证被关闭的原因。很公平，但是如果一个给定的标签被设置为`CLOSED`，那么包含关闭代码*只有*才有意义；你不会想让它出现在正在进行的票据上的！此外，如果记录包含关闭代码和非关闭状态，队列将拒绝该记录。

这给我刚刚制作的干净、简单的界面带来了一个问题，也给我想让代码帮助指导未来落入这一领域的开发人员的梦想带来了问题。我当然可以给有效载荷添加一个`closureCode?: string`，然后就结束了，但是这样我们就不会强制执行`closureCode`和`status`之间的关系。我可以只添加评论，但我想做得更好。

## 等等，我们可以用工会

让我们来看看一个简单的方法，然后清理它！

```
export interface SnowRecord {
  status: 'OK';
 **payload: SnowPayloadWip | SnowPayloadClosed;** }interface SnowPayloadWip {
  assetId: number;
  serviceNowTaskId: string;
  status: SnowStatus.WIP;
  workNotes?: string | null;
}interface SnowPayloadClosed {
  assetId: number;
  serviceNowTaskId: string;
  status: SnowStatus.CLOSED;
  workNotes?: string | null;
  closureCode: SnowClosureCode;
}
```

在实践中，用法也非常巧妙！

```
sendRecords({
  records: [
    {
      status: 'OK',
      payload: { **🚨 TS error, expected `closureCode`**
        assetId: 12345,
        clientRequestId,
        serviceNowTaskId: snowId,
        status: SnowStatus.CLOSED,
        workNotes: "Wow, that's handy",
      },
    },
  ],
  topic: 'Flexibility!',
});// Alternatively
sendRecords({
  records: [
    {
      status: 'OK',
      payload: {
        assetId: 12345,
        clientRequestId,
        serviceNowTaskId: snowId,
        status: SnowStatus.WIP,
        closureCode: SnowClosureCode.NO_DATA, **🚨 TS error!**
      },
    },
  ],
  topic: 'Flexibility!',
});
```

让我们从有效载荷的角度来看这个问题，我想说的关键点是:

```
payload: SnowPayloadWip | SnowPayloadClosed
```

这是一个两种类型的*联合*，这意味着`payload`可以是*WIP 形状或闭合形状(或者更多，如果我们扩展联合！)，但不能两者兼而有之。这对于我们的情况来说是完美的，因为当开发人员试图创建无效的属性组合时，它会发出警告！例如，如果一个开发人员试图编写一个代码块，发送一个状态为 WIP *的记录，并且*包含一个结束代码，他们会从 IDE 中得到一个关于`closureCode`是一个意外属性的警告！同样，如果状态是 closed，他们会因为没有包含`closureCode`而收到警告。*

这是可行的，但是那些界面看起来非常重复，作为开发者，我们讨厌不必要的重复！

## 干的时候！

使用一点额外的 TypeScript-fu，我们可以[通过创建所有公共属性的基本接口，然后用我们需要的每种组合的配置来扩展它，从而完成这个任务。](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

```
interface SnowPayload {
  assetId: number;
  serviceNowTaskId: string;
  status: SnowStatus;
  workNotes?: string | null;
}interface SnowPayloadWip extends SnowPayload {
  status: SnowStatus.WIP;
}interface SnowPayloadClosed extends SnowPayload {
  status: SnowStatus.CLOSED;
  closureCode: SnowClosureCode;
}
```

因为我们扩展了`SnowPayload`，这些新接口中的每一个都包含了它的属性，并且还可以覆盖它们原来的定义(比如`status`)或者添加其他的。这移除了两个接口之间的重复属性，并且更简洁地描述了唯一的位！

总的来说，结果如下所示:

```
export interface SendRecordRequest {
  readonly records: SnowRecord[];
  readonly topic: string;
}export interface SnowRecord {
  status: 'OK';
  payload: SnowPayloadWip | SnowPayloadClosed; ✨
}interface SnowPayload {
  assetId: number;
  serviceNowTaskId: string;
  status: SnowStatus;
  workNotes?: string | null;
}interface SnowPayloadWip extends SnowPayload {
  status: SnowStatus.WIP;
}interface SnowPayloadClosed extends SnowPayload {
  status: SnowStatus.CLOSED;
  closureCode: SnowClosureCode;
}export enum SnowStatus {
  WIP = 'Work in Progress',
  CLOSED = 'Closed Complete',
  ...
}export enum SnowClosureCode {
  ...
}
```

这有点长，但希望现在您对 TypeScript 如何帮助捕捉您自己或您团队的代码中的错误有了一点点的了解。此外，我希望这可以作为一个真实世界的例子，说明如何使用联合来严密地描述软件约束，让您的 IDE 保护您不犯业务逻辑错误，并且如果您的团队成员不得不用比您更少的上下文来维护代码，也可以帮助他们避免类似的问题😃。

欲了解更多直接来自 ts 中工会来源的趣闻，[请查看文档](https://www.typescriptlang.org/docs/handbook/advanced-types.html#union-types)！

有更好的解决方案吗？觉得我能把这篇文章写得更好吗？在评论或消息中让我知道；我总是乐于接受更多的学习！