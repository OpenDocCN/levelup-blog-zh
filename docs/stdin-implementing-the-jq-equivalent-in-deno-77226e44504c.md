# STDIN:在 Deno 中实现 JQ 等价

> 原文：<https://levelup.gitconnected.com/stdin-implementing-the-jq-equivalent-in-deno-77226e44504c>

Deno 可能是编程界的下一个大事件。本文帮助您理解读取 STDIN 和使用它。我不会在这里创建整个库。相反，我将给出一个小演示，演示如何使用 Deno 读取 STDIN 数据并解析它。

`jq`就像`sed`对于 JSON 数据一样，你可以用它来切片、过滤、映射和转换结构化数据

`— [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/`)`

F**act Check:**STDIN[[标准输入流](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)) ] —标准输入是程序从中读取其输入数据的流。

![](img/595610469ca054c03bca75dba2b24740.png)

ytimg.com

## **如何创建一个标准输入**

从`stdin`传递数据非常容易。您可以使用`<`将数据传输到任何程序。

## **例如:**

```
deno run program.ts < file_name.txt
deno run programe.ts < echo "data here"
```

您还可以使用管道`(|)`将任何程序的输出传递给另一个程序。

## **例如:**

```
cat file_name.txt | deno run program.ts
echo "data here" | deno run programe.ts
```

## **如何读取 Deno 中的 stdin**

读取 stdin 与读取 Deno 中的流非常相似。Deno 提供了类似`Deno.read`和`Deno.readAll`的核心 API。

```
// examples/advance_jq.tsconst stdinContent = await Deno.readAll(Deno.stdin);
console.log(stdinContent);
```

**运行:** `deno run examples/advance_jq.ts < examples/advance_jq.ts`

当你运行这个程序时，会打印出一些数字(`Uint8Array`)。像其他语言一样，流数据是在缓冲区中编码的缓冲数据。要转换我们需要`TextDecoder`。

```
// examples/advance_jq.tsconst stdinContent = await Deno.readAll(Deno.stdin);
const response = new TextDecoder().decode(stdinContent);
console.log(response);
```

**运行:** `deno run examples/advance_jq.ts < examples/advance_jq.ts`

您可以看到您的文件数据作为输出

**解析 JSON**

解析 JSON 并提取值是一项非常繁琐的任务。我已经根据提供的键编写了一个从对象中提取值的基本程序。代码如下所示:

```
const evalReg = /(\.)|(\[(\d)\])/;
const safeEval = (key: string, obj: any) => {
  let lastKey;
  let match;
  do {
    if (lastKey) {
      if (match && match[2]) {
        obj = obj[lastKey][match[3]];
      } else {
        obj = obj[lastKey];
      }
    }
    match = evalReg.exec(key);
    if (!match) {
      lastKey = key;
      break;
    } else {
      lastKey = key.substr(0, match.index);
      key = key.slice(!match[3] ? match.index + 1 : match.index + 3);
    }
  } while (match);
  if (lastKey) {
    obj = obj[lastKey];
  }
  return obj;
};
```

这里我使用`RegExp.exec` [ [more](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec) ]方法解析密钥并提取令牌。这是 JQ 所能做的一个非常粗略的例子。所以`safeEvel`代码也小😁。

## 这种方法的工作原理:

```
const obj = {
  id: 1,
  version: "1.0.1",
  contributors: ["deepak", "gary"],
  actor: {
    name: "Tom Cruise",
    age: 56,
    "Born At": "Syracuse, NY",
    Birthdate: "July 3 1962",
    movies: ["Top Gun", "Mission: Impossible", "Oblivion"],
    photo: "[https://jsonformatter.org/img/tom-cruise.jpg](https://jsonformatter.org/img/tom-cruise.jpg)",
  },
};
console.log(JSON.stringify(obj, null, 2));
console.log(safeEval("id", obj));
console.log(safeEval("contributors", obj));
console.log(safeEval("contributors[1]", obj));
console.log(safeEval("actor.movies[2]", obj));
```

## 输出:

```
1
[ "deepak", "gary" ]
gary
Oblivion
```

如你所见，这正是我们所需要的。让我们完成实际的演示。

**【注:】**感谢 Deno `import`，现在我可以直接从 Github 使用这个文件了。我不需要创建另一个文件来导入。你能做到的。不过，我会用网络来`import`。

```
import safeEval from "[https://raw.githubusercontent.com/deepakshrma/deno-by-example/master/examples/safe_eval.ts](https://raw.githubusercontent.com/deepakshrma/deno-by-example/master/examples/safe_eval.ts)";const stdinContent = await Deno.readAll(Deno.stdin);
const response = new TextDecoder().decode(stdinContent);try {
  console.log(safeEval(key, JSON.parse(response)));
} catch (err) {
  console.log(response);
}
```

但是等等，我们将从哪里得到丢失的钥匙呢？

![](img/58fe16045d315624bb602599f44cb87b.png)

(Paolo Nicolello 在 Unsplash 上的照片

Deno 提供了对使用 CLI 传递给程序的参数的直接访问。我们可以使用`Deno.args`将所有参数作为数组传递给程序。让我们使用它。

```
import safeEval from "[https://raw.githubusercontent.com/deepakshrma/deno-by-example/master/examples/safe_eval.ts](https://raw.githubusercontent.com/deepakshrma/deno-by-example/master/examples/safe_eval.ts)";const stdinContent = await Deno.readAll(Deno.stdin);
const response = new TextDecoder().decode(stdinContent);const [key = ""] = Deno.args;
try {
  console.log(safeEval(key, JSON.parse(response)));
} catch (err) {
  console.log(response);
}
```

你可以创建一个 JSON( `tom.json`)并试用。

```
/* tom.json */
{
  "id": 1,
  "version": "1.0.1",
  "contributors": ["deepak", "gary"],
  "actor": {
    "name": "Tom Cruise",
    "age": 56,
    "Born At": "Syracuse, NY",
    "Birthdate": "July 3 1962",
    "movies": ["Top Gun", "Mission: Impossible", "Oblivion"],
    "photo": "[https://jsonformatter.org/img/tom-cruise.jpg](https://jsonformatter.org/img/tom-cruise.jpg)"
  }
}
```

**运行:**

```
$ deno run examples/advance_jq.ts "id" < examples/tom.json
## 1$ deno run examples/advance_jq.ts "actor.name" < examples/tom.json
## Tom Cruise
```

完美:让我们试试卷发

```
curl -s -k [https://raw.githubusercontent.com/deepakshrma/deno-by-example/master/examples/tom.json](https://raw.githubusercontent.com/deepakshrma/deno-by-example/master/examples/tom.json) | deno run  examples/advance_jq.ts "actor.movies[1]"Output: Mission: Impossible
```

不错！任务:**我有可能**

更多示例，请访问:[https://deepakshrma.github.io/deno-by-example/](https://deepakshrma.github.io/deno-by-example/)

阅读更多关于`Deno.readAll`([https://doc . deno . land/https/github . com/deno land/deno/releases/latest/download/lib . deno . d . ts # deno . read all](https://doc.deno.land/https/github.com/denoland/deno/releases/latest/download/lib.deno.d.ts#Deno.readAll))

提前感谢您的支持和建议。

对于代码回购:

[](https://github.com/deepakshrma/deno-by-example) [## deepakshrma/deno-举例说明

### 在 GitHub 上创建一个帐户，为 deepakshrma/deno-by-example 开发做出贡献。

github.com](https://github.com/deepakshrma/deno-by-example)