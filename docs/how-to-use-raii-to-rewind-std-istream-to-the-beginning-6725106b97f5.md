# 如何使用 RAII 保持代码简洁明了

> 原文：<https://levelup.gitconnected.com/how-to-use-raii-to-rewind-std-istream-to-the-beginning-6725106b97f5>

![](img/598752719bead9f2e6237234a1c0e484.png)

我最近一直在研究 [OpenVINO](https://github.com/openvinotoolkit/openvino) 的一个功能，并提出了一个我认为值得分享的解决方案。它使用了一个叫做 RAII 的 C++特性，这个小技巧帮助我避免了必须实现的过度复杂的代码。

# 几句开场白

OpenVINO 支持许多类型的 DL 模型，但是在内部使用自己的表示，我们简单地称之为 IR。IR 通常由一个叫做模型优化器的“离线工具”产生，输出是一个 XML 文件，包含 OpenVINO 理解的模型表示。要将此模型加载到内存中，用户需要在其应用程序中使用以下 API:

```
InferenceEngine core;
const auto network = core.ReadNetwork("model.xml");
```

从 OpenVINO 的 2020.4 版本开始，您可以使用相同的 API 直接加载 ONNX 模型，而无需使用模型优化器。

```
InferenceEngine core;
const auto network = core.ReadNetwork("model.onnx");
```

ONNX 模型只是序列化的 protobuf 文件。虽然它们可能相当大(高达 2GB ),但在我们开始整体解析它们之前，我们希望快速浏览一下用户提供的文件，并判断它是否看起来像是给定类型的正确模型。换句话说，我们在开始时进行快速检查，如果文件看起来可疑，就从 ReadNetwork 方法提前返回。顶层代码如下所示:

```
CNNNetwork ReadNetwork(const std::string& model_path) {
    std::ifstream model(model_path); for (auto& reader : registered_readers) {
        if (reader.supportsModel(model)) {
            return reader.ReadNetwork(model);
        }
    } throw std::runtime_error{"None of the registered readers can 
                              parse the provided file"};
}
```

阅读器 API 看起来像这样:

```
bool IReader::supportsModel(std::istream& model) const;
CNNNetwork IReader::ReadNetwork(std::istream& model);
```

# 使用流

每个读者都应该对一个流进行操作，这个流下面可能是一个文件或一个已经加载到内存中的模型。但是对于如何使用流没有限制。最有问题的情况是当一个读取器从它的`supportsModel()`方法返回 false，并且没有“倒带”到流的开始。在这种情况下，存在另一个读者从文件中的无效位置开始验证模型的风险。

事实上，这正是我在第一版代码中犯的错误。我在输入文件中跳来跳去，发现模型不是 ONNX 格式，并从 ONNX 阅读器实现返回 false。之后，IR 读取器引用了表示模型文件的流，并返回 false。这样我就无法导入任何模型，大量的测试开始失败。这是它开始时的样子:

要修复它，我只需要在我的代码中增加一些对`model.seekg(0, model.beg)`的调用。但是…整个`supportsModel()`方法已经相当复杂了，它有很多分支，并且依赖于返回语句中的另一个函数。不过，我必须确保在所有情况下流都被重置。

这是混乱的、重复的，并且使得该方法看起来更加复杂。即使我减少了对`seekg()`方法的调用次数，并将它移到 return 语句之前，它仍然不理想:

# RAII 来救援了

那么在 C++中我们能做些什么来确保函数返回时发生一些事情呢？我们是否也能确保无论如何都会发生？我指的是定期返回还是抛出异常？事实证明我们可以，如果你曾经使用过互斥体，你应该熟悉 std::lock_guard。每当执行退出一个代码块时，它就解锁一个互斥体。

这正是我使用的方法，除了我不必释放任何资源(这是 RAII 的主要用途)。我创建了一个小小的帮助器结构，它保存了一个对流的引用，并在其析构函数中调用`stream.seekg(0, stream.beg)`。就这么简单。

有了这个小小的帮助结构，我可以回到我的原始代码版本，只需在开头添加一行代码。RAII 和栈展开的美妙之处在于，即使在`is_valid_model`的某个地方抛出异常，也能确保流被重置。

# 附言

我知道`StreamRewinder`不是一个完美的名字，但是命名和缓存失效不是编程中最困难的两件事吗？:)

感谢您的阅读，祝您周末愉快！