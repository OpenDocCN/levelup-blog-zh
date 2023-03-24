# 用 Flutter 编码的技巧

> 原文：<https://levelup.gitconnected.com/tips-for-coding-with-flutter-57c2a9b63f6e>

Flutter 是 Google 创建的开源 UI 软件开发工具包。它用于从单一代码库为 Android、iOS、Linux、Mac、Windows、Google Fuchsia 和 web 开发应用程序。

![](img/8b189a68ac86b71932d139aa34836a3e.png)

# 命名约定

库、包、目录和源文件应该是小写的，单词之间有下划线，如下所示:

`library dart_dynamic_links;
import 'socket/socket_manager.art';`

类、枚举、扩展名和类型定义应以大写字母开头，如下所示:

`class MainSection {...}
enum ItemSelectionTemp {...}
extension MyList<F> on List<F> {...}
typedef Definition<D> = bool function(D value);`

常量、变量、参数和命名参数应以小写字母开头，其他单词则以大写字母开头，如下例所示:

`const magazinePrice = 4.20;
var itemsOfSchools;
final urlPlacement = RegExp(‘^([a-z]+):');
void magSum(int magazinePrice) {...}`

# 指定类成员的类型

当值已知时，总是指定成员的类型是很重要的。您应该尽可能避免使用 var。

`int days = 22;
final vehichle car = vehichle();
String name = 'Kevin';
const int timeUntilEnd = 120;`

# 用户相对导入

为了避免混乱和问题，最好使用相对方法在 lib/ folder 中导入库。

**不要这样做:**

`import 'package:demo/src/utilis/firebase_utilis.dart';`

相反，**这样做**:

`import '../../../utilis/firebase_utilis.dart';`

# 使用？？还有呢？经营者

你应该用？？(如果为空)和？。(空值感知)运算符，而不是条件表达式中的空值检查。

**不要**做这些:

`v = x == null ? y : x;
v = x == null ? null :x.y;`

相反**做**这些:

`v = x ?? y;
v = x?.y;`

# 避免使用“as”运算符

通常，如果无法进行强制转换，则“as”运算符会引发异常，以避免您使用“is”运算符。

**不要这样做:**

`(item as Vehichle).name = 'BMW';`

相反，**做**这个:

`if (item is Vehichle)
item.name = 'BMW';`

# 使用'`if’`'条件代替条件表达式

很多时候，我们需要根据行和列中的某些条件来呈现一个小部件。如果**条件表达式**在任何情况下都返回 null，那么我们应该只使用 If 条件。

**不要这样做:**

```
Widget getText(BuildContext context) {
 return Row(
 children: [
 Text(“Hi”),
 Platform.isAndroid ? Text(“MediumAndroidReader”) : null,
 Platform.isAndroid ? Text(“MediumAndroidReader”) : SizeBox(),
 Platform.isAndroid ? Text(“MediumAndroidReader”) : Container(),
 ]
 );
```

相反，**做**这个:

```
Widget getText(BuildContext context) { 
return Row(
children: 
[
Text(“Hi”), if (Platform.isAndroid) Text(“MediumAndroidReader”) 
] 
);
}
```

# 使用级联运算符

如果您不打算在同一个对象上执行一系列操作，那么您应该使用级联(..)运算符。

```
var street = Street()
..lineTo(0, size.height)
..lineTo(size.width, size.height)
..lineTo(size.width, 0)
..close();
```

# 使用原始字符串

你应该使用原始字符串来避免反斜杠和美元符号。

**不要**这样做:

```
var text = ‘This is a demo string \\ and \$ is not raw’;
```

相反，**做**这个:

```
var text = r'This is a demo string \\ and \$ is raw';
```

# 使用跨页收藏

当现有项已经存储在另一个集合中时，使用扩展集合将简化代码。

**不要这样做:**

```
var a= [4,5,6];
var b= [1,2];
a.addAll(b);
```

相反，**做**这个:

```
var a= [4,5,6];
var b= [1,2,...y];
```

# 不要将变量初始化为空

Dart 中的变量在其值未定义时会自动初始化为 null，因此您不需要这样做。

**不要这样做:**

```
int maxPage = null;
```

相反，**做**这个:

```
int maxPage;
```

# 避免“print()”调用

当你使用' print()'并且日志输出过长时，有时 Android 会丢弃一些日志行。要避免这种情况，请始终使用“debugPrint()”。

# 使用“ListView.builder”获得长列表

如果您正在处理大型或无限列表，通常建议您使用 ListView builder 来提高性能。默认的 ListView 构造函数一次构建整个列表，这可能会很耗时。这就是为什么我们使用 ListView.builder 创建一个惰性列表，当用户向下滚动时加载，Flutter 按需构建小部件。

# 结束语

我希望其中的一些技巧能帮助你写出更简洁的代码，并改进你的应用程序。如果你有任何建议或其他事情要补充，请在下面留下评论。