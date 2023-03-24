# 通过与 TypeScript 进行比较来学习 Dart(用于颤振)

> 原文：<https://levelup.gitconnected.com/learn-dart-for-flutter-by-comparing-it-to-typescript-3078ca18fe8f>

## Dart 代码与 TypeScript 代码比较的基本示例

![](img/adb45f0df3fc6927c319bbb11b061657.png)

# 介绍

学习像[飞镖语言](https://en.wikipedia.org/wiki/Dart_(programming_language))(被[扑动](https://en.wikipedia.org/wiki/Flutter_(software))使用)这样的新东西可能会很难。[本指南](https://github.com/jeroenouw/dart-compared-to-typescript)提供了一些例子，这些例子是为从打字背景开始学习 Dart/Flutter 的人准备的，但是反过来也可以。经历这些应该会让你更容易潜入飞镖/扑击。

Dart 语言是强类型的，因此将语法与 TypeScript 进行比较是合适的。Flutter 框架是用 Dart 编写的，用来轻松快速地编写跨平台的原生 app。

下面的例子在 [Github](https://github.com/jeroenouw/dart-compared-to-typescript) 上也有，代码可读性也更强。任何人都可以贡献和添加更多的例子。灵感来源于' [Golang for Node.js 开发者](https://github.com/miguelmota/golang-for-nodejs-developers)'。

# Dart 与 TypeScript 示例的比较

## 包装

```
**[TypeScript]**
package.json // NPM**[Dart]**
pubspec.yaml // Pub
```

## 评论

```
**[TypeScript]**
// single line comment/* 
 multi line comment
*/**[Dart]**
// single line comment// 
// multi line comment
///// documentation comment
```

## 记录

```
**[TypeScript]**
console.log('TypeScript code');**[Dart]**
print('Dart code');
```

## 基本类型

```
**[TypeScript]** const boolType: boolean = true;
const numberType: number = 5;
const stringType: string = 'John';
const arrayType: [] = [];
const anyType: any = 'John'; // Can be anything;
const tupleType: [string, number] = ['John', 5];**[Dart]** final bool boolType = true;
final int numberType = 5;
final double doubleType = 5.5;
final String stringType = 'John';
final List listType = [];
final dynamic dynamicType = 'John'; // Can be anything;
```

## 变量

```
**[TypeScript]**
var a: string = 'a';
let b: string = 'b';
const c: string = 'c';**[Dart]**
var a = 'a';
-
final String c = 'c'; // runtime constant
const c = 'c'; // compile time constant / freezing 
```

## 插入文字

```
**[TypeScript]**
firstName: string = 'John';
lastName: string = 'Doe';console.log(`This is ${firstName} ${lastName})**[Dart]**
final String firstName = 'John';
final String lastName = 'Doe';print('This is $firstName $lastName')
```

## 数组

```
**[TypeScript]** const persons: string[] = ['John', 'William'];**[Dart]** final List<String> persons = ['John', 'William'];
```

## 功能

```
**[TypeScript]** function add(a: number, b: number): number {
  return a + b;
}**[Dart]** int add(int a, int b) {
  return a + b;
}
```

## 异步函数

```
**[TypeScript]** async function add(a: number, b: number): number {
  return a + b;
}**[Dart]** Future<int> add(int a, int b) async {
  return a + b;
}
```

## 班级

```
**[TypeScript]** export class Foo {}**[Dart/Flutter]** class Foo extends StatelessWidget  {}class Foo extends StatefulWidget  {}
```

## 构造器

```
**[TypeScript]** export class Foo {
   constructor() {}  
}**[Dart/Flutter]** class Foo extends StatelessWidget  {
   Foo() {}
}
```

## 感谢您的阅读！任何人都可以在此提供示例[。如果你觉得这篇文章有用，请点击👏关注 button，并考虑阅读我的其他文章:](https://github.com/jeroenouw/dart-compared-to-typescript)

[](/how-to-use-push-notifications-with-flutter-23bed9e47e52) [## 如何在 Flutter 中使用推送通知

### 开始以最简单的方式在本地向您的用户推送通知

levelup.gitconnected.com](/how-to-use-push-notifications-with-flutter-23bed9e47e52) [](https://itnext.io/building-your-first-reusable-widget-with-flutter-cadb54c3c253) [## 用 Flutter 构建第一个可重用的小部件

### 在本指南中，我将向您介绍如何使用 Flutter 和 Dart 构建一个可重用的小部件。

itnext.io](https://itnext.io/building-your-first-reusable-widget-with-flutter-cadb54c3c253) [](/angular-7-share-component-data-with-other-components-1b91d6f0b93f) [## 角度 9-与其他元件共享元件数据

### 使用 Angular 的输入、输出、EventEmitter 和 ViewChild 共享组件数据。

levelup.gitconnected.com](/angular-7-share-component-data-with-other-components-1b91d6f0b93f)