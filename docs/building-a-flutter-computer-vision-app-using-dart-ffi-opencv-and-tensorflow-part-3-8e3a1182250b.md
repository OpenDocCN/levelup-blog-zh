# 使用 Dart 构建颤振计算机视觉应用程序:ffi、OpenCV 和 Tensorflow(第 3 部分)

> 原文：<https://levelup.gitconnected.com/building-a-flutter-computer-vision-app-using-dart-ffi-opencv-and-tensorflow-part-3-8e3a1182250b>

## 这是解释如何使用 dart:ffi、OpenCV 和 Tensorflow 在 Flutter 中编写计算机视觉应用程序的三部分系列的最后一部分。

[**第一部分**](https://medium.com/@jeffrey.wolberg/building-a-flutter-computer-vision-app-using-dart-ffi-opencv-and-tensorflow-part-1-513dac9325ab) **讨论了如何正确配置 OpenCV 库和我们的自定义 C++文件以在 Flutter 应用程序中工作，以及如何通过 dart:ffi 从 Dart 访问这些 C++文件。**

[**第二部分**](https://medium.com/@jeffrey.wolberg/building-a-flutter-computer-vision-app-using-dart-ffi-opencv-and-tensorflow-part-2-81472b4ac380) **讨论如何使用 dart:ffi 库使用指针在 Dart 和 C++之间传递数据。这对于将图像数据传输到 C++来说尤其重要，这样它就可以被 OpenCV 操作并返回到 Dart。**

**第三部分讨论了如何使用 tflite_flutter 插件在 Flutter 应用程序中使用 TensorFlow。我们将通过适当的配置，常见的错误，以及如何在我们的设备上运行推理。(即将推出！)**

注意:本教程只包括 iOS 的配置，并且已经在 M1 mac 上测试过。

![](img/f6021a5355d99ea7a8abf5a7ff3af46d.png)

Sudoku Cam，一个用 C++实现的使用 OpenCV 的计算机视觉颤振 app

在[之前的](https://medium.com/@jeffrey.wolberg/building-a-flutter-computer-vision-app-using-dart-ffi-opencv-and-tensorflow-part-1-513dac9325ab) [教程](https://medium.com/@jeffrey.wolberg/building-a-flutter-computer-vision-app-using-dart-ffi-opencv-and-tensorflow-part-2-81472b4ac380)中，我们看到了如何使用 dart:ffi 库在 C++中访问 OpenCV，以及如何在 dart 中来回发送图像数据。我们经常需要使用 OpenCV 对图像进行一些处理，然后将其发送到神经网络，该网络将为我们执行一些任务，如分类。让我们来学习如何使用 **tflite_flutter** 包如何运行设备上的推断！注意:我将使用 *tflite_flutter* 包，而不是 *tflite* 包，因为前者提供了调整列表大小和运行多输入推理的便捷方法。这将在本教程的后面演示。

下面是 tflite_flutter 包的官方文档，它们给出了更全面的概述。

## 库的配置

像 OpenCV 一样，我们必须为它提供一个. framework 文件，这是一个包含库的头文件和静态链接的二进制文件的包:在这种情况下，我们将需要 **TensorFlowLiteC.framework，**你可以通过点击[这个链接](https://github.com/am15h/tflite_flutter_plugin/releases/download/v0.5.0/TensorFlowLiteC.framework.zip)来下载。下载后，将文件粘贴到 **~/中。pub-cache/hosted/pub . dartlang . org/TF lite _ flutter<版本> /ios**

我会推荐尝试导入如下的包来测试你的 app 是否能够访问 tflite_flutter。

```
import 'package:tflite_flutter/tflite_flutter.dart';
```

如果您的应用程序构建成功，那太好了，您可以跳到下一部分。如果出现链接错误，请继续阅读。在某些情况下**。Flutter 用于其包的 pub-cache** 文件夹不在上面提供的位置，而是可以在 Flutter 目录中找到。在这种情况下，导航到 **<颤振方向> /。pub-cache/hosted/pub . dartlang . org/TF lite _ flutter<VERSION>/IOs**并将文件粘贴到那里。正确的位置也可以通过如下符号链接访问: **< PROJECT-DIR > /ios/。symlinks/plugins/TF lite _ flutter/IOs/**

注意，Flutter-DIR 指的是下载 FLUTTER 的目录，而 PROJECT-DIR 指的是编写 FLUTTER 应用的文件夹。

## 在设备上运行推理

因为机器学习任务通常需要大量计算，所以**。tflite** 文件的发明是为了让边缘设备(比如手机)尽可能高效快速地运行推理。您可以通过此处提供的说明[将您的训练模型转换为. tflite 文件。](https://www.tensorflow.org/lite/convert)

一旦你有了这些，让我们在我们的设备上的 Flutter 应用程序中设置我们的模型。

我们可以使用以下函数加载我们的模型:

```
import 'package:tflite_flutter/tflite_flutter.dart';
Interpreter model = await Interpreter.fromAsset("modelName.tflite");
```

确保在 pubspec.yaml 文件的 assets 部分提供模型路径，如下所示:

```
assets:
  - assets/modelName.tflite
```

加载模型后，由您来配置输入数据，以匹配创建模型时指定的尺寸。

例如，让我们假设我们正在对 81 个 34x34 灰度图像(81，34，34，1)进行推理，以从十个可能的类别(81，10)中对每个图像进行分类。

我们的 Dart 代码如下所示:

```
// create a list of 81 empty lists
List<Object> inputs = List<Object>.generate(81, (index) => []); // fill in our pixel values and ensure that they're the right size 
for (int i=0; i<81; i++)
    inputs[i] = images[i].reshape([34, 34, 1]);// initialize our outputs to 0
Map<int, Object> outputs = 
{ 0: List<double>.filled(81 * 10, 0).reshape([81, 10]) };// run our model! The results will be written into 'outputs'
model.runForMultipleInputs(inputs, outputs);
```

如果 TensorFlow 模型预期的尺寸与 Dart 代码输入的尺寸不匹配，请确保尺寸完全匹配。这包括尺寸为 1 的情况(例如(81，34，34， ***1*** ))

Dart 提供了许多方便的列表操作，可用于获得每个输出数组的最大置信度得分和索引。

```
import 'dart:math' as math;List<List<dynamic>> outValues = List<List<dynamic>>.from(output[0]);for (int i=0; i<outValues.length; i++) {
    // obtain maximum value in the currOutput
    List<double> currOutput = List<double>.from(outValues[i]);
    double maxVal = currOutput.reduce(math.max); // obtains the first idx where maxVal occurs
    int maxIdx = currOutput.indexWhere((el) => el == maxVal);
    // MORE CODE HERE...}
```

现在，您可以将自定义的机器学习模型插入到 Flutter 应用程序中，并在设备上运行推理。我们之前学习了如何在 C++中的 OpenCV 中运行图像处理，以及如何在 Dart 和 T2 之间传递数据。本教程总结了流水线的最后一个阶段，即对我们处理过的图像运行设备上的推理。希望你喜欢！