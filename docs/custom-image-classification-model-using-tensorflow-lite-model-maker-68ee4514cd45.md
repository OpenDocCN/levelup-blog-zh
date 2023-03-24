# 使用 TensorFlow Lite Model Maker 定制图像分类模型

> 原文：<https://levelup.gitconnected.com/custom-image-classification-model-using-tensorflow-lite-model-maker-68ee4514cd45>

在本文中，您将学习如何获取自定义图像数据集并训练分类模型。然后，您将把您的模型导出为一个 TFLite 模型，并运行推理。

![](img/11bdb3d5983605b9bfbd8623b8f75f27.png)

由[阿曼达·达尔比约恩](https://unsplash.com/@amandadalbjorn?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/artificial-intelligence?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

对于那些希望涉足深度学习世界的人，或者那些只将机器学习视为达到目的的手段的人，TensorFlow Lite Model Maker 可能正是你正在寻找的。它的设置很快，学习曲线比完全成熟的 TensorFlow 甚至 Keras API 都要平缓得多。然而，对于 Model Maker 提供的简单性，您确实需要进行大量的定制。在本文中，我们将使用 TensorFlow Lite Model Maker 创建一个深度学习模型，该模型可以对 16 种不同的鸟类进行分类。

## 先决条件

[用 Python 为机器学习收集图像数据](https://medium.com/codex/collecting-image-data-for-machine-learning-in-python-6334474d0e23)

## 必需的库

如果尚未安装，请在您的终端上安装必要的库

```
pip install tflite_model_maker
pip install os
pip install numpy
pip install tensorflow 
```

然后导入所需的库

```
import os
import numpy as np
import tensorflow as tf
assert tf.__version__.startswith('2')
from tflite_model_maker import model_spec
from tflite_model_maker import image_classifier
from tflite_model_maker.config import ExportFormat
from tflite_model_maker.config import QuantizationConfig
from tflite_model_maker.image_classifier import DataLoader
import matplotlib.pyplot as plt
```

## 图像数据

定义图像数据的路径。如果您还没有浏览过关于为 Python 中的机器学习收集图像数据的必备文章，那么现在是时候停下来看看了。

```
image_path = '/content/drive/MyDrive/Colab Notebooks/Bird_Classification/BIRDS/train'
```

注意:如果你很细心，你会注意到我的图像路径只指向我的“火车”文件夹。我的前一篇文章，在 Python 中为机器学习收集图像数据，旨在使用 Keras 的 image_dataset_from_directory 函数来组织数据，该函数需要单独的“测试”和“训练”文件夹。Model Maker 将为您创建该分割，因此在本例中，我仅使用我的培训文件夹。

## 列车测试分离

在训练数据和剩余数据之间进行第一次 80/20 分割

```
data = DataLoader.from_folder(image_path)train_data, rest_data = data.split(0.8)
```

将“rest_data”再对半分成“test_data”和“validation_data”

```
validation_data, test_data = rest_data.split(0.5)
```

## 创建模型

现在创建模型。通过:

*   将用于实际训练模型的训练数据
*   验证数据将用于查看您的模型在每个时期后的性能如何
*   时代的数量定义了你想要你的模型经历多少轮训练(时代越多，你的模型训练的时间就越长)
*   模型规范是一种通用的预训练图像模型，您可以将其用作自己数据集的基础。有几个选项可以在准确性和文件大小之间进行不同的权衡。你可以在这里阅读更多关于不同模型的信息:[https://blog . tensor flow . org/2020/03/higher-accuracy-on-vision-models-with-efficient net-lite . html](https://blog.tensorflow.org/2020/03/higher-accuracy-on-vision-models-with-efficientnet-lite.html)

```
model = image_classifier.create(train_data, model_spec=model_spec.get('efficientnet_lite0'), validation_data=validation_data, epochs = 20)
```

## 评估模型

一旦您的模型完成运行，您就可以根据它从未见过的“test_data”对它进行评估。这将让您清楚地了解您的模型是否过度适合训练/验证数据。

```
loss, accuracy = model.evaluate(test_data)
```

## 导出您的模型

一旦您对您的模型的性能感到满意，将您的模型导出到您以后可以再次找到的任何目录。这会将您的模型导出为一个 TFLite 模型。

```
model.export(export_dir='.')
```

## 运行 TFLite 模型

首先，加载你的模型并分配张量

```
import tensorflow.lite as tflite# Load TFLite model and allocate tensors.interpreter = tflite.Interpreter(model_path='Your_Path_Here/model.tflite')#allocate the tensors
interpreter.allocate_tensors()
```

得到输入和输出张量

```
input_details = interpreter.get_input_details()
output_details = interpreter.get_output_details()
```

读入一个你希望模型预测并解码成张量的样本图像

```
import cv2
import numpy as npimage_path='/content/drive/MyDrive/Colab Notebooks/Bird_Classification/BIRDS/validate/Blue Jay/13.jpg'img = cv2.imread(image_path)
img = cv2.resize(img,(300,300))#Preprocess the image to required size and castinput_shape = input_details[0]['shape']
input_tensor= np.array(np.expand_dims(img,0))
```

将张量设置为指向要推理的输入数据，然后运行推理

```
input_index = interpreter.get_input_details()[0]["index"]
interpreter.set_tensor(input_index, input_tensor)
interpreter.invoke()
output_details = interpreter.get_output_details()
```

做出预测

```
output_data = interpreter.get_tensor(output_details[0]['index'])
pred = np.squeeze(output_data)
```

如果您打印“pred ”,您将得到一个 uint 8(0–255)值的数组。这些对应于每个类的置信度。在我的例子中，这是我们在[为 Python 中的机器学习收集图像数据](https://medium.com/codex/collecting-image-data-for-machine-learning-in-python-6334474d0e23)教程中指定的 16 种鸟类。

要获得更人性化的预测，请指定下面的类别索引。

```
class_ind = {
0 :'American Crow',
1:'American Robin',
2: 'Black-crested Titmouse',
3: 'Blue Jay',
4:'Carolina Chickadee',
5: 'Common Grackle',
6: 'Eastern Phoebe',
7: 'House Wren',
8: 'Ladder-backed woodpecker',
9: 'Northern Cardinal',
10: 'Northern Flicker',
11: 'Northern Mockingbird',
12: 'Squirrel',
13: 'Tufted Titmouse',
14: 'White-crowned Sparrow',
15: 'White-winged Dove'}
```

因为“pred”对应于每个鸟类的置信度，所以我们的预测应该是数组中的最高值。抓取最高预测位置。

```
highest_pred_loc = np.argmax(pred)
```

使用“highest_pred_loc”在“class_ind”字典中搜索实际的鸟名。把预测打印出来就大功告成了！

```
bird_name = class_ind[highest_pred_loc]
print(f"It's a wild {bird_name}! So cute.")
```

## 结论

我个人发现 TensorFlow Lite Model Maker 是让你的深度学习模型启动并运行的最简单的方法，值得在准确性和定制化方面稍作权衡。如果你对鸟类不感兴趣，那么[中用 Python](https://medium.com/codex/collecting-image-data-for-machine-learning-in-python-6334474d0e23) 为机器学习收集图像数据的步骤和本文都应该很简单，适合你自己的项目。