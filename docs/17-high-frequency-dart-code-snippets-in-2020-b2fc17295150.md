# 2020 年 17 个高频 Dart 代码片段

> 原文：<https://levelup.gitconnected.com/17-high-frequency-dart-code-snippets-in-2020-b2fc17295150>

## 每个 dart 开发人员都应该知道的代码片段。

![](img/e55e3445234b285e1a6d4b72ca0c09ee.png)

由[谷仓图片](https://unsplash.com/@barnimages?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/toolbox?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Dart 是一种客户端优化语言，适用于任何平台上的快速应用程序。

那么，这意味着什么呢？

到目前为止，JavaScript 在客户端技术中占主导地位，但是 dart 最近越来越受欢迎。已经飘起，这是一个跨平台的移动框架，在其早期已经通过了 [github stars](https://github.com/flutter/flutter) 超过 [react-native](https://github.com/facebook/react-native) 。

我用 react-native 开发了 3 年多，最近我尝试了 flutter。我想说的开发体验简直棒极了。

它速度很快，有现成的小部件，调试和故障排除都很方便。

这里我列出了一些用 dart 编写软件时需要了解的有用的代码片段。

## 三元运算符

```
void main(){
 const someThingTrue = false; // try set with true
 someThingTrue? print("its true") : print("its not true");
}
```

> **Const vs Final**:**Const**值必须在*编译时*已知， **final** 值必须在*运行时*已知

## 缺省值

```
void main(){
 const defaultValue = "SomeDefaultValue";
 final someValueNotSureOfItsExistance = null;
 var expectingSomeValue = someValueNotSureOfItsExistance ?? defaultValue;
 print(expectingSomeValue);
}
```

## 零安全

```
class SomeClass{

  void someMethod(){
    print("some method called");
  }}void main(){
 SomeClass someClassObj;
 someClassObj.someMethod(); 
 // this one will crash as someClassObj is null
}
```

下面是处理它的方法

```
class SomeClass{

    void someMethod(){
     print("some method called");
    }
}void main(){
 SomeClass someClassObj;
 someClassObj?.someMethod();// someMethod will call if someClassObj exist, no crash here!
}
```

## 如果存在

```
void main(){
 const someBool = true;

 if(someBool){
   print("its true");
 }}
// print its true!
```

## For 循环

```
for(var i=0;i<1e2;i++){ // instead of i<100, Cool, right?
}
```

## 处理地图

```
void main(){Map<String,String> map = {
  "key1":"value 1",
  "key2":"value 2",
};map?.forEach((key, value) {
 print(key);
 print(value);
});print(map?.values?.length); 
// Double null safety check,so it will never crash!}
```

## 通用字符串模板

```
void main(){ var name = "John",age = 20;
 var someStringConcatenateSomeVariable = 'My Name is ${name} and my  age is ${age}';
 print(someStringConcatenateSomeVariable);}
```

## 多行字符串

```
void main(){var symbole = """
 this is some
 multi line huge
 string length! """;print(symbole);}/*Output be like this is some
multi line huge
string length!*/
```

## 扩展运算符

```
void main(){var values = [1,2,3];
var anotherValues = [...values,4,5,6];
print(anotherValues);}// output be like [1, 2, 3, 4, 5, 6]
```

## 按值映射查找

```
void main(){Map<String,String> map = {
 "key1":"value 1",
 "key2":"value 2",
};print(map?.keys?.firstWhere((element) => map[element] == "value 1",  orElse: ()=>null));}// output will be key1
```

## 按关键字查找地图

```
void main(){Map<String,String> map = { "key1":"value 1",
 "key2":"value 2",};print(map?.keys?.firstWhere((element) => element == "key1", 
orElse: ()=>null));}// Output be like key1
```

## 默认参数值

```
void simpleFunc({int a:1,int b:2}){ print('a= $a and b=$b');}void main() {
 simpleFunc(a: 5); // a is passed as arg but b is taken from default
}// output be like **a= 5 and b=2**
```

## 箭头功能短指针

```
var simpleFunc =(int a,int b) => a+b;void main() {
 print(simpleFunc(2,3));
}// return a+b i.e **5**
```

## charAt()速记

```
void main() {
 print('Hello World'[6]); //return 'W'
}// Returns 'W'
```

## 条件函数调用

```
void func1(){ print("func 1 called");}void func2(){ print("func 2 called");}void main() {
 const someValue = 3;
 (someValue == 4? func1 : func2)();
}// Output be like 'func 2 called'
```

## 字符串到数字

```
void main() { const someValue = "123";
 print(int.parse(someValue));}// Its 123 which is an int
```

## 数字到字符串

```
void main() {
 int a = 123;
 print(a.toString().length);
}
// Its 3 in output
```

# 感谢阅读。🍻