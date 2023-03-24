# Javascript:等到事情发生或超时

> 原文：<https://levelup.gitconnected.com/javascript-wait-until-something-happens-or-timeout-82636839ea93>

![](img/c52cbede5bf5a9d67effb8b45741593d.png)

鲁道夫·巴雷托在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我正在为一个 Chrome 扩展编写一个内容脚本，我需要等到某个我无法控制的元素出现在 DOM 中，或者超时。

我找不到任何适合我需要的东西，所以我想出了这个异步函数，它需要一个函数来评估是否满足条件，以及以毫秒为单位的超时。它返回一个承诺，如果在超时时间内满足条件，则解决该承诺；如果超时时间已到，则拒绝该承诺。

```
const sleepUntil = async (f, timeoutMs) => {
  return new Promise((resolve, reject) => {
    const timeWas = new Date();
    const wait = setInterval(function() {
      if (f()) {
        console.log("resolved after", new Date() - timeWas, "ms");
        clearInterval(wait);
        resolve();
      } else if (new Date() - timeWas > timeoutMs) { // Timeout
        console.log("rejected after", new Date() - timeWas, "ms");
        clearInterval(wait);
        reject();
      }
    }, 20);
  });
}
```

用法(异步/等待承诺):

```
try {
    await sleepUntil(() => document.querySelector('.my-selector'), 5000);
    // ready
} catch {
    // timeout
}
```

用法(。然后答应):

```
sleepUntil(() => document.querySelector('.my-selector'), 5000)
    .then(() => {
        // ready
    }).catch(() => {
        // timeout
    });
```