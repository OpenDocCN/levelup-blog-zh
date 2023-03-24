# 保持冷静，继续前进:redux-saga 案例研究

> 原文：<https://levelup.gitconnected.com/keep-calm-and-race-on-redux-saga-case-study-2f5b0a6630ae>

![](img/0eb380073e3e01fb3c02fbc91ad7bd32.png)

在本文中，我将通过构建一个 web 应用程序的轮询特性，展示一个使用 [Redux-Saga](https://redux-saga.js.org/) 、 [JavaScript 生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)和 [Redux](https://redux.js.org/) 的案例研究。poll API 请求是一种按照设定的时间间隔向服务器发出 AJAX 调用以检查更新的请求。然后我们将使用 [Cypress](https://www.cypress.io/) 测试我们的代码。

# 用 Redux Saga 构建一个轮询特性

假设您需要向服务器发出轮询请求，以接收由客户端触发的作业的状态。例如，让我们想象一个场景，用户上传一个文件。然后，上传成功完成后，服务器需要处理文件，然后才能在应用程序中查看。在这种情况下，您希望显示一个指示进度的状态栏，并阻止用户查看文件，直到文件完成。

服务器将对我们的轮询请求有 3 个响应:`started`、`succeeded`或`failed`。下面的代码展示了如何使用 Redux Saga 编写一个轮询请求。

```
import { take, put, *delay* } from 'redux-saga/effects'function* *checkJobStatus*() {
  let jobSucceeded = false;
  while (!jobSucceeded) {
    yield put({type: "POLLING_ACTION_REQUEST"});
    const pollingAction = yield take("POLLING_ACTION_RESPONSE");
    const pollingStatus = pollingAction.payload.response.status;
    switch (pollingStatus) {
      case POLLING_STATUS.SUCCEEDED:
        jobSucceeded = true;
        yield put({type: "HANDLE_POLLING_SUCCESS"});
        break;
      case POLLING_STATUS.FAILED:
        jobSucceeded = true;
        yield put({type: "HANDLE_POLLING_FAILURE"});
        break;
      default:
        break;
    }
    // delay the next polling request in 1 second
    yield call(*delay*, 1000);
  }
}
```

在上面的例子中，我们通过使用' [put](https://redux-saga.js.org/docs/api/#putaction) '效果执行一个请求，该效果分派一个动作。紧接着，我们使用一个名为“ [take](https://redux-saga.js.org/docs/api/#takepattern) ”的 Redux-Saga [效果](https://redux-saga.js.org/docs/api/#effect-creators)，它**会阻止**Saga 的执行，直到有人调度作为参数给出的动作。一旦`POLLING_ACTION_RESPONSE`被调度，我们检查从服务器返回的状态。如果我们得到“完成”或“失败”状态，我们需要使用“ [put](https://redux-saga.js.org/docs/api/#putaction) ”效果来相应地处理它们。否则，我们对服务器执行另一个轮询请求，依此类推，直到它完成。

> 效果只是一个对象，它包含一些由中间件解释的信息。您可以查看效果，比如对中间件执行一些操作的指令(例如，调用一些异步功能，将一个动作分派给存储，等等。).

但是，如果您想处理的不仅仅是根据服务器对轮询请求的响应进行操作，那该怎么办呢？如果您想限制对服务器的轮询时间，比如说 1 分钟，该怎么办？如果您还想让用户在处理过程中取消文件的上传，该怎么办？这听起来有点复杂，不是吗？

有了 redux-saga，这比你想象的要容易得多！

在我们看一些代码之前，让我们把它分解一下，了解一下我们在这里面临的是什么。首先，我们有异步运行的轮询请求。现在，在这个请求运行的任何时间点，用户都可以取消这个操作。

我们也不要忘记，这个请求可能因为许多原因而失败(服务器错误、错误的请求等)。这里的另一个元素是我们想要为这个请求设置的超时。当请求运行时，所有这些场景都可能在任何给定的时间发生，我们很想知道哪一个先执行，因此我们面临着一场行动的**竞赛**。

让我们看看 redux-saga 是如何做到的:

以下示例在四种效果之间运行一个`**race**` Redux-Saga 函数:

1.  对我们最初的`checkJobStatus`函数的调用。
2.  一项`CANCEL_POLLING`行动，最终可能会在商场上展开。
3.  一项`POLLING_FAILED`行动，最终可能会在商店中执行。
4.  对[的调用延迟](https://redux-saga.js.org/docs/api/#delayms-val)。`delay`是一个 Redux-Saga 实用函数，返回一个在 X 毫秒后解决的承诺。我们用它来设置比赛的暂停。

```
import { race, take, put, call, delay } from 'redux-saga/effects'function* *startPollingSaga*(action) {
    *// Race the following commands with a timeout of 1 minute* const { response, failed, timeout } = yield *race*({
      response: call(*checkJobStatus*),
      cancel: take("CANCEL_POLLING"),
      failed: take("POLLING_FAILED"),
      timeout: call(*delay*, 60000)
    }); // handle failure scenario
    if (failed) {
      yield put(*{type: "*HANDLE_POLLING_FAILURE*"}*);
    }
}
```

如果`call(checkJobStatus)`先结束，`cancel`、`failed`、`timeout`将为`undefined`。在我们的例子中,`response`也将是`undefined`,因为`checkJobStatus`不返回承诺，而是自己处理轮询响应。

如果`call(delay, 60000)`先解决，`timeout`将是`delay`和`cancel`的结果，`failed`和`response`将是`undefined`。

如果在`checkJobStatus`完成之前`CANCEL_POLLING`类型的动作被分派到商店上，`response`、`failed`和`timeout`将成为`undefined`并且`cancel`将获得被分派动作的值。

如果类型为`POLLING_FAILED`的动作在`checkJobStatus`完成之前被分派到商店，`response`、`cancel`和`timeout`将成为`undefined`并且`failed`将获得被分派动作的值。

**注意**:在`POLLING_FAILED`或`CANCEL_POLLING`动作被调度的情况下，`race`效果会自动取消`*checkJobStatus*`和`*delay*` 并抛出取消错误。

# 用 Cypress 测试 Redux Saga

现在我们可以实现上面的场景了，让我们学习如何轻松地测试它！

在这个例子中，我将使用 [Cypress](https://www.cypress.io/) 演示一个解决方案。

> 注意:我决定展示一个测试超时场景的例子，因为这可能是最有趣的一个例子。所有其他场景都非常简单。

```
***describe***(**'ui test'**, **function**() { ***it***(**'should wait for processing to timeout'**, **function**() { *// Overrides native global functions related to time allowing
    // them to be controlled synchronously before polling request* ***cy***.clock(); ***cy***.route(**'GET'**, 'upload file endpoint**'**, uploadResponse)
      .as('fileUploaded'); ***cy***.route(**'GET'**, 'polling endpoint**'**, pollingResponse)
      .as('pollingStarted'); // Since this article is talking about file upload we are using
    // a custom command to imitate the file upload because it's not
    // built-in in cypress.
    ***cy***.uploadFile('dropdown zone', 'file name**'**);

    ***cy***.wait('fileUploaded**'**); ***cy***.wait('pollingStarted**'**); *// Set the clock forward to cause a timeout* ***cy***.clock().then((clock) => {
      clock.tick(60000);
      clock.restore();
    }); // Here you can verify that the desired ui behavior is as
    // expected  

  });
});
```

那么我们这里有什么？

在定义路由和执行轮询请求之前，我们希望覆盖与时间相关的本地全局函数。这将允许我们同步控制本地全局函数。为此，我们使用`***cy***.clock();`,这样我们可以稍后决定将时钟向前设置，这样我们就可以导致超时。

在定义了上传和轮询请求路由之后，我们提交一个文件并等待它上传和轮询开始。

现在我们可以将时钟拨快:

```
***cy***.clock().then((clock) => {
 clock.tick(60000);
 clock.restore();
});
```

`clock.tick(milliseconds)`

> 将时钟移动指定数量的`*milliseconds*`。将调用受影响时间范围内的任何计时器。

`clock.restore()`

> 恢复所有被覆盖的本机函数。这是在测试之间自动调用的，所以通常不需要。

之后，您就可以验证期望的 UI 行为是否符合预期了。

那么到目前为止我们学到了什么？我们了解到，当使用 Redux-Saga 时，管理多个动作之间的**竞赛**是非常容易的。就我个人而言，我喜欢你可以在一个地方管理所有这些操作，这使得它非常直观，易于维护。我们还了解到，在使用 Cypress 时，测试一个您的某个操作导致超时的场景是非常容易的。我们看到了如何以同步方式“等待”异步超时。

就是这样！现在轮到你试一试了！