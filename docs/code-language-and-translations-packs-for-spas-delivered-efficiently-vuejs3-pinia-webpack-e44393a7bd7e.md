# 面向 spa 的语言和翻译包，高效交付:VueJS3、Pinia、Webpack、NodeJS。

> 原文：<https://levelup.gitconnected.com/code-language-and-translations-packs-for-spas-delivered-efficiently-vuejs3-pinia-webpack-e44393a7bd7e>

![](img/e349334ba09dd7397be790e3402393fc.png)

有许多方法可以将翻译字符串传递给 web(也只有 web)应用程序。我将我的偏好缩小到三种最常见的方法，因为我过去在不同的环境中尝试过它们。因此，我对好处和坏处的理解相当不错。尽管如此，我还是想比较并解释一下为什么我选择了最后一个，隐藏的第四个选项。我经历了这个练习，因为我们正在新的 ESGgen 平台中实现**多语言支持，这是一个单页应用(SPA)架构，最终将运行 3 个不同的 web 应用。因此，我必须考虑长远的未来，确保在这个过程中不会牺牲性能等优先事项。这篇文章涵盖了我们如何解决这个问题的理论、比较和实施指南，以及我们未来的计划。那么，我们有什么选择？**

## 将所有内容打包在一个版本中

创建包含所有语言和翻译字符串的发布包。

*   这是一个现成的解决方案。只是工作。
*   对于严重依赖 CMS 或其他外部数据源以正确的语言提供内容的小应用程序来说，这可能是完美的。
*   它需要下载的代码(不同的语言)将永远不会被使用，因为用户很少改变语言。
*   如果有任何字符串需要更新，我们需要经历整个发布周期。
*   由于解析代码所需的更大的大小和额外的 CPU 处理，显著降低了初始应用程序的加载速度。
*   如果没有适当的检查，一个翻译可能会破坏整个应用程序。
*   很可能有一个包含所有翻译字符串的中央 JSON 文件。在一个大的应用程序中，它会变得混乱和不可维护。
*   不同的语言字符串可能会很快失去同步。

## 根据翻译构建

这是对第一个选项的升级，他们不必为了只使用一个字符串而下载所有的翻译字符串。

*   用户只下载他们需要的翻译。
*   更小的初始封装尺寸和更快的加载时间。
*   如果一种语言需要一个补丁，它可以独立部署。
*   由于需要构建的包数量增加，发布部署变得复杂。
*   由于对部署周期的影响，这将增加成本和维护工作量。
*   当您支持超过 5 种语言时，变得不可维护。
*   当你不能自动化所有的事情并且依赖手动测试时，会显著增加测试时间。
*   与以前相同的挑战，集中翻译文件和语言不同步。

## 使用第三方工具

这种方法带来了额外的成本，但确实有效。它对任何本机或混合应用程序都非常适用。这是对前两个选项的改进，因为它可以更好地提供翻译。

*   只允许交付实际需要的东西。
*   如果需要，它可以很容易地与后端集成。
*   通常允许在没有发布的情况下“即时”编辑，这可能是好的也可能是坏的。很大程度上取决于你的公司。
*   经常允许 A/B 测试，如果您热衷于与用户一起运行一些测试，这绝对是一个好处。
*   很容易实现，也很容易被团队使用。
*   通常需要额外的费用。
*   这是一个额外的库，会增加你的包的重量，或者会导致延迟。通常，图书馆很拥挤。
*   对第三方服务产生依赖。如果由于某种原因出错，它可能会使应用程序无用。
*   它在交付方面不是最佳的，可能会导致 UI 呈现或初始启动的延迟(取决于实现)。
*   由于有太多的未知因素，我不提倡这种方法。

## 异步翻译文件从代码结构构建

这是我决定采用的选项，稍后我将解释我们所做的以及它将如何帮助我们在未来进一步发展。

*   我们只向用户提供他们语言所需的内容。该应用程序是语言无关的。
*   包是异步加载的，不会影响核心包的下载和执行。
*   所有翻译都是通过各自的视图、组件或模块文件来定义和定位的。易于分别查找和维护。
*   我们可以即时切换语言，无需重新加载应用程序。
*   Webpack(立即邀请！)& nodeJS 帮助构建语言包。
*   不依赖第三方服务。
*   容易确保语言包没有丢失任何字符串键，并验证代码是否有错误。
*   增加了一些构建配置的复杂性和自动化。

一个好的构建过程应该关注大部分自动化，使开发人员的工作更易于管理，并帮助他们构建更优化的现成代码。我喜欢修补 nodeJS 和 Webpack，并编写小的定制脚本来交付更好的代码。这就是奇迹发生的地方。不幸的是，大多数工程团队忽视了良好构建过程的力量，仅仅使用了 **create-react** 或其他 **vueCLI** ，它们对概念验证很好，但对生产和企业级 spa 却不好。

让我们深入了解一下我们的翻译包。我将向您解释它是如何构建的，我们发现的缺点，以及我们未来改进它的计划。

![](img/2b9d1a80d890020a2770aa0ab2528a58.png)

# SPA 翻译引擎

**我忍不住给它起了一个好听的名字！**这对任何项目都非常重要，因为我们总是希望让它听起来比实际情况更好。但是，不幸的是，“JS 翻译”听起来并不那么令人兴奋。

在接下来的半年里，我们真的不需要支持翻译。只有英语就足够了。与此同时，我知道从硬编码的字符串到存储变量，我们需要花费多少时间和精力来重新编写整个应用程序。因此，最好现在就养成习惯，并在一切相对较小的时候优化装载和包装策略。在这个例子中，有些人会说，“我们不要这样做。没什么优先”。在创业环境中尤其如此。现在就做有助于我们以最小的开销把事情做好，但以后会节省大量成本。

# 未解决的挑战和未来考虑

有一些事情我想为未来考虑，但我决定现在不做任何事情。我们不想过度设计这个小功能，因为我们不能 100%确定以后事情会朝哪个方向发展。理解挑战很重要。知道我们能解决它们是“重要的”；)

## 本地存储翻译

一旦加载了翻译，我们可能希望将它们保存在带有版本号的本地存储中。然后，如果用户再次来到应用程序，版本是正确的，我们可以假设翻译必须是相同的。只有当我们的语言包变得非常大(超过 100KB)时，我才会**考虑这一更新，我们决定将所有翻译作为发布周期的一部分，因此它们都绑定到应用版本。此外，弄清楚用户多久返回一次也很重要，因为如果我们要发布一个新版本(至少一周一次)，实现它是没有意义的。**

## 更智能的翻译包

当我们在 10 个不同的组件中找到一个名为“继续”的按钮时，此时，**完全相同的字符串将在翻译包中重复 10 次**，每次都在各自的名称空间中。我想在最终的翻译文件中实现字符串重复检查。如果它很重要，实现一个过程，将重复的文本合并到一个项目中，并将其放在“共享”名称空间中。这还需要构建过程在 JS 和 VUE 文件中使用新的名称空间引用来更新代码。它只能在构建中完成，而不能在源代码本身中完成。

## 将翻译拆分成多个包

我们最终可能会让用户浏览典型的 5-10 个视图，这将被视为核心体验。我们可以确保将这些翻译打包在一起，并始终首先交付。**其他任何事情都是根据组件/视图需求的异步调用**。这意味着更多的 HTTP 请求，但可以极大地减少初始负载。一旦应用程序上线，我非常希望尝试这种方法，我们将对翻译大小有一个完整的了解。

## 从发布中分离翻译

现在困扰我的一件事是，如果我们只更新文本，我们需要做一个发布。假设我们使用服务器端渲染，解耦的一个重要好处是允许我们在前端和后端使用相同的翻译。我们可以采用两种方法将代码从语言包中分离出来。

*   我们发布的版本将应用程序和翻译结合在一起。在顶部再添加一个发布流程，该流程只允许重新部署翻译。这很容易实现，有助于降低语言更新部署的风险。
*   从代码库中完全删除翻译。这在版本控制、发布时间或潜在的向后兼容性方面带来了一些挑战，只是为了确保先做什么，其他一切都仍然有效。这将开辟其他机会，如 CMS 集成或管理面板用于更新翻译，这意味着我们可以为非工程师建立一个自助服务工具。不幸的是，这种方法可能会很快引入大量的开销以及应用程序和翻译文件之间的错位(简单的混乱)。听起来不太对，但我们不排除这种可能性。

![](img/728ee72bfb646b0e3fe533556838a69e.png)

# 那么它是如何工作的呢？

1.  创建*。组件或视图文件夹中的语言翻译文件。或者，您可以将它放在/lang 子文件夹中，以保持其整洁。
2.  运行 build，这将运行翻译过程。它会收集你所有的*。lang 文件，将它们合并在一起，创建一个对象，并使用 JS 模板保存最终文件。/translations/* . build . js 文件。
3.  构建脚本将解析数据并对其进行 eval()。如果有语法错误，构建将会失败。它还会将任何辅助语言键与主要语言(en)进行比较，并在“键不匹配”时显示警告。
4.  Pinia store 定义了州内系统中可用的语言。简单的数据，如语言代码，名称和图标。仅此而已。
5.  VUE 应用程序有一个名为 update()的 Pinia 操作，它将根据 cookie 参数或默认的“en”来检索语言。然后，它将发出一个异步请求来加载目标文件并填充全局存储。
6.  Pinia store 公开了 getString()方法来简化数据检索。当我们需要在返回之前操作数据时，它还允许我们添加额外的功能。
7.  我们需要导入 Pinia 存储文件，并在组件中使用该方法。仅此而已。
8.  一个叫做语言选择器的额外组件使用翻译配置来显示所有可能的语言。在选择时，它用新语言的参数调用 Pinia update()操作。
9.  一旦文件被检索并存储，整个应用程序就会改变语言。不需要重新加载。

以上几点我们都过一遍，我会分享更多的细节和代码。然后，在你的项目中随意使用它。我重用了我为视图驱动的路由器路径配置编写的大部分代码，所以你可以在我以前的帖子中查看，你可能会决定一箭双雕；)

[](https://blog.devgenius.io/code-views-driven-router-paths-instead-of-a-hardcoded-mess-vuejs-b3f0d257c553) [## 代码:视图驱动的路由器路径，而不是硬编码的混乱——VueJS

### 路由器配置路径是在 index.js 文件中的一个大文件中定义的，所有神奇的事情都发生在这里…

blog.devgenius.io](https://blog.devgenius.io/code-views-driven-router-paths-instead-of-a-hardcoded-mess-vuejs-b3f0d257c553) 

## 第一步:*。组件和视图的 lang 文件。

每个语言文件只包含一个模块、视图或组件的内容，并且它也位于同一个文件夹中。它有一个名为(我称之为名称空间)的对象。组件名称空间以“c”为前缀，而视图以“v”为前缀。只是为了确保以后不会再有任何碰撞，永远不会。

保留所有翻译及其组件的好处是，当添加和删除新内容时，我们不会留下混乱。如果我们不再需要某个特定视图并删除了该文件夹，所有路线和翻译都将消失。它解决了软件团队在同意将这样的信息保存在另一个文件夹(例如。/translations)并假设每个人都会记得在不再需要的时候更新它(它们不会再需要了)。

```
'vHome': {
    'title': 'Welcome to ESGgen',
    'subtitle': 'Unlock the value of ESG in hours not weeks',
    'fieldlLabel': 'Email',
    'fieldPlaceholder': 'Ex: [abc@domain.com](mailto:abc@domain.com)',  
    'loginBtn': 'Get Started'
}
```

## 步骤 2:构建创建翻译包的流程。

这部分会很复杂。我编写了一个简单的 nodeJS 脚本来完成这项工作。它在任何构建之前运行，以确保我们总是拥有最新的翻译集。理想情况下，我希望这个脚本在观察/服务开发模式下运行，但这将在以后添加。这项工作的最终结果是创建**语言/索引。xx . build . js**文件，包含给定语言代码的所有翻译。

**translation.js** -定义语言
-查找所有带扩展名的文件。lang，
-以同样的方式遍历“extra”语言(我应该有一个方法，只传递变量以避免重复)
-然后比较 extraLang 和 primary 中的键

```
let primaryLang = 'en';
let extraLang = ['pl'];// parse primary first
let primaryLangOutput = helpers.findFiles('src/' + APP, primaryLang + '.lang');
helpers.saveResults('src/' + APP + '/languages/index.js', primaryLangOutput, 'LANGUAGE', primaryLang);
const primaryLangObject = eval(Function('"use strict";return {' + primaryLangOutput + '}')());// let's do other languages
for (let i = 0; i < extraLang.length; i++) {
 let currentLang = extraLang[i];let currentLangOutput = helpers.findFiles('src/' + APP, currentLang + '.lang');
 helpers.saveResults('src/' + APP + '/languages/index.js', currentLangOutput, 'LANGUAGE', currentLang);
 let currentLangObject = eval(Function('"use strict";return {' + currentLangOutput + '}')());if (helpers.compareObjectKeys(primaryLangObject, currentLangObject) === false) {
  console.log('\x1b[31m* ERROR: ' + currentLang.toUpperCase() + " Translation keys don't match.\x1b[0m");
  //process.exit();
 }
}
```

**helpers . find files&helpers . save results**

```
/**
 * Recursive function to parse all subfolders from a parent, find specific files and update results array for future parsing
 * [@param](http://twitter.com/param) {string} startPath - root folder path
 * [@param](http://twitter.com/param) {string} filter - an extension of files we looking for (they should contain JS data only)
 * [@param](http://twitter.com/param) {string} [finalOutput] - strinigify content of loaded files
 */
exports.findFiles = function(startPath, filter, finalOutput=''){
 // error handling, just in case
  if (!fs.existsSync(startPath)){
    console.log("\x1b[31m* Directory doesn't exist.\x1b[0m", startPath);
    return;
  }// do the job
  var files=fs.readdirSync(startPath);
  for(var i=0;i<files.length;i++){
    var filename=path.join(startPath,files[i]);
    var stat = fs.lstatSync(filename);if (stat.isDirectory()){
      finalOutput = exports.findFiles(filename, filter, finalOutput); //recurse
    }
    else if (filename.indexOf(filter)>=0) {
      console.log('*',filename);let fileContent = fs.readFileSync(filename, 'utf8');// just in case, check for come at the end as it shouldn't be there.
      if (fileContent.slice(-1) === '}' || fileContent.slice(-1) === ']' ) {
       fileContent += ',\n'
      }finalOutput += fileContent
    };
  };return finalOutput
};/**
 * It will take the given file and update the placeholder with new data
 * Note that it uses .buildTmpl file as a template to build the final file
 * [@param](http://twitter.com/param) {string} destinationFile - the template file used to build the final file
 * [@param](http://twitter.com/param) {string} content - what we want to write in the file
 * [@param](http://twitter.com/param) {string} placeholder - placeholder where new information will be placed in the file
 * [@param](http://twitter.com/param) {string} [suffix] - extra suffix above the default for all built files
 */
exports.saveResults = function (destinationFile, content, placeholder, suffix) {
 if (suffix) { suffix+='.'}
 else { suffix = ''}let extension = destinationFile.split('.').pop();
 let generatedFilename = destinationFile.replace(extension, suffix+PROCESSED_SUFFIX+extension);fs.copyFile(destinationFile, generatedFilename, (err) => {
  if (err) throw err;let fileContent = fs.readFileSync(generatedFilename, 'utf8');
  let finalContent = fileContent.replace(placeholder, content);fs.writeFile(generatedFilename, finalContent, function (err) {
  if (err) throw err;
  });
 });
}
```

## 步骤 3:检查错误和差异

在保存所有内容之前，我想检查我的字符串中是否有错误，最好的方法是解析代码。由于我实际上是在编写对象并将它们组合成一个，所以我可以做一个简单的 eval()来看看它是否如预期的那样工作。这也允许我基于关键字比较翻译，捕捉任何错误或差异，并产生警告(或停止构建)。

这是在初始脚本(translations.js)中完成的。然后，在处理额外文件的最后，我调用 helpers.compageObjectKeys 来解决问题。

**helpers . compareobjectkeys** 这个有一个 bug，因为有时候比较会给出假否定。

```
/**
 * Compare (deep) keys of two objects and tell me if they are same or not
 * [@param](http://twitter.com/param) {object} object1 - the template file used to build the final file
 * [@param](http://twitter.com/param) {object} object2 - what we want to write in the file
 * [@return](http://twitter.com/return) bool
 */
exports.compareObjectKeys = function(object1, object2) {
 let keys1 = Object.keys(object1);
 let keys2 = Object.keys(object2);if (keys1.length !== keys2.length) { // means it is already wrong
  console.log('*', keys1);
  console.log('*', keys2);
  return false;
 }for (const key of keys1) { // loop through all keys to compare them
  let val1 = object1[key];
  let val2 = object2[key];
  let areObjects = isObject(val1) && isObject(val2);if (areObjects && !exports.compareObjectKeys(val1, val2) || !areObjects && val1 !== val2) {
   return false;
  }
 }return true;
}
function isObject(object) {
 return object != null && typeof object === 'object';
}
```

## 步骤 4: Pinia 商店配置

我在 Pinia 中定义了不同的数据存储。其中一个是**语言**，它包含配置和一些动作(在下面的步骤中描述)。商店使用配置数据来检查允许哪些语言。语言选择器组件稍后也使用这些数据来确定在 UI 中显示什么，并在可用选项之间切换。

这就是我目前的语言状态。
**=语言代码一旦成功加载
**字符串{}** =存储我们所有翻译的对象。**

```
state: () => ({
  config: {
   en: {
    name: 'English',
    icon: '...icon link...'
   },
   pl: {
    name: 'Polski',
    icon: '...icon link...'
   }
  },
  currentCode: '',
  string: {}
 })
```

## **步骤 5:加载翻译文件的 Pinia update()操作**

**如果没有提供参数，该方法负责确定要加载的内容。然后，它加载语言包并调用 successResponse()函数，该函数更新存储设置并将所有语言推送到 state.string 对象。**

**由于我们将所有翻译字符串加载到 Pinia Store，并使它们在全球可用，因此我们可以在不重新加载应用程序的情况下动态切换语言。相当棒。**

```
/**
 * Update existing language strings with a new one from the Language Pack (the automated process building all *.lang files)
 * [@memberof](http://twitter.com/memberof) Client/Store/Langugage
 * [@param](http://twitter.com/param) {String} [langCode='en'] new language to be loaded
 */
update(langCode) {
 let newLang = Core.Cookie.superGet('language') || this.config[langCode] || 'en';import(/* webpackMode: "lazy" */ /* webpackChunkName: "translation" */ '[@Client/languages](http://twitter.com/Client/languages)/index.'+newLang+'.built.js')
  .then((response) => {
   successResponse(this, response);
  })
  .catch((response) => {
   // TODO: tracking
  });/**
  * this is just abstraction of the success work to be done after importing a file.
  * [@param](http://twitter.com/param) {Object} state pass-in state
  * [@param](http://twitter.com/param) {Object} response response from import
  */
 function successResponse(state, response) {
  Core.Cookie.superSet('language', newLang);// update state
  state.$patch({
   currentCode: newLang
  });
  state.string = response.default; // this is needed as it's deep object ($patch won't work correctly with it)
 }
},
```

## **步骤 6: Pinia getStrings()方法命名空间字符串**

**只是一个简单的 getter 来根据提供的名称空间检索正确的对象。我认为这可能是一个好主意，以防将来我们需要在返回之前进行一些数据操作。它还允许我调用更深层次的对象，而 JS 不会抱怨某些东西没有定义。**

```
/**
 * Get an object for a given namespace. If not there yet or wrong, it will return {}
 * [@memberof](http://twitter.com/memberof) Client/Store/Langugage
 * [@param](http://twitter.com/param) {string} namespace namespace you set within your *.lang file
 * [@returns](http://twitter.com/returns) object || {}
 */
getStrings(state) {
 return (namespace) => state.string[namespace] || {};
}
```

## **步骤 7:在组件/视图中使用语言字符串。**

**导入您的 Pinia 商店文件(假设您已经正确初始化了它，因为我不会解释如何做)。在我的例子中，我像下面这样导入整个商店，我可以像下面这样访问语言模块。**

```
import * as Store from '@Client/store/index.js'<template>
  {{ Store.language.getStrings('vHome').title }}
</template>
```

## **步骤 8:切换翻译的语言选择器**

**我连接到 store 并使用 Store.language.config，然后遍历定义的选项。这就是我如何知道我可以切换到什么和 onclick，我会调用 Store.langauge.update('XX ')，它会得到快速加载，替换存储和完成！简单。**

**![](img/5953a6b246b143e91b1c4477b20b494b.png)**

# **最后的想法…**

**很容易花费太多的时间，过度设计一些应该简单明了的东西。当我们试图为每一个问题——一个我们期望的问题和一个我们不期望的问题——进行设计时，通常就是这样。我可以相当准确地预测这个小功能将来会发展到哪里，以及我应该实现什么，但是现在还不需要。我真的不认为那是对我时间的最好利用。**

**✅:我知道这会提供我们现在需要的所有功能。✅:我了解这些限制，并有应对计划。✅:它可以更好，可能需要升级，这不会被阻止。**

**让我们等待数据和更广泛的团队投入，然后我开始猜测什么可能是棘手的问题，并尝试现在解决它。**

**现在，去做一些有助于你的团队开发更好的软件的事情吧！我们太经常考虑客户，而对我们自己，工程师考虑得不够。让软件工程生活变得更容易对每个人都有帮助。**

# **编辑—2022 年 2 月 13 日**

**Webpack 已经是过去式了。在经历了一些挑战后，我决定尝试一些不同的东西，最终选择了 Vite。这改变了一切，我不太可能再去 webpack 路由器。我强烈建议尝试一下。我花了两个小时来完成整个转变。**