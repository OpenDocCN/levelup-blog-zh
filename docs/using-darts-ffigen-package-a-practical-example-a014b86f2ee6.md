# 使用 Dart 的 ffigen 包—一个实际例子

> 原文：<https://levelup.gitconnected.com/using-darts-ffigen-package-a-practical-example-a014b86f2ee6>

我维护了一个名为 [mraa](https://pub.dev/packages/mraa) 的 Dart 包，这是一个使用 Dart 的 [FFI](https://dart.dev/guides/libraries/c-interop) 机制的[英特尔 MRAA Linux](https://iotdk.intel.com/docs/master/mraa/index.html) 库的实现。这个包现在已经有 3 年的历史了，它是通过手工制作 FFI 绑定来实现的，这些 FFI 绑定是 Dart 到 MRAA 头文件中提供的 MRAA C API 所需要的。这已经足够好了，但是不容易维护，MRAA API 中的任何变化都必须检查，并且相应地更新软件包，这既耗时又容易出错，需要一个更好的长期解决方案。

幸运的是，Dart 现在有了 [ffigen](https://pub.dev/packages/ffigen) 包。这个包扫描一个或多个基于 C 的 API 头文件，并自动创建一个带有已经生成的必要 FFI 绑定的 Dart 类文件。因为这个类是自动生成的，所以 MRAA API 中的任何更改都会被自动拾取，不需要任何手动检查。这对维护来说是一个很大的好处，所以我决定从这个生成的文件中采用 mraa 包所需的 FFI 绑定。

然而，在这样做时，有几个需要考虑的事项

1.  不得以任何方式接触生成的文件，即不应应用手动编辑，节省时间删除手动 FFI 绑定代码是没有意义的，因为每次重新生成生成的文件时都必须应用手动编辑。
2.  必须保持包的现有 API 结构，即通过相应的命名 Dart 类访问 MRAA API 的不同部分，例如像这样访问 GPIO 函数“mraa . GPIO . initialize…”。这允许更好的功能命名空间，我们不希望一个 API 提供给用户的函数可能有 100 个，就像生成的文件一样长。
3.  mraa 包的公共 API 必须保持最小的变化，我们不希望为升级 mraa 的用户做不必要的工作，因此 ffigen 生成的任何类型都必须移植到现有的包类型命名结构中。

本质上，我们需要用生成的类的代码替换现有的手工制作的 FFI 绑定代码，并且影响最小。

一种方法是使用 C++世界中常见的方法，称为 [Pimpl 习语](https://en.cppreference.com/w/cpp/language/pimpl)。这种技术本质上是通过将类的实现细节放在一个单独的类中，并通过一个不透明的指针来访问，从而将类的实现细节从其对象表示中移除。对于模式爱好者来说，它是桥模式的变体，即隐藏实现的一种方式，主要是为了打破编译依赖，而完整的桥模式是支持多种实现的一种方式，尽管 C++中的 Pimpl 习语在设计模式正式化之前已经存在了很多年。

## Ffigen 'd Pimpl 类

让我们从生成我们的 ffigen d 类开始，称为“pimpl”类，这不是一篇关于 ffigen 或如何使用它的文章，请参见它的文档，所以下面是我的 ffigen_pubspec.yaml 文件的亮点:-

```
figen:  
  output: 'mraa_impl.dart'  
  name: 'MraaImpl'  
  description: 'Holds ffigen generated implementation bindings to MRAA.'  
  headers:  
    entry-points:  
      - 'mraa/mraa.h'  
    include-directives:  
      - '**mraa/*.h'  
  comments: true  
  preamble: |
```

因此，我们正在生成一个名为 mraa_impl.dart 的文件，该文件包含一个名为 MraaImpl 的类，该类来自一个单独的 mraa.h 头文件，该头文件本身包含每个 API 区域的头文件(这是 C 语言中的标准做法)，我们保留了注释，并为该类添加了一个序言，用于许可等。ffigen 处理这个生成没有问题，生成的类可以在这里看到[。](https://github.com/shamblett/mraa/blob/master/lib/src/implementation/mraa_impl.dart)

现在让我们看看如何更新 mraa 包来使用这个类。

## 主要 mraa 类别更新

从主 mraa 类开始，我们可以看到原始结构依赖于底层的 mraa 共享库，我们现在需要删除它，使 mraa 类依赖于 pimpl 类，所以:-

```
Mraa() {  
  _lib = DynamicLibrary.open('libmraa.so');  
}
```

变成:-

```
Mraa() {  
  _impl = mraaimpl.MraaImpl(DynamicLibrary.open('libmraa.so'));  
}
```

我们补充说:-

```
// The MRAA Implementation class  
late mraaimpl.MraaImpl _impl;
```

现在，单个 API 的构造已经从:-

```
common = MraaCommon(_lib, noJsonLoading, useGrovePi);
```

致:-

```
common = MraaCommon(_impl, noJsonLoading, useGrovePi);
```

将 mraa 共享库的依赖项替换到我们的 pimpl 类中，注意没有影响公共 API 的变化。

## API 类更新

我们现在需要更新我们的单个 API 类，以通用 API 为例，构造更改为:-

```
MraaCommon(this._impl, this._noJsonLoading, this._useGrovePi) {  
  // Set up the pin offset for grove pi usage.  
  if (_useGrovePi) {  
    _grovePiPinOffset = Mraa.grovePiPinOffset;  
  }  
}  

// The MRAA implementation  
final mraaimpl.MraaImpl _impl;
```

也就是说，我们现在使用我们的 pimpl 类，查看初始化方法，我们看到它已经从:-

```
MraaReturnCode initialise() => returnCode.fromInt(_initFunc());
```

到

```
MraaReturnCode initialise() => MraaReturnCode.
returnCode(_impl.mraa_init());
```

注意，返回类型没有改变，所以对公共 API 没有影响。底层 C 调用现在通过 pimpl 类调用，而不是通过我们手工制作的 FFI 绑定调用共享库，这允许我们删除原始类中存在的数百行代码，行数现在是 316 而不是 658。还要注意返回代码的派生，这是因为我们也升级了包的枚举，稍后会详细介绍。

现在让我们看一个传递参数并返回类型的方法:-

```
String platformVersion(int platformOffset) {  
  final ptr = _platformVersionFunc(platformOffset);  
  if (ptr != nullptr) {  
    ptr.toDartString();  
  }  
  return 'Platform Version is unavailable';  
}
```

成为

```
String platformVersion(int platformOffset) {  
  final ptr = _impl.mraa_get_platform_version(platformOffset);  
  if (ptr != nullptr) {  
    return ptr.cast<ffi.Utf8>().toDartString();  
  }  
  return 'Platform Version is unavailable';  
}
```

请再次注意，公共方法签名没有改变，指针处理有一点重新编码，但是这是可以预料到的，因为我们已经从现有的类中删除了对它的任何支持。这些更新简单直观，更重要的是在我们的公共方法中。

所有其他 API 类中的所有其他方法都进行了相应的更新，与节省的维护时间相比，这花费的时间非常少。

## 公共类型更新

该包使用了许多公开的类型，我们不想更改这些类型的名称，所以我们不想直接从 pilpl 类中使用这些类型，而是想将 pilpl 类中使用的名称映射到现有的名称，让我们看一些例子。

许多 API 调用采用“context”参数，这是一种不透明的类型，它被初始化，然后根据需要传递给 API 方法，这在基于 C 的 API 中是常见的做法，一个例子是 GPIO 类上下文类型，它被定义为:-

```
class MraaGpioContext extends Opaque {}
```

不透明的 FFI 类型，我们现在不能使用它，我们必须使用皮条客类提供的定义，所以现在变成:-

```
typedef MraaGpioContext = mraaimpl.mraa_gpio_context;
```

pimpl 类中的 mraa_gpio_context 本身就是一个 typedef，所以我们现在用 typedef 创建一个 typedef(整洁),重要的是保留我们现有的类型名。然而，这确实对公共 API 产生了破坏性的影响，在 pimpl 类中声明的 typedef 是一个指针，

```
ffi.Pointer<_gpio>; 
```

因为现有的类是一个不透明的类型，所以没有成员可以访问，这没问题，但是任何声明这个的用户，

```
Pointer<MraaGpioContext>
```

现在将看到破损，因为我们的新 typedef 已经是一个指针，所以这个用法现在需要改为“MraaGpioContext”。这是一个简化的变化，因为这些类型只初始化一次，不应该导致太多的更新，我相信这是可以接受的。

同样，我们也必须用皮条客类中的类型替换我们现有的手工类类型，以 MraaGpioEvent 类为例，我们改变了原来的:-

```
class MraaGpioEvent extends Struct {  
  /// Construction  
  MraaGpioEvent(int id, int timestamp) {  
    id = id;  
    timestamp = timestamp;  
  }  

  @Int32()  

  /// Id  
  external int id;  

  @Int64()  

  /// Timestamp  
  external int timestamp;  
}
```

到

```
typedef MraaGpioEvent = mraaimpl.mraa_gpio_event;
```

也就是说，我们再次使用来自 pimpl 类的定义，在这种情况下，mraa_gpio_event 是一个类，而不是一个 typedef，所以我们仍然可以使用来自单元测试套件的声明，例如:-

```
final events = <MraaGpioEvent>[];
```

并且仍然访问类成员，因此在不改变任何现有代码的情况下大大简化了我们的代码。

其余的类型都按照这些思路进行了适当的更新。这就留下了另一个需要解决的大问题，枚举。

## 公共枚举更新

MRAA 等封装的 C API 声明包含许多选择电压阈值、引脚方向、频率等的配置值。由各种 C API 函数使用。以 pin 方向为例，我们可以看到 MRAA GPIO C 报头包含以下内容:-

```
typedef enum {  
    MRAA_GPIO_OUT = 0,      /**< Output. A Mode can also be set */  
    MRAA_GPIO_IN = 1,       /**< Input */
    .....
```

还有其他方法，在某些情况下使用直接定义指令。这表示为:-

```
abstract class mraa_gpio_dir_t {  
  /// < Output. A Mode can also be set  
  static const int MRAA_GPIO_OUT = 0;  

  /// < Input  
  static const int MRAA_GPIO_IN = 1;
...
```

在由 ffigen 编写的皮条客类中，我们现在需要将这些值合并到我们的 mraa 包中。

最初，该包使用标准的 Dart 枚举，其中不能为单个枚举提供值，提供了支持映射表和访问函数来将 Dart 枚举转换为实际值，这些都是手工制作的，容易出错并且维护起来很繁琐。你可以在上面的 API 类更新部分看到一个例子，参见“fromInt”帮助函数。

现在，随着 Dart 中增强枚举的出现，我们可以大大简化这一过程，并大大减少维护工作。MraaGpioDirection 枚举现在变成:-

```
enum MraaGpioDirection {  
  /// Out  
  out(mraaimpl.mraa_gpio_dir_t.MRAA_GPIO_OUT),  

  /// In  
  inn(mraaimpl.mraa_gpio_dir_t.MRAA_GPIO_IN),
  ....
```

随着代码的减少，即使有一些覆盖和帮助方法，当然我们的公共枚举名称也没有改变，单个枚举的名称也没有改变，我们不再关心这些值是什么，它们只是从 pimpl 类中选取的。

这里的维护节省不能被低估，必须以某种方式检查这些值在 MRAA 的不同版本之间没有发生变化是一项非常累人的工作，希望最好的结果并不是一个可行的替代方案。

注意上面列举的' inn '，这应该是' in '作为一个方向，但是我们不能有' in '，它的语法高亮用' in '不能作为一个标识符，因为它是一个关键字。。我想，不可能拥有一切！

## 结果

那么，我们是否按照我们上面陈述的考虑实现了包的更新，也就是说，我们对用户的影响很小或者没有影响吗？快速查看的一个方法是检查我们找到公共 API 用户的文件，在这个包中是单元测试套件和示例的目录。

我们在 mraa_uart_details.dart 示例中看到一个更新:-

```
'${returnCode.asString(ret)}');
```

成为

```
'$ret)');
```

新的 enum 实现带来的简化。

在整个单元测试套件中，我们在 mraa_common_test.dart 中只有一个影响:-

```
mraa.common.resultPrint(returnCode.asInt(MraaReturnCode.success));  
mraa.common  
    .resultPrint(returnCode.asInt(MraaReturnCode.errorInvalidHandle));
```

成为

```
mraa.common.resultPrint(MraaReturnCode.success.code);  
mraa.common.resultPrint(MraaReturnCode.errorInvalidHandle.code);
```

再次简化，不需要对测试套件进行其他更新，测试套件运行完全没有错误。

总的来说，我认为这是一个值得考虑的胜利。在其他项目中使用这个包可能会产生一些其他的不一致，但是绝对不对用户产生任何影响将是奇迹，我们正在努力实现“最小化到没有变化”，而不是一点变化都没有。

上面的考虑 1 和 2 似乎完全符合。

## 结论

如果你正在使用 FFI 和一个基于 C 的 API 做一些严肃的工作，ffigen 是一个不错的选择，我发现它简单易用，直观，代码生成完整，不需要手工修饰，没有惊喜，而且工作正常。

但是我建议，虽然你可以，但你不应该直接向你的用户公开你的 ffigen 'd 类，把它包在另一个 Dart 层下面，从长远来看这是值得的。

祝你快乐！