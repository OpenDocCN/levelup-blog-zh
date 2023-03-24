# 如何可视化多元数据分析

> 原文：<https://levelup.gitconnected.com/how-to-visualize-multivariate-data-analysis-ba7f13e230f9>

## 基于 PCA 和因子分析的图表

![](img/31c28ce7851932972c8e7037ea31265d.png)

在本教程中，我们将使用 [**factoextra**](https://cran.r-project.org/web/packages/factoextra/index.html) R 包，并考虑[国家](https://drive.google.com/file/d/1H0Bc1dmA6RQ7iWF0w76jCjvmK-fxeR7i/view?usp=sharing)数据集。让我们开始:

```
library(factoextra)df<-read.csv("DataCountries.txt", sep="\t")head(df)
```

![](img/7704da5073e315e6fce5fe03f2d90640.png)![](img/374328d3af894d6e7288d1f2f8f8bf2d.png)

# 主成分分析

现在，我们将对数据集运行 PCA 分析。注意，我们只需要包含数字变量。我们还将把列`Country`设置为行名。

```
# set as rownames the column Country
rownames(df)<-df$Country# remove the Countrly columns
df$Country<-NULL# run a PCA Analysis
dfPCA <- prcomp(df, center = TRUE, scale. = TRUE)
```

让我们得到 Scree 图，它显示了由主成分解释的方差的百分比。

```
fviz_eig (dfPCA)
```

![](img/b2e43560d9a1d62f10def9331787a1e9.png)

# 个人图表

让我们通过考虑因素图上个体的质量，将所有国家绘制成二维图。

```
# cos2 = the quality of the individuals on the factor map
# Select and visualize some individuals (ind) with select.ind argument.
 # - ind with cos2 >= 0.96: select.ind = list(cos2 = 0.96)
 # - Top 20 ind according to the cos2: select.ind = list(cos2 = 20)
 # - Top 20 contributing individuals: select.ind = list(contrib = 20)
 # - Select ind by names: select.ind = list(name = c("23", "42", "119") )fviz_pca_ind(dfPCA, col.ind = "cos2" , repel = TRUE)
```

![](img/80d9992e67c9d29f7043d5da0f5c886b.png)

# 变量图

让我们看看如何通过考虑变量的贡献，将变量表示成二维形式。

```
#  select.var = list(contrib = 2)fviz_pca_var(dfPCA, col.var = "contrib", repel = TRUE)
```

![](img/90021e9e0141ccc0b37cc0ec2e4d9dba.png)

# 双标图

```
# Graph of the Biplot 
fviz_pca_biplot(dfPCA, repel = TRUE)
```

![](img/989506c9fab0a652455ba957c19bfa39.png)

# 特征值、变量和个体

让我们看看如何获得变量和个体的特征值和统计数据，如**坐标**、对 PCs 的**贡献**和**表示质量**

**特征值**

```
# Eigenvalues
eigens_vals <- get_eigenvalue(dfPCA) eigens_vals
```

![](img/9376e36341e0d4c530f3dba5ff1a865b.png)

# 变量

```
# By Variable 
by_var <- get_pca_var(dfPCA) by_var$coord 
by_var$contrib 
by_var$cos2
```

![](img/ad76a9f81251710f8b3614299ec4a35e.png)

**个人**

```
# By ndividual 
by_ind <- get_pca_ind(dfPCA) 
by_ind$coord 
by_ind$contrib 
by_ind$cos2
```

[](https://jorgepit-14189.medium.com/membership) [## 用我的推荐链接加入媒体-乔治皮皮斯

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

jorgepit-14189.medium.com](https://jorgepit-14189.medium.com/membership) 

*原载于*[*https://predictivehacks.com*](https://predictivehacks.com/how-to-visualize-multivariate-data-analysis/)*。*