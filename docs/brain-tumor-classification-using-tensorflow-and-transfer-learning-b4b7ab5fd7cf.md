# 基于张量流和迁移学习的脑肿瘤分类

> 原文：<https://levelup.gitconnected.com/brain-tumor-classification-using-tensorflow-and-transfer-learning-b4b7ab5fd7cf>

![](img/d8b0cb7550e19724986d44868c4a51cf.png)

来源:Freepik.com

我使用机器学习技术对四种不同类型的脑瘤进行分类；无肿瘤、神经胶质瘤、脑膜瘤和垂体瘤。所以，我想到了写一篇差不多的文章。

我将这篇文章分为两个不同的部分，在第一部分我们将建立一个机器学习模型来对脑瘤(你正在阅读的那个)进行分类，在[第二部分](https://medium.com/gitconnected/machine-learning-in-production-a036567e9c86)我将谈论如何将你的机器学习模型投入生产。

> [第二部分:生产中的机器学习](https://medium.com/gitconnected/machine-learning-in-production-a036567e9c86)

我们今天要讲的要点是:

*   什么是脑瘤及其类型
*   数据准备
*   什么是迁移学习？
*   不同层的定义
*   训练模型
*   通过训练好的模型进行预测
*   评估模型
*   保存模型以备将来使用。

所以，事不宜迟，让我们开始吧。

# 脑瘤是什么？

脑瘤只不过是你大脑中异常细胞的聚集或生长。它被认为是儿童和成人中最具侵袭性的疾病之一。检测脑瘤的最佳技术是磁共振成像(MRI)。

通过扫描产生了大量的图像数据。这些图像由放射科医生检查。由于脑肿瘤及其特性的复杂性，手动检查容易出错。作为一名机器学习爱好者，我能想到的减少人为干预和人为错误的最佳方式是使用机器学习技术自动化这一分类过程。

说够了，让我们开始实际工作吧！

我们将使用 [**Kaggle**](https://www.kaggle.com/datasets/sartajbhuvaji/brain-tumor-classification-mri) **提供的数据集。**可以下载或者使用 Kaggle 笔记本进行分类。我用过 Kaggle 笔记本，会在 [**GitHub**](https://github.com/thejitenpatel/brainTumorDetection) **上分享给大家。**

如题所示，我们将使用 Tensorflow 库进行分类任务。除此之外，对于数据分析和可视化，我们将使用其他一些库，如 pandas、numpy、seaborn、matplotlilb 和 Scikit-learn。现在，让我们导入所有的库。

```
import matplotlib.pyplot as plt
import numpy as np
import seaborn as sns
import cv2
import tensorflow as tf
from tensorflow.keras.preprocessing.image import ImageDataGenerator
from tqdm import tqdm
import os
from sklearn.utils import shuffle
from sklearn.model_selection import train_test_split
from tensorflow.keras.applications import EfficientNetB0
from tensorflow.keras.callbacks import EarlyStopping, ReduceLROnPlateau, TensorBoard, ModelCheckpoint
from sklearn.metrics import classification_report,confusion_matrix
import ipywidgets as widgets
import io
from PIL import Image
from IPython.display import display,clear_output
from warnings import filterwarnings
for dirname, _, filenames in os.walk('/kaggle/input'):
    for filename in filenames:
        print(os.path.join(dirname, filename))
```

让我们定义一些颜色，这将有助于我们的可视化。

```
colors_dark = ["#1F1F1F", "#313131", '#636363', '#AEAEAE', '#DADADA']
colors_red = ["#331313", "#582626", '#9E1717', '#D35151', '#E9B4B4']
colors_green = ['#01411C','#4B6F44','#4F7942','#74C365','#D0F0C0']

sns.palplot(colors_dark)
sns.palplot(colors_green)
sns.palplot(colors_red)
```

# 数据准备

我们将检测四种不同类型的脑瘤。所以，像下面这样声明一个名为 label 的变量。

```
labels = ['glioma_tumor','no_tumor','meningioma_tumor','pituitary_tumor']
```

我们将分割训练集以创建分类模型。Kaggle 提供的测试集用于将 ML 模型测试到产品中。如果你现在还不明白，不要担心，继续读这篇文章，你会明白的:)

```
X_train = []
y_train = []
image_size = 150
for i in labels:
    folderPath = os.path.join('../input/brain-tumor-classification-mri','Training',i)
    for j in tqdm(os.listdir(folderPath)):
        img = cv2.imread(os.path.join(folderPath,j))
        img = cv2.resize(img,(image_size, image_size))
        X_train.append(img)
        y_train.append(i)

for i in labels:
    folderPath = os.path.join('../input/brain-tumor-classification-mri','Testing',i)
    for j in tqdm(os.listdir(folderPath)):
        img = cv2.imread(os.path.join(folderPath,j))
        img = cv2.resize(img,(image_size,image_size))
        X_train.append(img)
        y_train.append(i)

X_train = np.array(X_train)
y_train = np.array(y_train)
```

在上面的代码中，我们遍历了训练和测试目录，并在调整图像大小后追加到 python 列表中。请注意，我们将图像的大小调整为 150x150。

现在，让我们为每个标签绘制一些图像。

```
k=0
fig, ax = plt.subplots(1,4,figsize=(20,20))
fig.text(s='Sample Image From Each Label',size=18,fontweight='bold',
             fontname='monospace',color=colors_dark[1],y=0.62,x=0.4,alpha=0.8)
for i in labels:
    j=0
    while True :
        if y_train[j]==i:
            ax[k].imshow(X_train[j])
            ax[k].set_title(y_train[j])
            ax[k].axis('off')
            k+=1
            break
        j+=1
```

我们将洗牌使用 python 的*洗牌*方法创建的 **x_train** 和 **y_train** ，并查看形状。

```
X_train, y_train = shuffle(X_train,y_train, random_state=101)
X_train.shape
```

此外，我们将把数据集分成训练集和测试集。测试集是训练集的 **10%** 。

```
X_train,X_test,y_train,y_test = train_test_split(X_train,y_train, test_size=0.1,random_state=101)
```

我们将利用 sci-kit learn*train _ test _ split*函数将数据集一分为二。 **test_size** 参数帮助我们设置测试集的大小(0.1 = 10%)。

我们都知道计算机只理解数值。我们之前声明过的标签是一串列表。无论如何，我们必须将这些标签转换成数值，以便我们可以将这些标签输入到机器学习模型中。将标签转换成数值的最佳解决方案是使用一种著名的技术 **One Hot Encoding。**

```
y_train_new = []
for i in y_train:
    y_train_new.append(labels.index(i))
y_train = y_train_new
y_train = tf.keras.utils.to_categorical(y_train)

y_test_new = []
for i in y_test:
    y_test_new.append(labels.index(i))
y_test = y_test_new
y_test = tf.keras.utils.to_categorical(y_test)
```

解释**一个热编码**超出了本文的范围，但是它所做的是为每个标签分配二进制值。上面的代码帮助我们做同样的事情。

唷！😮‍💨

很多工作😵

但是现在，我们必须做实际的工作:使用迁移学习建立一个分类模型

# 什么是迁移学习？

深度卷积神经网络模型可能需要几天甚至几个月的时间在非常大的数据集上进行训练。简化这一过程的一个简单方法是重用为标准计算机视觉基准数据集(如 ImageNet 图像识别任务)开发的预训练模型的模型权重。简单地说，迁移学习就是在一个新问题上重新使用一个预先训练好的模型。它还建议当我们有一个小数据集时使用迁移学习。

表现最好的模型可以下载并使用，或者集成到一个新的模型中，以解决您的计算机视觉问题。我们将使用 **EfficientNetB0** 模型，该模型将使用来自 **ImageNet** 数据集的权重。

```
effnet = EfficientNetB0(weights='imagenet',include_top=False,input_shape=(image_size,image_size,3))
```

上面一行代码将帮助我们重用权重。将 **include_top** 参数设置为 *False* ，这样网络就不包括来自预建模型的**顶层/输出**层，这允许我们根据我们的使用案例添加我们的输出层！

现在，我们将把我们的定制层添加到 **EfficientNetB0** 模型中。

```
model = effnet.output
model = tf.keras.layers.GlobalAveragePooling2D()(model)
model = tf.keras.layers.Dropout(rate=0.5)(model)
model = tf.keras.layers.Dense(4,activation='softmax')(model)
model = tf.keras.models.Model(inputs=effnet.input, outputs = model)
```

我们使用了几层，每层的解释如下:

**GlobalAveragePooling2D:** 这一层与 CNN 中的 Max Pooling 层类似，唯一的区别是它使用平均值而不是 Max 值，而 *pooling* 。这将有助于我们在训练时减少机器的计算负荷。

**Dropout:** Dropout 是一种在训练过程中忽略随机选择的神经元的技术。他们是被随机“淘汰”的。这有助于避免过度拟合。

**密集:**一个简单的神经网络层，也是我们的输出层，它将图像分类为 4 个可能类别中的一个。请注意，在上面的代码中，我们将 4 作为密集层中的第一个参数，这意味着我们必须输出 4 种不同的可能性(类)。

我们还在密集层中使用了 *Softmax* 函数，该函数输出预测值的概率。

```
model.compile(loss='categorical_crossentropy',optimizer = 'Adam', metrics= ['accuracy'])
```

我们最终编译了我们的模型。

```
checkpoint = ModelCheckpoint("effnet.h5",monitor="val_accuracy",save_best_only=True,mode="auto",verbose=1)
reduce_lr = ReduceLROnPlateau(monitor = 'val_accuracy', factor = 0.3, patience = 2, min_delta = 0.001, mode='auto',verbose=1)
```

**回调:**回调可以帮助你更快的修复 bug，可以帮助你构建更好的模型。他们可以帮助你可视化你的模型的训练进展，甚至可以通过在每次迭代中实现提前停止或定制学习速率来帮助防止**过度适应**。

# 培训模式

```
history = model.fit(X_train,y_train,validation_split=0.1, epochs =12, verbose=1, batch_size=32, callbacks=[tensorboard,checkpoint,reduce_lr])
```

上面的代码将帮助您训练模型。如果你使用 **CPU** 来训练模型，那么训练模型将花费近一两个小时，但是如果你使用 **GPU** ，那么只需要几分钟。

```
filterwarnings('ignore')

epochs = [i for i in range(12)]
fig, ax = plt.subplots(1,2,figsize=(14,7))
train_acc = history.history['accuracy']
train_loss = history.history['loss']
val_acc = history.history['val_accuracy']
val_loss = history.history['val_loss']

fig.text(s='Epochs vs. Training and Validation Accuracy/Loss',size=18,fontweight='bold',
             fontname='monospace',color=colors_dark[1],y=1,x=0.28,alpha=0.8)

sns.despine()
ax[0].plot(epochs, train_acc, marker='o',markerfacecolor=colors_green[2],color=colors_green[3],
           label = 'Training Accuracy')
ax[0].plot(epochs, val_acc, marker='o',markerfacecolor=colors_red[2],color=colors_red[3],
           label = 'Validation Accuracy')
ax[0].legend(frameon=False)
ax[0].set_xlabel('Epochs')
ax[0].set_ylabel('Accuracy')

sns.despine()
ax[1].plot(epochs, train_loss, marker='o',markerfacecolor=colors_green[2],color=colors_green[3],
           label ='Training Loss')
ax[1].plot(epochs, val_loss, marker='o',markerfacecolor=colors_red[2],color=colors_red[3],
           label = 'Validation Loss')
ax[1].legend(frameon=False)
ax[1].set_xlabel('Epochs')
ax[1].set_ylabel('Training & Validation Loss')

fig.show()
```

在模型被训练之后，在每个**时期可视化你的模型性能是很好的。它帮助我们确定我们的模型在每个时期的表现，以及我们何时可以停止训练等等。衡量模型性能的最佳方式是使用**线图。****

上面的代码绘制了训练和验证准确性/损失的线形图。

# 预言；预测；预告

```
pred = model.predict(X_test)
pred = np.argmax(pred,axis=1)
y_test_new = np.argmax(y_test,axis=1)
```

我们将使用 *argmax* 函数，因为预测数组中的每一行都包含相应标签的四个值。每行中的**最大值**描述了 4 种可能结果中的预测输出。

因此，使用 *argmax* ，我们将能够找出与预测结果相关的指数。

# 估价

在训练模型并进行预测后，我们大多数人(大多数是新手)直接在生产中使用该模型，因为我们大多数人只关心准确性，这是衡量我们模型性能的一种方式，但还有其他不同的方式来衡量**模型性能**。

通过评估我们的模型，我们可以深入了解模型的性能。对模型的评估有助于我们理解不同的矩阵(性能测量)。

```
print(classification_report(y_test_new,pred))
```

sci-kit learn 的*分类 _ 报告*方法将帮助我们找出不同的性能度量，如**精度、召回率、f1-分数、支持度、准确度、宏观平均值等。**

我们不容易理解数字和文本，但是对数字和文本的可视化有助于我们更好地理解。上面的代码会给你一个不同数字的报告。

让我们想象一下。

```
fig,ax=plt.subplots(1,1,figsize=(14,7))
sns.heatmap(confusion_matrix(y_test_new,pred),ax=ax,xticklabels=labels,yticklabels=labels,annot=True,
           cmap=colors_green[::-1],alpha=0.7,linewidths=2,linecolor=colors_dark[3])
fig.text(s='Heatmap of the Confusion Matrix',size=18,fontweight='bold',
             fontname='monospace',color=colors_dark[1],y=0.92,x=0.28,alpha=0.8)

plt.show()
```

我们将绘制混淆矩阵的**热图**，这反过来有助于我们直观地总结模型性能。

# 保存我们的模型

```
model.save('modelv1.h5')
```

保存 Tensflow 模型有不同的方法，但我将模型保存为 **.h5 格式。**这是因为这是保存模型最简单的方法，加载模型也更容易。

# 结论

我们学习了迁移学习，并将其应用于现实世界的项目中。上面的代码将帮助你达到 98%的准确率。我们也了解脑瘤。

在接下来的文章中，也就是本文的第 2 部分，我们将使用这个模型来构建 web 应用程序，并学习如何在生产中使用我们的 ML 模型。

在那之前，我希望你喜欢我的文章。你可以在这篇文章中找到必要的链接，下面是我为这个项目编写的 [**Github**](https://github.com/thejitenpatel/brainTumorDetection) 代码。我希望我对你有所帮助。

我从我以前的文章中得到了如此多的爱。如果您有任何疑问、建议、问题或任何事情，可以随时在[**LinkedIn**](https://www.linkedin.com/in/thejitenpatel/)**[**Twitter**](https://twitter.com/thejitenpatel)**[**insta gram**](https://www.instagram.com/thejitenpatel/)**上联系我。******

****一个很小的请求，如果你有任何建议或任何事情，请填写下表，这将有助于我写你希望我写的技术和主题。谢谢:)****

******快乐编码😉******

****https://forms.gle/1zB9GiKAdejyNbkh6****

****[](https://github.com/thejitenpatel/brainTumorDetection) [## GitHub-jaten Patel/脑瘤检测

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/thejitenpatel/brainTumorDetection) [](https://thejitenpatel.medium.com/) [## Jiten Patel -培养基

### 阅读吉滕·帕特尔在媒介上的作品。机器学习工程。|扑开发者|写手。每天，吉滕·帕特尔和…

thejitenpatel.medium.com](https://thejitenpatel.medium.com/)****