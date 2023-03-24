# 使用看门狗定时器确保 Node.js 程序正常运行

> 原文：<https://levelup.gitconnected.com/ensuring-healthy-node-js-program-using-watchdog-timer-9170fa35cf68>

如果您有一个 Node.js 程序，该程序被设计为异步提取任务并处理它们，那么您应该注意挂起的进程。

![](img/b69315dcd7ed3fd91b97730ab829ac94.png)

监控 Node.js 程序运行状况。

考虑这样一个程序的例子:

```
import delay from 'delay';const getNextJob = async () => { /* ... */ };
const doJob = async () => { /* ... */ };const main = async () => {
  while (true) {
    const maybeNextJob = await getNextJob(); if (maybeNextJob) {
      await doJob(maybeNextJob);
    } else {
      await delay(1000);
    }
  }
};main();
```

`getNextJob`用于从任意数据库中提取任务指令，`doJob`用于执行这些任务。

这里的风险是任何异步任务都可能无限期挂起，例如，如果`getNextJob`从远程数据库中提取数据，数据库套接字可能会无限期挂起。几乎可以肯定，这总是一个错误。

在我的具体案例中，我遇到了一个`node-postgres`中的[错误](https://github.com/brianc/node-postgres/issues/1952)，导致连接在`ClientRead`状态下挂起。后者发生在服务器看到开始查询的协议消息，但它还没有返回到空闲状态时，这发生在服务器在查询结束时发送`ReadyForQuery`响应时。PostgreSQL 没有为`ClientRead`设置超时，也就是说，这会导致我的`getNextJob`无限期挂起。

防范这种风险的最好方法是给用于拉取和执行任务的循环添加一个超时。每次迭代都应刷新超时值；当超时没有及时重置时，您应该终止进程并记录足够的详细信息，以确定导致进程挂起的原因。这种模式称为[看门狗定时器](https://en.wikipedia.org/wiki/Watchdog_timer)。

下面是看门狗定时器的一个示例实现:

```
import delay from 'delay';const getNextJob = async () => { /* ... */ };
const doJob = async () => { /* ... */ };const main = async () => {
  const timeoutId = setTimeout(() => {
    console.error('watchdog timer timeout; forcing program termination'); process.exit(1);
  }, 30 * 1000); timeoutId.unref(); while (true) {
    timeoutId.refresh(); const maybeNextJob = await getNextJob(); if (maybeNextJob) {
      await doJob(maybeNextJob);
    } else {
      await delay(1000);
    }
  }
};main();
```

这将创建一个计时器，它会在每次检查新任务的循环开始时刷新。30 秒的超时适用于整个周期(即`getNextJob`和`doJob`),因为您正在强制突然终止，所以它应该远高于内部任务限制。

我必须在我的多个应用程序中实现上述模式，以防止这些幽灵进程挂在使用 Kubernetes 编排的许多进程的大规模部署中。因此，我将上述逻辑+一些糖抽象成一个模块[看门狗定时器](https://github.com/gajus/watchdog-timer)。在很大程度上，它可以像前面使用`setTimeout`的例子一样使用:

```
import {
  createWatchdogTimer,
} from 'watchdog-timer';
import delay from 'delay';const getNextJob = async () => { /* ... */ };
const doJob = async () => { /* ... */ };const main = async () => {
  const watchdogTimer = createWatchdogTimer({
    onTimeout: () => {
      console.error('watchdog timer timeout; forcing program termination'); process.exit(1);
    },
    timeout: 1000,
  }); while (true) {
    watchdogTimer.refresh(); const maybeNextJob = await getNextJob(); if (maybeNextJob) {
      await doJob(maybeNextJob);
    } else {
      await delay(1000);
    }
  }
};main();
```

需要强调的是，这是一个进程内保护，也就是说，如果有什么东西阻塞了事件循环，那么就不会调用超时。为了防止后者，您还需要一个外部服务来检查应用程序的活性。如果您正在使用 Kubernetes，那么这个功能由`[livenessProbe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)`提供，并且可以使用`[lightship](https://github.com/gajus/lightship)` NPM 模块来实现。

`watchdog-timer`与 Lightship 完美融合:

```
import {
  createWatchdogTimer,
} from 'watchdog-timer';
import {
  createLightship,
} from 'lightship';const main = async () => {
  const lightship = createLightship({
    timeout: 5 * 1000,
  }); lightship.signalReady(); lightship.registerShutdownHandler(async () => {
    console.log('shutting down');
  }); const watchdogTimer = createWatchdogTimer({
    onTimeout: () => {
      // If you do not call `destroy()`, then
      // `onTimeout` is going to be called again on the next timeout.
      watchdogTimer.destroy(); lightship.shutdown();
    },
    timeout: 1000,
  }); while (true) {
    if (lightship.isServerShuttingDown()) {
      console.log('detected that the service is shutting down; terminating the event loop'); break;
    } // Reset watchdog-timer on each loop.
    watchdogTimer.reset(); // `foo` is an arbitrary routine that might hang indefinitely,
    // e.g. due to a hanging database connection socket.
    await foo();
  } watchdogTimer.destroy();
};main();
```

综上所述，为了避免挂起进程，你必须有一个进程内看门狗来发现你的应用什么时候处于空闲/没有执行预期的步骤；您必须使用一个进程外看门狗来确保应用程序不会陷入阻塞事件循环。