# 用 golang 和 regexp 更新文件内容

> 原文：<https://levelup.gitconnected.com/update-file-content-with-golang-and-regexp-2f031891f713>

最近，我在我们的一个项目中更改了 i18n 解决方案，我需要更改数百个文件，我尝试手动更新文件，但这太耗时了。所以我考虑写一个程序自动更新文件。

下面的项目是我写的更新文件，我添加了一些演示文件来演示这个程序做什么。演示文件在`demodata`文件夹中，在这个文件夹中，我放了一些演示文件，包括`java`、`kotlin`、`objc`和`swift`文件，演示如何将文件从`StringProvider`更新到`StringVaues`。

[](https://github.com/swanwish/fileupdater) [## GitHub - swanwish/fileupdater:一个用 golang 写的工具，可以用 regexp 和…

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/swanwish/fileupdater) 

# 问题

在这个演示中，以 java 文件为例，原始内容如下:

```
String value1 = StringProvider.getValue("key1", "default value 1");
String value2 = StringProvider.getValue("key2", "default value 2");
String value3 = isAdd ? StringProvider.getValue("key3", "default value 3") : StringProvider.getValue("key4","default value 4");
String value4 = StringProvider.getValue("key5",
                "default value 5");
```

上面的代码调用 StringProvider 的方法根据一个键获取值，我想改成另一个类，这个类的变量名为 key name，比如`StringValues.key1`。源代码有多种格式，有的逗号后面没有空格，有的多行。我需要搜索它们并用新方法替换它们。

# 解决办法

我创建的项目，它可以注册更新规则，应用程序将使用规则搜索文本，并根据规则替换文本。

## 更新规则

更新规则可以配置文件扩展名、正则表达式模式和匹配替换符。

更新规则的结构如下所示:

```
type UpdateRule struct {
 Exts             []string
 Pattern          string
 GetMatchReplacer func(match []string) *string
}
```

应用程序将搜索规则中配置了扩展名的文件，并搜索规则中带有模式的文本，模式是 regexp 模式，`GetMatchReplacer`有匹配参数，从 regexp 中获取，并将替换字符串返回给应用程序，如果返回值为 nil，则文本不会被替换，当返回值不为 nil 时，它将被用作目标。

## 列出目录中的文件

List file 将在 search dir 中搜索文件，它将检查文件的扩展名，添加返回匹配的文件，这个函数是一个递归函数，如果文件夹中的项是 dir，那么将用 sub dir 再次调用这个函数。下面的代码是函数的实现。

```
func (updater fileUpdater) listFiles(searchDir string) []string {
 items, err := ioutil.ReadDir(searchDir)
 if err != nil {
  logs.Errorf("Failed to get items from dir %s, the error is %#v", searchDir, err)
  return nil
 }files := make([]string, 0)
 for _, item := range items {
  itemName := item.Name()
  subDir := filepath.Join(searchDir, itemName)
  if item.IsDir() {
   subFiles := updater.listFiles(subDir)
   if err != nil {
    logs.Errorf("Failed to get sub file, the error is %#v", err)
    return nil
   }
   files = append(files, subFiles...)
  } else {
   fileExt := path.Ext(itemName)
   for _, ext := range updater.fileExts {
    if fileExt == ext {
     files = append(files, filepath.Join(searchDir, itemName))
    }
   }
  }
 }
 return files
}
```

## 使用正则表达式过滤字符串

我使用 regexp 来过滤需要替换的字符串，我使用函数`func (re *Regexp) FindAllStringSubmatch(s string, n int) [][]string`来查找字符串，下面是对函数的描述。

> // FindAllStringSubmatch 是 FindStringSubmatch 的“All”版本；它
> //返回表达式所有连续匹配的一部分，如
> //包注释中的‘All’描述所定义。
> //返回值 nil 表示不匹配。

我从这个项目中学到了一些东西:

java 文件的模式示例如下:

```
"StringProvider\\.getValue\\(\"(.*?)\",\\s*?\"(.*?)\"\\)"
```

根据此规则，下面的文本将返回三个项目。

```
String value1 = StringProvider.getValue("key1", "default value 1");
```

`FindAllStringSubmatch`的结果有三项，第一项是`StringProvider.getValue("key1", "default value 1")`，第二项是`key1`，最后一项是`default value 1`。

在模式中，我使用`\\s*?`来匹配术语之间的空白，占位符`(.*?)`我在末尾添加了一个`?`，这不会使用贪婪模式，所以如果我有两个匹配项，它将返回两个项。