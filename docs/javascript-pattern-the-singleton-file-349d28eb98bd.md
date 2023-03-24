# JavaScript 模式——“单例文件”

> 原文：<https://levelup.gitconnected.com/javascript-pattern-the-singleton-file-349d28eb98bd>

![](img/b078a5b60cd176473208b68c222333b5.png)

前几天，我偶然发现了我过去遇到的一些问题的解决方法。

我想这对于已经从事 JavaScript 工作一段时间的人来说可能是显而易见的，但是因为我花了一段时间才明白，所以我想我应该试着让其他人不要等待，即使只是一次。它也会让你们其他人开怀大笑。

我不知道这种模式的正式名称是什么，所以我称它为“单例文件”——你可以在任何地方导入它，但它只创建一个所有东西最终都会使用的单个对象。

## 问题是

我遇到的问题是日志之类的服务，这些服务需要在大多数模块启动之前可用。起初，我尝试用一个`initLogging`函数来做这件事，类似于大多数服务。然而，当系统启动并且所有模块都被导入时，一些模块代码在日志设置完成之前就已经运行了。

## 解决方案

这个解决方案并没有那么巧妙，但是它工作得很好(至少对我来说):您将实际的日志记录器“对象”作为模块的一个导出。

## 定义日志模块

我的大部分东西都使用温斯顿记录系统。我喜欢 seri log/结构化日志的外观，但是现在开始工作太麻烦了。我目前使用的日志模块看起来有点像下面的代码。警告—有点长。

```
import { createLogger, transports, format } from 'winston';

// Allow the logging level to be controlled at start time
let level = process.env.LOG_LEVEL;
if (process.env.NODE_ENV !== "test") {
  console.info(`Logging at ${level} level.`)
}

// This is the format for lines to be printed to console. The format varies
// depending on the environment.
const consoleLineFormat = format.printf(({ level, message, label, timestamp }) => {
  if (process.env.NODE_ENV === "production") {
    return `[${label}] ${level}: ${message}`;
  } else {
    return `${timestamp} [${label}] ${level}: ${message}`;
  }
});

let consoleFormat;
if (process.env.NODE_ENV === "production") {
  // Heroku captures info like timestamp anyway, don't duplicate it.
  consoleFormat = format.combine(
    format.splat(),
    consoleLineFormat
  );
} else {
  // Put more information in the dev logs
  consoleFormat = format.combine(
    format.splat(),
    format.colorize(),
    format.timestamp(),
    consoleLineFormat
  );
}

// For file logs, use JSON
const fileFormat = format.combine(
  format.splat(),
  format.json(),
  format.timestamp()
)

// Create the base logger; everything else comes from this.
// We always log to console; exceptions are always logged, regardless
// of the level that's set.
const base_logger = createLogger({
                              level: 'info',
                              transports: [
                                new transports.Console({ level: level, format: consoleFormat })
                              ],
                              exceptionHandlers: [
                                new transports.Console({ format: consoleFormat })
                              ]
                            });

// For development, also log to files. No point in that for Heroku.
if (process.env.NODE_ENV === "development") {
  base_logger
    .add(new transports.File({ filename: 'combined.log', level: 'debug', format: fileFormat }))
    .add(new transports.File({ filename: 'errors.log', level: 'error', format: fileFormat }));
}

// 'parentLogger' is exported to the other modules. They use the 'child'
// function to create a local logger, tagged with the module name.
export const parentLogger = Object();
type childOptions = {
  module: string;
}
parentLogger.child = function(opts: childOptions) {
  return base_logger.child({ label: opts.module });
}
```

很抱歉我写了这么长时间——我确实试着去掉了一些，但所有这些都是有原因的，我认为它可能对人们有用。

## 使用日志模块

幸运的是，实际使用起来很简单。

```
import { parentLogger } from "./logger";
const logger = parentLogger.child({ module: "events" })
```

就是这样。现在本地代码简单地使用`logger.info`，或者你想要的任何级别。每个模块里都是`logger`，所以我不用记住其他名字。

## 为什么有效

老实说，我没有详细调查过这个。我猜想这是因为 Node 不让模块开始运行，直到它们正在导入的模块已经完成了初始运行——否则使用依赖项将是一场噩梦。如果有人确切知道，或知道不同的，请让我知道。

就是这样。希望对某人有所帮助！

哦——如果你想知道的话，这张图片与故事完全无关。找不到合适的岗位形象，就用“可爱的小狗”代替了。