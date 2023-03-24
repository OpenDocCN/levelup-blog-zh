# 使用 Dart 构建颤振计算机视觉应用程序:ffi、OpenCV 和 Tensorflow(第 2 部分)

> 原文：<https://levelup.gitconnected.com/building-a-flutter-computer-vision-app-using-dart-ffi-opencv-and-tensorflow-part-2-81472b4ac380>

## 这是解释如何使用 dart:ffi、OpenCV 和 Tensorflow 在 Flutter 中编写计算机视觉应用程序的三部分系列的第二部分。

[**第一部分**](https://medium.com/@jeffrey.wolberg/building-a-flutter-computer-vision-app-using-dart-ffi-opencv-and-tensorflow-part-1-513dac9325ab) **讨论了如何正确配置 OpenCV 库和我们的自定义 C++文件以在 Flutter 应用程序中工作，以及如何通过 dart:ffi 从 Dart 访问这些 C++文件。**

第二部分讨论了如何使用 dart:ffi 库通过指针在 Dart 和 C++之间传递数据。这对于将图像数据传输到 C++来说尤其重要，这样它就可以被 OpenCV 操作并返回到 Dart。

[**第三部分**](https://medium.com/@jeffrey.wolberg/building-a-flutter-computer-vision-app-using-dart-ffi-opencv-and-tensorflow-part-3-8e3a1182250b) **讨论如何使用 tflite_flutter 插件在 Flutter 应用程序中使用 tensorflow。我们将通过适当的配置，常见的错误，以及如何在我们的设备上运行推理。**

![](img/f6021a5355d99ea7a8abf5a7ff3af46d.png)

Sudoku Cam，一个用 C++实现的使用 OpenCV 的计算机视觉颤振 app

## 在 C++之间传递图像数据

有两种主要方法可以做到这一点:

1.  使用 OpenCV 的 **imwrite** 和 **imread** 函数读写图像数据。
2.  在我们的 Dart 和 C++文件之间传递一个指针，直接从内存中访问我们的字节。

让我们复习一下如何做到这两点！

## 使用 OpenCV 的 imwrite 和 imread 函数

使用这种方法相当简单，也很容易实现。我们将把图像路径传递给 C++并使用 OpenCV 的 **imread** 函数将它读入图像。

在 Flutter 中，我们可以访问用户照片库中的图像，在这种情况下，我们可以使用图像路径，或者我们只能访问图像的像素数据。在后一种情况下，我们必须将图像保存到一个临时位置，以便在 C++中访问它。让我们展示一个如何做到这一点的快速示例。

```
import ‘package:path_provider/path_provider.dart’;Uint8List imgBytes = YOUR_IMAGE_BYTES; // pixel data
XFile img = XFile.fromData(imgBytes); 
Directory dir = await getApplicationDocumentsDirectory();
String dirName = dir.path;
String imgPath = dirName + "/tempImg.jpg";
img.saveTo(imgPath);
```

我们通过 **path_provider** 包提供的**getApplicationDocumentsDirectory**，方法获取保存文件的目录。XFile 提供了一种使用 **saveTo** 方法将图像写入文件的便捷方式，因此我将把我的图像像素转换成一个 XFile，尽管还有其他方式将图像保存到文件中。

注意:我假设你的像素数据由无符号的 8 位整数表示，这相当于 C++中的数据类型**无符号字符**。我将在整个教程中继续做这个假设。如果您的图像以不同的格式保存，请相应地修改我的示例代码。

当我们调用 C++函数时，我们将传入 imgPath 作为参数，并使用 OpenCV 的 **imread** 函数。

```
returnType func(char *path, ...) {
    cv::Mat image = cv::imread(path);
```

现在我们的图像可以在 C++中访问，我们可以将 OpenCV 用于我们的计算机视觉逻辑。

在用 OpenCV 操作之后，我们经常想要在 Dart 中访问结果图像。我们可以传入另一个参数 outputImgPath，它将是保存输出图像的路径，或者我们可以覆盖原始图像路径。无论哪种方式，这都很容易做到:

```
cv::imwrite(outputImgPath, outputImg);
```

回到我们的颤振代码，我们可以通过线访问图像

```
Image img = Image.file(outputImgPath);
```

非常容易！

**cv::imwrite** 将我们正在写入磁盘的图像编码成 outputImgPath 中指定的格式(例如。jpg，。png)。在大多数情况下，这是预期的行为，但是如果我们想要访问原始和未压缩的像素数据，我们不能以这种方式保存图像。这就是我们必须通过 dart:ffi 使用指针指向原始像素数据的地方。

## 使用 dart:ffi 和指针在 C++之间传递数据

我们将利用 dart:ffi 库调用本机 C APIs 来读/写和分配/释放本机内存。具体来说，在我们的 dart 代码中，我们将在堆上分配特定数量的字节，然后将这个指针传递给 C++。在 C++中，我们要么访问由 Dart 填充的输入图像像素数据，要么用输出图像像素数据填充这些值。

如果我们知道图像的尺寸，这就容易多了。如果图像大小是可变的，这仍然是可能的，但是稍微复杂一些。处理可变大小的图像将在本教程的后面部分讨论。现在，让我们假设我们知道输入和输出图像的尺寸，它是 500x500。为了简单起见，我们还假设它是灰度图像。因此，输入字节总数为 500 x 500 x 1 = 250，000。如果我们有一个 RGB 图像，我们将分配三倍的内存。

在我们的 dart 文件中，确保`import dart:ffi;`

我们必须在本机堆上分配内存。在 Dart 中，我们必须写

```
Pointer<Uint8> imgPtr = malloc.allocate<Uint8>(500*500*1);
```

**Uint8** 是 dart:ffi 在 C++中写 **unsigned char** 的方式。

记住，在我们使用完 imgPtr 指向的数据后，我们必须使用以下方法释放内存:

```
malloc.free(imgPtr);
```

如果我们计划将我们的图像像素数据传递到我们的 C++函数中，我们可以编写以下代码行，将我们的图像数据复制到我们刚刚分配的内存中，然后这些数据将被传递到 C++中:

```
Uint8List imgBytes = YOUR_IMAGE_BYTES; // pixel data
imgPtr.asTypedList(imgBytes.length).setAll(0, imgBytes);
```

这个 **imgPtr** 通过如下签名传递给我们的 C++函数:

```
returnType func(..., unsigned char *imagePtr, ...) { 
```

如果我们用实际的像素值填充 imagePtr，我们现在可以通过解引用这个 imagePtr 来访问它们。或者，如果我们只是在 Dart 中分配内存，以便在 C++中填充它，我们可以执行以下操作:

```
for (int i=0; i < 500*500*1; i++)     imagePtr[i] = img.data[i];     // img is of type cv::Mat
```

**img.data** 是指向输出图像像素数据的指针，我们只是将它所指向的值复制到我们分配的内存块中。

在我们的 Dart 代码中，在我们的图像处理完成后，我们可以创建一个由该内存支持的列表。

```
Uint8List imgPixels = imgPtr.asTypedList(500*500*1);
```

现在 imgPixels 让我们可以访问从 C++中保存的所有像素数据。我们可以展示它，将它输入到机器学习模型中，等等！

注意:使用 **Image.memory(byteData)** 从内存中显示图像的标准 Flutter 方法期望传入的参数保存表示。jpg 或者。图像的 png 格式。如果你给它原始输入像素数据，它将无法显示你的图像。如果是这种情况，尝试使用 OpenCV 的 **imencode** 方法将原始像素数据编码成编码字节，然后可以在 Flutter 中成功传入 **Image.memory** 。

## 我们写点代码吧！

我们可以用一个例子来演示如何做到这一点:在一个 C++函数中对原始图像数据进行编码，该函数将使用一个指针读入我们的 RGB 输入数据，并通过另一个指针保存我们的编码输出数据。

在 C++中，我们可以编写函数 **encodeIm** ，返回编码图像的长度(总像素数):

```
// uchar is a typedef provided by OpenCV for 'unsigned char'int encodeIm(int h, int w, uchar *rawBytes, uchar **encodedOutput) {
    cv::Mat img = cv::Mat(h, w, CV_8UC3, rawBytes);
    vector<uchar> buf;
    cv:imencode(".jpg", img, buf); // save output into buf
    *encodedOutput = (unsigned char *) malloc(buf.size());
    for (int i=0; i < buf.size(); i++)
        (*encodedOutput)[i] = buf[i];
    return (int) buf.size();
```

注意 encodedOutput 是一个 uchar**(指向 uchar 的指针的指针)。因为我们无法在 Dart 中预先知道编码输出将占用的字节数，所以我们在 Dart 中分配了 8 个字节，并保存在 C++中动态分配的指针。这个 C++指针指向一个可变的内存量，这取决于我们的原始图像由于编码而缩小了多少。这样我们就不会给程序分配任何不必要的内存。

在我们的 Dart 代码中:

```
Uint8List bytes = YOUR_RAW_IMAGE_BYTES; // raw pixel data;// allocate memory on the heap for our input data
Pointer<Uint8> bytesPtr = malloc.allocate(bytes.length);// Allocate just 8 bytes to store a pointer that will be malloced in C++ that points to our variably sized encoded image
Pointer<Pointer<Uint8>> encodedImPtr = malloc.allocate(8);// copy our input data onto the heap
bytesPtr.asTypedList(bytes.length).setAll(0, bytes); // Encode our input image and access the outputted pixel data
int encodedImgLen = **_encodeIm**(h, w, bytesPtr, encodedImPtr);// Access the ptr malloced in C++ and save the data that it is     // pointing to
Pointer<Uint8> cppPointer = encodedOutputPtr.elementAt(0).value;
Uint8List encodedImBytes = cppPointer.asTypedList(encodedImgLen);Image myImg = Image.memory(encodedImBytes);// Free the memory that we allocated after we are finished with it
malloc.free(bytesPtr);
malloc.free(cppPointer);
malloc.free(encodedImPtr); // always frees 8 bytes
```

注意:上面加粗的函数 **_encodeIm** 是使用本教程第 1 部分末尾描述的方法加载到 Dart 中的。

对于如何做到这一点的复习，下面是以下代码:

```
// declare _encodeIm for initialization later
late int Function(int height, int width, Pointer<Uint8> bytes, Pointer<Pointer<Uint8>> encodedOutput) _encodeIm;final DynamicLibrary _dylib = Platform.isAndroid ? DynamicLibrary.open(‘libnative_add.so’) : DynamicLibrary.process();_encodeIm = _dylib.lookup<NativeFunction<Int32 Function(Int32 height, Int32 width, Pointer<Uint8> bytes, Pointer<Pointer<Uint8>> encodedOutput)>>(‘encodeIm’).asFunction();
```

太好了！我们已经学会了如何将包含我们的计算机视觉逻辑的 C++函数与我们的 Dart 代码连接起来，从而允许我们将我们的计算机视觉逻辑(C++)与我们的用户界面逻辑(Dart)分隔开来。我们已经学习了如何使用 OpenCV 的 imread 和 imwrite 方法以及通过指针来传入和检索图像数据。

在[下一部分](https://medium.com/@jeffrey.wolberg/building-a-flutter-computer-vision-app-using-dart-ffi-opencv-and-tensorflow-part-3-8e3a1182250b)中，我们将学习如何使用 Flutter 包 **tflite_flutter 将 C++返回的数据馈入机器学习模型。**我们将讨论正确的配置、常见的错误和其他有趣的细节。留下来。