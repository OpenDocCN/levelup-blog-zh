# sci kit-学习备忘单(2022)，用于数据科学的 Python

> 原文：<https://levelup.gitconnected.com/scikit-learn-cheat-sheet-2022-python-for-data-science-61c0a2e8517b>

## 初学者学习 Scikit 的绝对基础——2022 年学习

![](img/5195f3ad25fdd2879e91539e0b231c18.png)

来自 Unsplash 的照片由 [Tim Stief](https://unsplash.com/photos/YFFGkE3y4F8) 拍摄

Scikit-learn 是 Python 编程语言的免费软件机器学习库。它具有各种分类、回归、聚类算法，以及用于数据挖掘和数据分析的高效工具。它构建在 NumPy、SciPy 和 Matplotlib 之上。

> ***章节:***1。[基本示例](#4e6d)
> 2。[加载数据](#cc46)
> 3。[培训和测试数据](#aa8f)
> 4。[预处理数据](#3446)
> 5。[创建你的模型](#df83)
> 6。[模型拟合](#8c18)
> 7。[预测](#2ea8)
> 8。[评估你模特的表现](#60b6)
> 9。[调整您的模型](#022d)

# 基本示例:

下面的代码演示了使用 scikit-learn 在一组数据上创建和运行模型的基本步骤。

代码中的步骤包括:加载数据，分成训练集和测试集，缩放集，创建模型，根据数据拟合模型，使用训练好的模型对测试集进行预测，最后评估模型的性能。

```
>>> from sklearn import neighbors, datasets, preprocessing
>>> from sklearn.model_selection import train_test_split
>>> from sklearn.metrics import accuracy_score
>>> iris = datasets.load_iris()
>>> X,y = iris.data[:,:2], iris.target
>>> X_train, X_test, y_train, y_test = train_test_split(X,y)
>>> scaler = preprocessing_StandardScaler().fit(X_train)
>>> X_train = scaler.transform(X_train)
>>> X_test = scaler.transform(X_test)
>>> knn = neighbors.KNeighborsClassifier(n_neighbors = 5)
>>> knn.fit(X_train, y_train)
>>> y_pred = knn.predict(X_test)
>>> accuracy_score(y_test, y_pred)
```

# 加载数据

您的数据需要是数字，并存储为 NumPy 数组或 SciPy 备用矩阵。转换成数字数组的其他类型也是可以接受的，比如 Pandas DataFrame。

```
>>> import numpy as np
>>> X = np.random.random((10,5))array([[0.21069686, 0.33457064],
       [0.23887117, 0.6093155 ],
       [0.48848537, 0.62649292]])>>> y = np.array(['A','B','A'])array(['A', 'B', 'A'])
```

# 培训和测试数据

将数据集分为 X 和 y 变量的定型集和测试集。

```
>>> from sklearn.model_selection import train_test_split
>>> X_train,X_test,y_train,y_test = train_test_split(X,y, random_state = 0)
```

# 预处理数据

在模型拟合之前准备好数据。

## 标准化

通过移除平均值并缩放至单位方差来标准化要素。

```
>>> from sklearn.preprocessing import StandardScaler
>>> scaler = StandardScaler().fit(X_train)
>>> standarized_X = scaler.transform(X_train)
>>> standarized_X_test = scaler.transform(X_test)
```

## 正常化

具有至少一个非零分量的每个样本(即数据矩阵的每一行)独立于其他样本被重新缩放，使得其范数等于 1。

```
>>> from sklearn.preprocessing import Normalizer
>>> scaler = Normalizer().fit(X_train)
>>> normalized_X = scaler.transform(X_train)
>>> normalized_X_test = scaler.transform(X_test)
```

## 二值化

根据阈值将数据二值化(将特征值设置为 0 或 1)。

```
>>> from sklearn.preprocessing import Binarizer
>>> binarizer = Binarizer(threshold = 0.0).fit(X)
>>> binary_X = binarizer.transform(X_test)
```

## 编码分类特征

使用介于 0 和 n_classes-1 之间的值对目标标签进行编码。

```
>>> from sklearn import preprocessing
>>> le = preprocessing.LabelEncoder()
>>> le.fit_transform(X_train)
```

## 输入缺失值

填补缺失值的插补转换器。

```
>>> from sklearn.impute import SimpleImputer
>>> imp = SimpleImputer(missing_values = 0, strategy = 'mean')
>>> imp.fit_transform(X_train)
```

## 生成多项式要素

生成由阶数小于或等于指定阶数的要素的所有多项式组合组成的新要素矩阵。

```
>>> from sklearn.preprocessing import PolynomialFeatures
>>> poly = PolynomialFeatures(5)
>>> poly.fit_transform(X)
```

# 创建您的模型

创建各种监督和非监督学习模型。

# 监督学习模型

*   线性回归

```
>>> from sklearn.linear_model import LinearRegression
>>> lr  = LinearRegression(normalize = True)
```

*   支持向量机(SVM)

```
>>> from sklearn.svm import SVC
>>> svc = SVC(kernel = 'linear')
```

*   朴素贝叶斯

```
>>> from sklearn.naive_bayes import GaussianNB
>>> gnb = GaussianNB()
```

*   KNN

```
>>> from sklearn import neighbors
>>> knn = neighbors.KNeighborsClassifier(n_neighbors = 5)
```

# 无监督学习模型

*   主成分分析

```
>>> from sklearn.decomposition import PCA
>>> pca = PCA(n_components = 0.95)
```

*   k 表示

```
>>> from sklearn.cluster import KMeans
>>> k_means = KMeans(n_clusters = 3, random_state = 0)
```

# 模型拟合

将监督和非监督学习模型拟合到数据上。

# 监督学习

*   使模型符合数据

```
>>> lr.fit(X, y)
>>> knn.fit(X_train,y_train)
>>> svc.fit(X_train,y_train)
```

# 无监督学习

*   使模型符合数据

```
>>> k_means.fit(X_train)
```

*   适应数据，然后转换数据

```
>>> pca_model = pca.fit_transform(X_train)
```

# 预言；预测；预告

使用训练好的模型预测测试集。

*   预测标签

```
#Supervised Estimators
>>> y_pred = lr.predict(X_test)#Unsupervised Estimators
>>> y_pred = k_means.predict(X_test)
```

*   估计标签的概率

```
>>> y_pred = knn.predict_proba(X_test)
```

# 评估您的模型的性能

确定模型在测试集上表现如何的各种回归和分类指标。

# 分类指标

*   准确度分数

```
>>> knn.score(X_test,y_test)
>>> from sklearn.metrics import accuracy_score
>>> accuracy_score(y_test,y_pred)
```

*   分类报告

```
>>> from sklearn.metrics import classification_report
>>> print(classification_report(y_test,y_pred))
```

*   混淆矩阵

```
>>> from sklearn .metrics import confusion_matrix
>>> print(confusion_matrix(y_test,y_pred))
```

# 回归度量

*   绝对平均误差

```
>>> from sklearn.metrics import mean_absolute_error
>>> mean_absolute_error(y_test,y_pred)
```

*   均方误差

```
>>> from sklearn.metrics import mean_squared_error
>>> mean_squared_error(y_test,y_pred)
```

*   r 分数

```
>>> from sklearn.metrics import r2_score
>>> r2_score(y_test, y_pred)
```

# 聚类度量

*   调整后的兰德指数

```
>>> from sklearn.metrics import adjusted_rand_score
>>> adjusted_rand_score(y_test,y_pred)
```

*   同种

```
>>> from sklearn.metrics import homogeneity_score
>>> homogeneity_score(y_test,y_pred)
```

*   垂直测量

```
>>> from sklearn.metrics import v_measure_score
>>> v_measure_score(y_test,y_pred)
```

# 交叉验证

*   通过交叉验证评估分数

```
>>> from sklearn.model_selection import cross_val_score
>>> print(cross_val_score(knn, X_train, y_train, cv=4))
```

# 调整您的模型

找到将最大化模型预测准确性的正确参数值。

# 网格搜索

对估计量的特定参数值的穷举搜索。下面的示例试图找到为 knn 指定的正确聚类数量，以最大化模型的准确性。

```
>>> from sklearn.model_selection import GridSearchCV
>>> params = {'n_neighbors': np.arange(1,3), 'metric':['euclidean','cityblock']}
>>> grid = GridSearchCV(estimator = knn, param_grid = params)
>>> grid.fit(X_train, y_train)
>>> print(grid.best_score_)
>>> print(grid.best_estimator_.n_neighbors)
```

# 随机参数优化

超参数的随机搜索。与网格搜索相反，并不是所有的参数值都被尝试，而是从指定的分布中采样固定数量的参数设置。尝试的参数设置的数量由 n_iter 给出。

```
>>> from sklearn.model_selection import RandomizedSearchCV
>>> params = {'n_neighbors':range(1,5), 'weights':['uniform','distance']}
>>> rsearch = RandomizedSearchCV(estimator = knn, param_distributions = params, cv = 4, n_iter = 8, random_state = 5)
>>> rseach.fit(X_train, y_train)
>>> print(rsearch.best_score_)
```

Scikit-learn 对于各种机器学习模型来说是一个非常有用的库。以上部分提供了在不同模型上执行分析的基本分步过程。如果你想了解更多，请查阅 [Scikit-Learn](https://scikit-learn.org/stable/) 的文档，因为还有很多有用的函数可以学习。

[**与 5k 以上的人一起加入我的电子邮件列表，免费获得“完整的 Python 数据科学小抄手册”**](https://pages.christopherzita.com/python-cheat-sheet)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)