# 不到 15 行代码中的多个人脸识别模型

> 原文：<https://levelup.gitconnected.com/multiple-face-recognition-models-under-15-lines-of-code-e505e89a97ae>

以脸书的“DeepFace”或谷歌的“OpenFace”为例——它们都是开源的，预测也非常准确。事实上，这些算法中的一些应该比人更准确！在这篇短文中，我们将首先了解人脸检测和人脸识别之间的区别，然后继续编写用于人脸识别的 python 代码。

![](img/fd72f262097d03b8ca331eb07b3b00b5.png)

萨曼莎·亨托什在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# **人脸检测 vs 人脸识别**

人脸检测和人脸识别在业内可以互换使用，但它们之间有很大的区别。
*人脸检测* —这是一种确定静止图像或视频帧中人脸的存在和位置的机制。

简单来说，人脸检测是人脸识别技术的一个子集。

# **让我们开始编码**

只需不到 15 行代码，你就可以使用各种算法——deep Face、OpenFace、FaceNet、VGG-Face 来识别两幅图像是否属于同一个人。为此，我们将使用一个名为“DeepFace”的库。

```
!pip install DeepFace
from deepface import DeepFacesearch_photo_path = "/Users/shirishgupta/Documents/DeepFace/images/"def verification(img1, img2, model_name = "VGG-Face", 
distance_metric = "cosine"):
    start = time.time()    
    result = DeepFace.verify(search_photo_path + img1,search_photo_path + img2, model_name = model_name,
                             distance_metric = distance_metric)
    print(result) 
    demography = DeepFace.analyze(search_photo_path + img1,['age', 'gender', 'Emotion'])
    print("Age :", demography["age"])
    print("Gender :", demography["gender"])
    print("Emotion :", demography["emotion"])
    end = time.time()
    print("Total Time Taken :", end - start)
```

如果你仔细阅读代码，它不仅会'*验证*两幅图像是否相同，还会使用'*分析*功能来识别*年龄、性别、种族、情感*。哇哦。

> 默认模型是“VGG 面”，但你也可以使用“开放面”、“面网”或“深度面”
> 。同样，对于距离度量，默认是“余弦”相似度，但你也可以使用“欧几里德”或“欧几里德 _l2”

# 让我们测试一些样品

# ***同一个人***

![](img/7d1c0f5d3a6bc0d4f5f8b42e2dfb849f.png)![](img/78fd53e9749eba5c023bef1fdc25edc8.png)

比较索菲亚·维加拉(又名《摩登家庭》中的格洛丽亚)的两张照片

```
**Call the verification function**
verification(img1 = "Gloria1.jpg", img2 = "Gloria2.jpg")**Result** 
{‘verified’: **True**, ‘distance’: 0.29478079080581665, ‘max_threshold_to_verify’: 0.4, ‘model’: ‘VGG-Face’, ‘similarity_metric’: ‘cosine’}
Age : 30.826963232265545
Gender : **Woman**
Emotion : {‘angry’: 1.1257877896953225e-10, ‘disgust’: 2.3604164951090527e-28, ‘fear’: 2.051567015188407e-17, ‘happy’: 99.99970793722319, ‘sad’: 3.787624262327966e-12, ‘surprise’: 6.746326677509912e-08, ‘**neutral**’: 0.0002937411129556654}
Total Time Taken : 22.704721927642822
```

# 不同的人

![](img/fa1fabbbc6a09db5d6adfdddb4c27df7.png)![](img/22f74dfafca4c2b679674c408ec6f7f1.png)

比较索菲亚·维加拉(又名《摩登家庭》中的格洛丽亚)和朱丽·鲍温(又名《摩登家庭》中的克莱尔)

```
**Call the verification function**
verification(img1 = "Gloria2.jpg", img2 = "Claire1.jpg")**Result**
{‘verified’: **False**, ‘distance’: 0.760310709476471, ‘max_threshold_to_verify’: 0.4, ‘model’: ‘VGG-Face’, ‘similarity_metric’: ‘cosine’}
Age : 24.55772955064261
Gender : **Woman**
Emotion : {‘angry’: 0.028805792680941522, ‘disgust’: 4.2523261356564035e-06, ‘fear’: 5.214745178818703, ‘happy’: 0.01625357399461791, ‘sad’: 0.5649986676871777, ‘surprise’: 0.09074355475604534, ‘**neutral**’: 94.0844476222992}
Total Time Taken : 24.77853012084961
```

显然，在这两种情况下，算法都能够验证照片是否属于同一个人。

# 结束语

我没有深究这背后的数学，而是专注于实际应用。这篇文章的灵感来自于 Sefik Ilkin Serengil*的一篇文章。希望你喜欢这篇短文！*