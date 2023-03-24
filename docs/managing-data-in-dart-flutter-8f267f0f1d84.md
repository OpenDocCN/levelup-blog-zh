# 在 Dart/Flutter 中管理数据

> 原文：<https://levelup.gitconnected.com/managing-data-in-dart-flutter-8f267f0f1d84>

## 在 Dart/Flutter 中管理数据的一些技巧

![](img/a35318f791df2af78d74b4a14783657a.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

StackOverflow 中有许多关于在 Flutter 中管理数据的问题，我在这里向您展示我是如何做的。让我们开始吧。

# 飞镖/颤动中的数据

## JSON

```
{
  "name": "hello",
  "address": "world"
}
```

我们的大部分数据都是从 JSON 格式的 API 中获取的。简单、简短、精确。在 Dart/Flutter 中，当我们在一个 HTTP 调用中从一个 API 获取数据时，它会将主体作为一个字符串返回给我们。然后我们使用`jsonEncode`将其转换为`Dynamic`。有两个我们将持续使用的基本功能是:`jsonEncode`和`jsonDecode`。Javascript 中的`JSON.parse`和`JSON.stringify`也是如此。然后，你会得到`Dynamic`或者你显式地把它转换成`Map`。

```
dynamic data1 = jsonDecode('{"name":"hello", "address":"world"}');
print(data1['name']); // helloMap<String, dynamic> data2 = jsonDecode('{"name":"hello", "address":"world"}');
print(data2['name']); // hello
```

## 动态/地图

```
Map<string, dynamic> data; // Simple Map
Map<dynamic, dynamic> data; // Complex Map
```

> 键/值对的集合，从中可以使用相关的键检索值。映射中的键的数量是有限的，并且每个键都有一个与之相关联的值。

`dynamic`几乎等同于 Javascript 中的`any`，而`Map`是一个对象。

## 类的实例

```
class MyClass{
  String name;
  String address;MyClass(this.name, this.address);
}MyClass myclass = MyClass('hello', 'world');
print(myclass); // Instance of 'MyClass'
print(myclass.name); // hello
```

和其他语言的课没什么区别。当然，您可以将从 API 返回的数据转换到您的类中。我们可以**使用`JSON Serializer`将你的** `**map**` **或者** `**dynamic**` **转换成你的**类。

## 目录

很多时候我们的 API 会返回 JSON 中的对象列表。在我们类的帮助下，我们现在也可以把它转换成一个列表。

## 给你的班级加电

```
class MyClass {
  String name;
  String address;
  MyClass(this.name, this.address); factory MyClass.fromJson(Map<String, dynamic> json) {
    return MyClass(
      json['name'] as String, 
      json['address'] as String);
  }Map<String, dynamic> toJson() => {
        'name': name,
        'address': address,
      };
}
```

这基本上就是管理数据所需的全部内容。

> **API 对类的响应**

```
final response = await http.get(url, headers: await setHeaders());
dynamic parsed = jsonDecode(response.body);
final MyClass myclass = MyClass.fromJson(parsed);
```

> **API 主体的类别**

```
final response = await http.put(url,
body: jsonEncode(MyClass.toJson()));
```

> 将 JSON 中的对象列表转换为您的类

```
List<MyClass> = List<MyClass>.from(
parsed['data'].map((i) => MyClass.fromJson(i)));
```

这应该足以轻松管理您的数据。下面是一个完整的例子:[镖靶](https://dartpad.dev/bad33f5201fd6f110871e8e73740e03c)

## JSON 可序列化

JSON Serializable 是一个帮助你将 JSON 转换成类的包，反之亦然。将它包含在 dev_dependencies 下的`pubspec.yaml`中，因为生产中不需要它。确保您也有 [build_runner](https://pub.dev/packages/build_runner) 设置。

```
dev_dependencies:
  flutter_test:
  sdk: flutter
  build_runner: ^1.7.4
  json_serializable: ^3.2.5 // add this
```

> 您的类文件名在这里很重要！

假设您的类文件名是`myclass.dart`，导入`json_annotation`和一个零件文件，我们很快就会生成这个文件。包括一个工厂和一个函数，代码如下:

```
import 'package:json_annotation/json_annotation.dart';
part 'myclass.g.dart'; // this file will be generated, or you can create the function too@JsonSerializable(nullable: false)
class MyClass{
  String name;
  String address;MyClass(this.name, this.address);
  factory MyClass.fromJson(Map<String, dynamic> json) => _$MyClassFromJson(json);
  Map<String, dynamic> toJson() => _$MyClassToJson(*this*);
}
```

在项目中运行该命令

```
flutter pub run build_runner build --delete-conflicting-outputs
```

上面的命令将基于您的类生成一个包含两个函数的文件，因此您不需要解析/映射每个类。

```
// myclass.g.dart file
part of 'myclass.dart';MyClass _$MyClassFromJson(Map<String, dynamic> json) {
  return MyClass(
    json['name'] as String,
    json['address'] as String
  );
}Map<String, dynamic> _$MyClassToJson(MyClass instance) => <String, dynamic>{
   'name': instance.token,
   'address': instance.address
};
```

我希望我分享了有用的知识给任何需要的人。在新冠肺炎的这段时间，请大家注意安全。