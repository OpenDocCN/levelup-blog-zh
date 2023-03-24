# 数据科学实践的免费数据集

> 原文：<https://levelup.gitconnected.com/free-datasets-for-data-science-practice-373e50fb2bb0>

![](img/878f9982495b3808bd4cbfce160e3d75.png)

图片来源:[像素](https://www.pexels.com/photo/stock-exchange-board-210607/)

## 用于数据分析和机器学习实践的开源数据集

# 一.导言

数据科学是一个非常实用的领域，你可以在实践中学习。提高你在数据科学和机器学习方面的技能的最好方法是继续从事几个数据科学项目。有时，找到合适的数据集用于您的项目可能会很有挑战性。令人欣慰的是，有如此多的免费资源可供使用，您可以从中获得干净、结构化的数据集，以便进行分析和建模。本文将讨论可用于分析和建模的各种免费开放数据来源。

# 二。免费数据集

如果你对可以用来练习数据科学和机器学习技能的开放和免费数据集感兴趣，这里有一些开放资源:

## a) R 数据集包

R 数据集包包含各种数据集。如需完整列表，请使用`library(help = "datasets")`

例如， **women** 是属于包含女性身高和体重的数据集包的数据集，可以按如下方式访问:

```
data("women")head(women)
```

## b) R Dslabs 包

R [**dslabs**](https://www.rdocumentation.org/packages/dslabs/versions/0.7.1) 包包含数据集和函数，可用于数据科学课程和研讨会中的数据分析练习、作业和项目。二十六(26)个数据集可用于数据可视化、统计推断、建模、线性回归、数据争论和机器学习方面的案例研究。

可以按如下方式安装和访问 dslabs 软件包:

```
install.packages("dslabs")library("dslabs")data(package ='dslabs')
```

例如，[***heights***](https://github.com/bot13956/Bayes_theorem)数据集包含在 R 的 dslabs 包中，可以使用 R Studio 中的以下代码将其转换为逗号分隔值(CSV)文件格式:

```
install.packages('dslabs')library(dslabs)write.csv(heights, "heights.csv", row.names = F)
```

## c) Python Sklearn 数据集

[Python sklearn 数据集](https://scikit-learn.org/stable/datasets/index.html#toy-datasets)附带了一些标准数据集，例如用于分类的[虹膜](https://en.wikipedia.org/wiki/Iris_flower_data_set)和[数字](https://archive.ics.uci.edu/ml/datasets/Pen-Based+Recognition+of+Handwritten+Digits)数据集以及用于回归的[波士顿房价数据集](https://archive.ics.uci.edu/ml/machine-learning-databases/housing/)。

Sklearn 数据集的访问方式如下:

```
from sklearn import datasetsiris = datasets.load_iris()digits = datasets.load_digits()breast_cancer_data = datasets.load_breast_cancer()
```

## d)加州大学欧文分校(UCI)机器学习知识库

UCI 目前维护着 487 个[数据集](https://archive.ics.uci.edu/ml/datasets.php)作为对机器学习社区的服务，可用于数据科学课程和研讨会中的数据分析实践、作业和项目。

## e) Kaggle 数据集

[Kaggle datasets](https://www.kaggle.com/datasets) 还包含大量用于极具挑战性的数据科学和机器学习项目的数据集。

## f)来自互联网的数据

有时你可以从网站上抓取数据，但是需要做大量的工作来清理、组织和重塑数据。然而，一些网站包含的数据格式清晰且结构化。非结构化数据的一个例子是大学城数据集的列表，它可以从[维基百科](https://en.wikipedia.org/wiki/List_of_college_towns#College_towns_in_the_United_States)中抓取。然后可以将抓取的数据整理并保存为文本文件以供进一步分析: [**数据整理教程:大学城数据集**](https://medium.com/towards-artificial-intelligence/tutorial-on-data-wrangling-college-towns-dataset-a0e8f8dfb6ae) 。互联网数据的另一个优势是它可以实时收集，例如股票数据或新冠肺炎数据。这种类型的数据非常适合于时间序列分析和预测。

Python 和 R 程序提供了一些资源，如果您知道文件的 URL，就可以从 CSV 文件导入数据。

**(i)使用 Python 和文件的 URL 导入 CSV 文件**

```
import pandas as pddf = pd.read_csv('https://archive.ics.uci.edu/ml/machinelearning-databases/breast-cancer-wisconsin/wdbc.data',header=None)
```

**(ii)使用 R 和文件的 URL 导入 CSV 文件**

*   **download.file()函数**

此功能将下载一个 CSV 文件，并将其另存为一个新文件:

```
download.file(“[https://raw.githubusercontent.com/bot13956/datasets/master/introduction_to_physics_grades.csv](https://raw.githubusercontent.com/bot13956/datasets/master/introduction_to_physics_grades.csv)", “grades.csv”)
```

*   **read.csv()函数**

此函数将下载文件并将其保存为数据框:

```
data<-read.csv(“[https://raw.githubusercontent.com/bot13956/datasets/master/introduction_to_physics_grades.csv](https://raw.githubusercontent.com/bot13956/datasets/master/introduction_to_physics_grades.csv)")
```

**(iii)从 pdf 文件中提取数据**

除了 CSV 文件格式，还可以从 pdf 文件中提取互联网数据: [**使用 Python 从 PDF 文件中提取数据，R**](https://medium.com/towards-artificial-intelligence/extracting-data-from-pdf-file-using-python-and-r-4ed8826bc5a1) 。

# 三。总结和结论

总之，我们已经讨论了可用于分析和建模的免费数据集的各种来源。数据是任何数据科学和机器学习任务的关键。数据有不同的形式，如数字数据、分类数据、文本数据、图像数据、声音数据和视频数据。模型的预测能力取决于构建模型时所用数据的质量。因此，在使用数据进行分析之前，检查数据来源的可靠性极其重要。

## 高质量数据的优势

a)高质量的数据不太可能产生错误。

b)高质量的数据可以导致较低的泛化误差，即模型很容易捕捉真实生活的影响，并可以应用于预测目的的看不见的数据。

c)由于较小的不确定性，高质量的数据将产生可靠的结果，即，数据捕捉真实的效果并且具有较小的随机噪声。

d)包含大量观测值的高质量数据会减少方差误差(方差误差根据**中心极限定理**随样本量减少)。

# 其他数据科学/机器学习资源

[数据科学最低要求:开始从事数据科学时你需要知道的 10 项基本技能](https://towardsdatascience.com/data-science-minimum-10-essential-skills-you-need-to-know-to-start-doing-data-science-e5a5a9be5991)

[数据科学课程](https://medium.com/towards-artificial-intelligence/data-science-curriculum-bf3bb6805576)

[机器学习的基本数学技能](https://medium.com/towards-artificial-intelligence/4-math-skills-for-machine-learning-12bfbc959c92)

[3 个最佳数据科学 MOOC 专业](https://medium.com/towards-artificial-intelligence/3-best-data-science-mooc-specializations-d58da382f628)

[进入数据科学的 5 个最佳学位](https://towardsdatascience.com/5-best-degrees-for-getting-into-data-science-c3eb067883b1)

[2020 年开始数据科学之旅的 5 个理由](https://towardsdatascience.com/5-reasons-why-you-should-begin-your-data-science-journey-in-2020-2b4a0a5e4239)

[数据科学的理论基础——我应该关心还是仅仅关注实践技能？](https://towardsdatascience.com/theoretical-foundations-of-data-science-should-i-care-or-simply-focus-on-hands-on-skills-c53fb0caba66)

[机器学习项目规划](https://towardsdatascience.com/machine-learning-project-planning-71bdb3a44349)

[如何组织你的数据科学项目](https://towardsdatascience.com/how-to-organize-your-data-science-project-dd6599cf000a)

[大型数据科学项目的生产力工具](https://medium.com/towards-artificial-intelligence/productivity-tools-for-large-scale-data-science-projects-64810dfbb971)

[数据科学作品集比简历更有价值](https://towardsdatascience.com/a-data-science-portfolio-is-more-valuable-than-a-resume-2d031d6ce518)

[数据科学 101 —包含 R 和 Python 代码的中型平台短期课程](https://medium.com/towards-artificial-intelligence/data-science-101-a-short-course-on-medium-platform-with-r-and-python-code-included-3cdc9d489c6d)

***如有疑问，请发邮件给我***:benjaminobi@gmail.com