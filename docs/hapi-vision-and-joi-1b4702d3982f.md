# 在哈比神中使用模板和验证输入

> 原文：<https://levelup.gitconnected.com/hapi-vision-and-joi-1b4702d3982f>

![](img/88a6c80a64ee0f2c9258f4e29eca7e2c.png)

照片由[内森·杜姆劳](https://unsplash.com/@nate_dumlao?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在这篇文章中，我们将把以前的文章添加到我们的应用程序中。我们将使用 Vision 进行模板渲染，使用 Joi 进行输入验证。

# 视力

[Vision](https://hapi.dev/module/vision/) 向`Server`、`Request`和`ResponseToolkit`接口添加方法，这样您就可以使用视图引擎来呈现模板。它不知道所使用的特定引擎；在这篇文章中，我们将使用 [EJS](https://ejs.co/) ，仅仅因为我对它很熟悉。

## 设置

包裹的通常处理:

```
$ yarn add @hapi/vision ejs
$ yarn add -D @types/hapi__vision @types/ejs
```

我们还需要放置模板的地方；这不是很有想象力，但我通常使用一个名为`templates`的目录。

```
$ mkdir templates 
```

下面列出的`registerVision`函数注册并配置插件。插件选项显示使用哪个`path`(在我们的例子中是`templates`)。`relativeTo`选项表示它位于顶层，和`package.json`在一起，而不在源代码目录中。

将模板目录放在顶层的一个原因是——如果它在`src`目录中，那么在 TypeScript 编译之后，它必须被复制到`lib`。不难，但这是我们不需要做的工作。

最后，我在开发中禁用了缓存，这样我就不必在每次更改模板时重启服务器。

`src/server.ts`:

```
async function registerVision(server: Server) {
  let cached: boolean;

  await server.register(hapiVision);

  if (!process.env.NODE_ENV || process.env.NODE_ENV === "development") {
    cached = false;
  } else {
    cached = true;
  }
  console.log(`Caching templates: ${cached}`);
  server.views({
    engines: {
      ejs: require("ejs")
    },
    relativeTo: __dirname + "/../",
    path: ‘templates’,
    isCached: cached
  });
}
```

注意，我们根本不用改变现有的`/`路线；我们可以同时使用视觉和其他反应方法。

## 试验

为了测试，我们将添加另一个包，`node-html-parser`。我们希望能够从应用程序发回的网页中提取一些细节，以确认(至少在某种程度上)它看起来像我们预期的那样。

注意:我们将把 HTML 测试保持在最低限度，这样就不会变成一篇真正的长文。在现实生活中，你的测试可能会更广泛。

```
$ yarn add -D node-html-parse
```

`test/test.people.ts:`

```
import { Server } from "@hapi/hapi";
import { describe, it, beforeEach, afterEach } from "mocha";
import { expect } from "chai";
import { parse } from "node-html-parser";// If the tests were in JavaScript we'd need to use ../lib/
import { init } from "../src/server";

const personData = { name: "Sherlock Holmes", age: 32 }

describe.only("server handles people", async () => {
	let server: Server;

	beforeEach(async () => {
		server = await init();
	})
	afterEach(async () => {
		await server.stop();
	});

	it("can see existing people", async () => {
		const res = await server.inject({
			method: "get",
			url: "/people"
		});
		expect(res.statusCode).to.equal(200);
		expect(res.payload).to.not.be.null;
		const html = parse(res.payload);
		// Look for a list item with the person-entry class
		const people = html.querySelectorAll("li.person-entry");;
		// We seeded two of them.
		expect(people.length).to.equal(2);
	});

	it("can show 'add person' page", async () => {
		const res = await server.inject({
			method: "get",
			url: "/people/add"
		});
		expect(res.statusCode).to.equal(200);
	});

	it("can add a person and they show in the list", async () => {
		let res = await server.inject({
			method: "post",
			url: "/people/add",
            payload: personData
		});
		expect(res.statusCode).to.equal(302);
		expect(res.headers.location).to.equal("/people");

		res = await server.inject({
			method: "get",
			url: "/people"
		});
		expect(res.statusCode).to.equal(200);
		expect(res.payload).to.not.be.null;
		const html = parse(res.payload);
		const people = html.querySelectorAll("li.person-entry");;
		// Did the person get added, and show up?
		expect(people.length).to.equal(3);
	});
})
```

所以这看起来非常像我们上次写的测试，这是使用像 mocha 和 chai 这样的工具的优势之一。您会注意到的唯一区别是，我们解析 HTML 来查看列表中有多少人。这显然可以做得更好——检查页面标题，检查正确的按钮是否可用，诸如此类。

因为我们还没有写任何代码，如果我们运行它，它会失败。这篇文章已经够长了，所以我就不贴进去了。

## 模板

这是一个非常简单的页面，有一个无序的列表。

EJS 模板逻辑期望`people`是一个`person`结构的数组；然后循环为每个条目构建一个`<li>`元素。Vision 在渲染时将`people`数组传递给模板；我们将在下一部分看到这一点。

```
<html>
	<head>
		<title>Purple People Eaters</title>
	</head>
	<body>
		<ul>
			<% people.forEach(person => { %>
				<li class="person-entry">
					<%= person.name %> - <%= person.age %>
				</li>
			<% }) %>
		</ul>
		<a href="/people/add">Add person</a>
	</body>
</html>
```

`templates/addPerson.ejs`是非常标准的形式；EJS 扮演的唯一角色是使用提供的`person`结构来设置输入字段的初始值。如果用户犯了一个错误，页面必须重新呈现，这很有帮助——如果我们将(坏的)数据传递回呈现，那么他们不会丢失他们输入的内容。

```
<html>
	<head>
		<title>Purple People Eaters</title>
	</head>
	<body>
		<h1>Add person</h1>
		<form method="POST">
			<label for="name">Name</label>
			<input type="text" name="name" value="<%= person.name %>">
			<label for="age">Age</label>
			<input type="text" name="age"  value="<%= person.age %>">
			<button type="submit">Add</button>
		</form>
	</body>
</html>
```

我们已经有了测试和模板——我们现在只需要添加代码。

## 密码

使用视觉出奇的简单。它将一个`view`方法添加到`ResponseToolkit`中，当被调用时，它将使用上下文中的数据呈现一个模板:

```
h.view("template_file", { data: someDataForTheTemplate })
```

您会注意到，在`addPersonGet`中，我们声明了一个空结构(作为`Person`类型)并将其传递给视图。如果我们不这样做，那么每次我们使用它时，我们必须在表达式中使用它之前检查变量是否存在，否则 EJS 会抛出一个异常。

在`addPersonPost`中，如果因为某种原因抛出了异常，那么页面将重新呈现，并传回`data`结构。如果你真的在构建一个页面，那么你可能至少想看看这个异常是什么。

```
import { Request, ResponseToolkit, ResponseObject, ServerRoute } from "@hapi/hapi";

type Person = {
    name: string;
    age: number;
}

const people: Person[] = [
    { name: "Sophie", age: 37 },
    { name: "Dan", age: 42 }
];

async function showPeople(request: Request, h: ResponseToolkit): Promise<ResponseObject> {
    return h.view("people", { people: people });
}

async function addPersonGet(request: Request, h: ResponseToolkit): Promise<ResponseObject> {
    let data = ({} as Person);
    return h.view("addPerson", { person: data });
}

async function addPersonPost(request: Request, h: ResponseToolkit): Promise<ResponseObject> {
    let data = ({} as Person);
    try {
        data = (request.payload as Person);
        people.push(data);
        return h.redirect("/people");
    } catch (err) {
        console.error("Caught error", err);
        return h.view("addPerson", { person: data })
    }
}

export const peopleRoutes: ServerRoute[] = [
  { method: "GET", path: "/people", handler: showPeople },
  { method: "GET", path: "/people/add", handler: addPersonGet },
  { method: "POST", path: "/people/add", handler: addPersonPost }  
];
```

强制性检查:

```
yarn run v1.22.10
$ NODE_ENV=test mocha -r ts-node/register test/**/*.test.ts

  server handles people
    ✓ can see existing people
    ✓ can show 'add person' page
    ✓ can add a person and they show in the list

  3 passing (113ms)

✨  Done in 2.69s.
```

搞定了。

# 向 Joi 确认

我们刚刚添加的内容可以工作，但是它不会对输入进行任何检查——这很容易使服务器崩溃。处理这个问题是 Joi 的职责。

## 设置

设置真的很简单，加包就行！因为它包含了类型定义，所以我们不需要单独的类型包。

```
$ yarn add joi
```

## 添加测试

只是为了确保每个人都在同一页上——积极测试检查如果给定有效的输入，事情会工作。负面测试检查如果输入错误，事物不会崩溃或烧毁。到目前为止，我们所写的只是正面的测试——这些都是好的，但是 bug(或者讨厌的用户)会给你的 API 带来不好的参数。

## 阳性测试

为此，我们将只使用现有的测试，并检查它们是否仍然通过。不过，将`describe`子句修改得更清楚会更清楚:

```
describe("server handles people - positive tests", ...
```

## 阴性测试

这些方法试图通过传入坏数据来破坏 API 没有名字、没有年龄、没有数字年龄等等。我几乎总是发现，当你做完所有的事情时，消极的测试会比积极的多。

```
describe("server handles people - negative tests", async () => {
	let server: Server;

	beforeEach(async () => {
		server = await init();
	})
	afterEach(async () => {
		await server.stop();
	});

	it("can't add a person with no name", async () => {
		let res = await server.inject({
			method: "post",
			url: "/people/add",
			payload: { ...personData, name: null }
		});
		expect(res.statusCode).to.equal(200);
	});

	it("can't add a person with no age", async () => {
		let res = await server.inject({
			method: "post",
			url: "/people/add",
			payload: { ...personData, age: null }
		});
		expect(res.statusCode).to.equal(200);
	});

	it("can't add a person with non-number age", async () => {
		let res = await server.inject({
			method: "post",
			url: "/people/add",
			payload: { ...personData, age: "Watson" }
		});
		expect(res.statusCode).to.equal(200);
	});
})
```

请注意，我们检查状态代码 200，因为在错误的页面应该重新呈现。

## 使用 Joi

要使用 Joi，您需要描述数据的允许形状。使用 Joi 的一个好处是模式描述非常像英语。

```
import Joi from "joi";
const ValidationError = Joi.ValidationError;

const schema = Joi.object({
    name: Joi.string().required(),
    age: Joi.number().required()
});
```

当`addPersonPost`收到新数据时，模式对象可以用来验证它。`stripUnknown`选项删除模式中没有列出的任何元素，这是另一层保护。

```
data = (request.payload as Person);
const o = schema.validate(data, { stripUnknown: true });
if (o.error) {
    throw o.error;
}
data = (o.value as Person);
people.push(data);
```

将下面的代码添加到`catch`子句将处理 Joi 发现的任何错误，并将它们添加到呈现`addPerson`页面的上下文中。

```
const errors: { [key: string]: string } = {};
if (err instanceof ValidationError && err.isJoi) {
    for (const detail of err.details) {
        errors[detail.context!.key!] = detail.message;
    }
} else {
    console.error("error", err, "adding person");
}

return h.view("addPerson", { person: data, errors: errors })
```

`errors`对象被声明为具有引用`string`值(例如`errors["itBroke"] = "yes"`)的`string`键(例如`errors["itBroke"]`)的对象。

然后，我们对异常进行健全性检查；如果它看起来真的像 Joi 异常，那么我们就处理它。当 Joi 引发一个验证异常时，它包含一个`details`元素，这是一个失败细节的数组。我们遍历它，并将每一个添加到`errors`对象中。

最后，我们重新呈现了`addPerson`模板，传入了`errors`对象，以便页面可以处理它。

**NB** : Joi 声明`context`和`key`元素是可选的。不清楚这是为什么——我选择用`!`装饰器覆盖这些元素，告诉 TypeScript 这些元素肯定会出现。

为了向用户报告错误，我们在模板中添加了`span`部分。如果你有一个非常统一的模板，你可以用 Javascript 生成 HTML，但是对于我们这里的小模板，这是最简单的方法。

```
<label for="name">Name</label>
<input type="text" name="name" value="<%= person.name %>">
**<span id="name-error" class="error text-red-500"></span>**
<label for="age">Age</label>
<input type="text" name="age"  value="<%= person.age %>">
**<span id="age-error" class="error text-red-500"></span>**
```

最后，我们将这段 JavaScript 代码添加到页面中:

```
<script>
const errors = <%- JSON.stringify(locals.errors || null) %>;
if (errors) {
  Object.keys(errors).forEach(error => {
    const el = document.getElementById(error + "-error");
    if (el) {
      el.innerText = errors[error];
    }
  })
}
</script>
```

注意`<%-`标签而不是`<%=`——根据 [EJS 文档](https://ejs.co/#docs)，它将一个非转义值输出到模板中。因为我们已经将对象转换为 JSON，所以我们不希望它再次被转义。

如果定义了`errors`,那么我们循环查找匹配的 HTML 元素。如果我们找到了它，然后将它设置为错误消息。这样做可以节省模板中大量复杂的调整，从而有条件地呈现错误消息。

当然，证据在测试中。

```
yarn run v1.22.10
$ NODE_ENV=test mocha -r ts-node/register test/**/*.test.ts

  server greets people
    ✓ says hello world
    ✓ says hello to a person

  smoke test
    ✓ index responds

  server handles people - positive tests
    ✓ can see existing people
    ✓ can show 'add person' page
    ✓ can add a person and they show in the list

  server handles people - negative tests
    ✓ can't add a person with no name
    ✓ can't add a person with no age
    ✓ can't add a person with non-number age

  9 passing (195ms)

✨  Done in 4.51s.
```

我们只是触及了 Joi 的皮毛。我们可以对名称添加更多的限制—例如，它必须超过 1 个字符。我们可以在年龄上增加一些限制，使它只为正数(这将是合理的！)—如果我们这样做了，那么增加一个负年龄、零年龄等等的测试是有意义的。

我希望这是有用的；如果你有任何问题或建议，请告诉我。

所有的代码都可以在这个 Github repo 中找到，如果你想克隆它并玩的话。