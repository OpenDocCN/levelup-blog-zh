# 用 Webpacker 和 Jest 在 Rails 中测试 Vue.js

> 原文：<https://levelup.gitconnected.com/testing-vue-js-in-rails-with-webpacker-and-jest-1b307979eb1f>

![](img/6100377b5b6d9660a9f607520195fd98.png)

由 [Ferenc Almasi](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在我正在做的项目中，我的任务是研究如何将 [Vue.js](https://vuejs.org/) 与我们现有的 Rails 应用程序集成。于是我开始看官方指南，看教程，看各种帖子，直到最后得到一个完全可以工作的 Vue 组件。

最后是时候写一些测试了，但是不幸的是，web packer gem 不包括测试配置，所以我不得不自己做。

令我惊讶的是，我发现没有多少关于如何设置的文档。所以我想我应该发这个帖子来和你分享我是如何最终让它工作起来的。

# 1.安装 Jest

我决定用 [Jest](https://facebook.github.io/jest/) 并不是出于个人喜好，我只是注意到 [vue-cli](https://github.com/vuejs/vue-cli) 与它同在，于是就去用它了。

要安装 Jest，你所要做的就是从你的项目根目录运行`yarn add — dev jest`。
然后，向您的`package.json`添加一个测试脚本:

```
{
 “scripts”: {
 “test”: “jest”,
 …
 },
 …
}
```

现在你可以用`yarn test`运行你的测试了。

# 2.定义测试的位置

如果您试图在此时运行`yarn test`，您会看到`config/webpack/test.js`失败了。这是因为 [Jest 在项目](https://facebook.github.io/jest/docs/en/configuration.html#testmatch-array-string)中寻找测试文件的方式。它基本上执行整个项目中所有匹配`.spec.js`或`.test.js`的文件。在这种情况下，文件与`*.test.js`匹配，所以它试图将其作为测试运行。

因为我们不希望 Webpack 配置文件以及项目中满足这个标准的其他文件与我们的测试一起运行，所以我们需要告诉 Jest 在哪里可以找到它们。

在我的例子中，由于我正在使用 [Rspec](http://rspec.info/) ，我决定将它指向`spec/javascripts`目录。但是您可以自由选择最适合您的项目的目录。

为此，您只需将[根](https://facebook.github.io/jest/docs/en/configuration.html#roots-array-string)添加到您的`package.json`中:

```
“jest”: {
 “roots”: [
 “spec/javascript”
 ]
},
```

___
**注:** *如果你的* `*package.json*` *已经相当大了，你又不想继续往里面添加更多的东西，可以通过* `*— config <path/to/js|json>;*` *选项定义 jest 配置。如果你选择这样做，你的* `*package.json*` *现在应该是这样的:*

```
{
 “scripts”: {
 “test”: “jest — config spec/javascript/jest.conf.js”,
 …
 },
 …
}
```

___

为了验证它的工作，您可以通过一个简单的测试创建一个`spec/javascript/team.spec.js`文件，如下所示:

```
test(‘there is no I in team’, () => {
 expect(‘team’).not.toMatch(/I/);
});
```

现在再次运行`yarn test`,您应该会在输出中看到一个绿色的“PASS ”,这意味着它工作了🎉。

# 3.巴别塔来救援了

既然我们的第一个测试已经运行了，让我们更进一步，试着测试一个 Vue 组件。

您可能尝试的第一件事是在`spec/javascript/`目录下创建一个文件，并将其命名为类似于`my_component.spec.js`的名称。然后尝试使用 [import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 语句从您的规范中导入您的组件，如下所示:

```
import MyComponent from ‘../../app/javascript/my_component.vue’;
```

如果您确实尝试过这种方法，请继续运行您的测试。您将在输出中看到一个`SyntaxError: Unexpected token import`。

这里的问题是`import`是 ECMAScript 6 的一部分，所以我们需要像 [Babel](https://babeljs.io/) 这样的 transpiler 的帮助。

要让它工作，你需要通过运行`yarn add — dev babel-jest babel-preset-es2015`安装两个包，并将“es2015”预置添加到你的`.babelrc`文件中:

```
{
 “presets”: [“es2015”,
   [“env”, {
 …
```

如果您想更进一步，您可以将[module directory](https://facebook.github.io/jest/docs/en/configuration.html#moduledirectories-array-string)添加到您的`.package.json`中，这样您就不必键入模块的完整路径:

```
“jest”: {
 …
 “moduleDirectories”: [
   “node_modules”,
   “app/javascript”
 ]
}
```

所以我们之前看到的是

```
import MyComponent from ‘../../app/javascript/my_component.vue’;
```

现在可以写成

```
import MyComponent from ‘my_component.vue’;
```

# 4.Vue 在哪？

如果您遵循了每一步，那么在尝试运行您的测试时，您应该会得到一个`SyntaxError`。这意味着它成功地导入了您的组件，但是它还不能理解`.vue`文件的格式。

幸运的是，我们有一个包可以帮我们解决这个问题。
所以继续运行`yarn add — dev vue-jest`并添加“moduleFileExtensions”、“transform”和“mapCoverage”，如[自述文件](https://github.com/eddyerburgh/vue-jest/blob/master/README.md)中所述。
你的`package.json`应该是这样的:

```
“jest”: {
 …
 “moduleFileExtensions”: [
   “js”,
   “json”,
   “vue”
 ],
 “transform”: {
   “^.+\\.js$”: “<rootDir>/node_modules/babel-jest”,
   “.*\\.(vue)$”: “<rootDir>/node_modules/vue-jest”
 },
 “mapCoverage”: true
}
```

有了 [moduleFileExtensions](https://facebook.github.io/jest/docs/en/configuration.html#modulefileextensions-array-string) 我们在导入单个文件组件时不再需要`.vue`。所以我们之前看到的是

```
import MyComponent from ‘my_component.vue’;
```

现在可以写成

```
import MyComponent from ‘my_component’;
```

你现在应该可以无缝地使用`import`了。

[转换](https://facebook.github.io/jest/docs/en/configuration.html#transform-object-string-string)部分中的规则指出了哪个包负责测试文件的转换。在我们的例子中，我们希望`vue-jest`处理我们所有的`.vue`文件，所以它们在被 Jest 处理之前被转换成普通的 javascript。

[设置 mapCoverage](https://facebook.github.io/jest/docs/en/configuration.html#mapcoverage-boolean) 是为了使用变压器发出的源地图。Jest 将在编写报告和检查阈值时使用它们来尝试和映射原始源代码的代码覆盖率。

最后，让我们添加 Vue 的官方单元测试实用程序库， [vue-test-utils](https://vue-test-utils.vuejs.org/en/) 。只要运行`yarn add — dev @vue/test-utils`就可以了。

您现在可以开始为您的 Vue 组件编写测试了🎉

*原载于 2018 年 1 月 16 日*[*https://dev . to*](https://dev.to/mariiio/testing-vuejs-in-rails-with-webpacker-and-jest-374a)*。*