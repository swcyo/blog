---
title: 可视化矩阵的相关性分析之ggcorrplot
author: 欧阳松
date: '2021-10-18'
slug: ggcorrplot
categories:
  - ggcorrplot
tags:
  - 相关性分析
from_Rmd: yes
---

前面介绍了corrplot可视化相关性分析，还有一个比较简单的包是ggcorrplot也可以绘制好看的相关性分析图，官网有一个很好的介绍文章（但是有时候打不开）

[ggcorrplot: Visualization of a correlation matrix using ggplot2 - Easy Guides - Wiki - STHDA](http://sthda.com/english/wiki/ggcorrplot-visualization-of-a-correlation-matrix-using-ggplot2)

国内也可以参考如下文章：

[R packages\|ggcorrplot : 相关矩阵的可视化(热图) - 简书 (jianshu.com)](https://www.jianshu.com/p/97f3420ac0f5)

[R语言可视化学习笔记之相关矩阵可视化包ggcorrplot - 简书 (jianshu.com)](https://www.jianshu.com/p/7fd01d00f741)

在R中可视化相关矩阵(correlation matrix)的最简单方法是使用corrplot包。另一种方法是在ggally包中使用函数ggcorr()。 但是，ggally包不提供用于重新排序相关矩阵或显示显著水平的选项。而基于**ggplot2**包以及**corrplot**包的相关矩阵可视化包**ggcorrplot**，**ggcorrplot**包提供对相关矩阵重排序以及在相关图中展示显著性水平的方法，同时也能计算相关性p-value

---

## ggcorrplot的主要特征

ggcorrplot具有重新排序相关矩阵以及在热图上显示显著性水平的功能。此外，它还包括用于计算相关性p值的矩阵的功能。

    ggcorrplot(): 使用ggplot2相关矩阵可视化。

    cor_pmat(): 计算相关性的p值。

## ggcorrplot下载与加载

    #CRAN     
    install.packages("ggcorrplot")
    #GitHub
    if(!require(devtools)) install.packages("devtools")
    devtools::install_github("kassambara/ggcorrplot")

接下来，我们将使用R包**ggcorrplot**可视化相关矩阵，依旧使用mrcars数据，现按官网的教程跑一下

## **计算相关矩阵**


```r
library(ggcorrplot)
data(mtcars)
corr <- round(cor(mtcars), 3)  # 使用cor()函数计算相关性，显示3位小数点
```


|     |    mpg|    cyl|   disp|     hp|   drat|     wt|   qsec|     vs|     am|   gear|   carb|
|:----|------:|------:|------:|------:|------:|------:|------:|------:|------:|------:|------:|
|mpg  |  1.000| -0.852| -0.848| -0.776|  0.681| -0.868|  0.419|  0.664|  0.600|  0.480| -0.551|
|cyl  | -0.852|  1.000|  0.902|  0.832| -0.700|  0.782| -0.591| -0.811| -0.523| -0.493|  0.527|
|disp | -0.848|  0.902|  1.000|  0.791| -0.710|  0.888| -0.434| -0.710| -0.591| -0.556|  0.395|
|hp   | -0.776|  0.832|  0.791|  1.000| -0.449|  0.659| -0.708| -0.723| -0.243| -0.126|  0.750|
|drat |  0.681| -0.700| -0.710| -0.449|  1.000| -0.712|  0.091|  0.440|  0.713|  0.700| -0.091|
|wt   | -0.868|  0.782|  0.888|  0.659| -0.712|  1.000| -0.175| -0.555| -0.692| -0.583|  0.428|
|qsec |  0.419| -0.591| -0.434| -0.708|  0.091| -0.175|  1.000|  0.745| -0.230| -0.213| -0.656|
|vs   |  0.664| -0.811| -0.710| -0.723|  0.440| -0.555|  0.745|  1.000|  0.168|  0.206| -0.570|
|am   |  0.600| -0.523| -0.591| -0.243|  0.713| -0.692| -0.230|  0.168|  1.000|  0.794|  0.058|
|gear |  0.480| -0.493| -0.556| -0.126|  0.700| -0.583| -0.213|  0.206|  0.794|  1.000|  0.274|
|carb | -0.551|  0.527|  0.395|  0.750| -0.091|  0.428| -0.656| -0.570|  0.058|  0.274|  1.000|


```r
# 用ggcorrplot包提供的函数cor_pmat()计算相关性的P值矩阵
p.mat <- cor_pmat(mtcars)
head(p.mat[, 1:4])
```

```
##            mpg       cyl      disp        hp
## mpg  0.000e+00 6.113e-10 9.380e-10 1.788e-07
## cyl  6.113e-10 0.000e+00 1.803e-12 3.478e-09
## disp 9.380e-10 1.803e-12 0.000e+00 7.143e-08
## hp   1.788e-07 3.478e-09 7.143e-08 0.000e+00
## drat 1.776e-05 8.245e-06 5.282e-06 9.989e-03
## wt   1.294e-10 1.218e-07 1.222e-11 4.146e-05
```

## **相关矩阵可视化**


```r
# 可视化相关矩阵 默认作图，method = 'square'
ggcorrplot(corr)
```

![plot of chunk unnamed-chunk-4](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-4-1.png)


```r
# 调整矩形热图为圆形，method = 'circle'
ggcorrplot(corr, method = "circle")
```

![plot of chunk unnamed-chunk-5](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-5-1.png)


```r
# 重新排序相关矩阵，使用等级聚类（hierarchical clustering）
ggcorrplot(corr, hc.order = TRUE, outline.col = "white")  #方形或圆圈的轮廓颜色。 默认值为“灰色”。
```

![plot of chunk unnamed-chunk-6](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-6-1.png)


```r
#控制矩阵形状
#下三角形
ggcorrplot(corr, hc.order = TRUE, type = "lower", #下三角形
           outline.color = "white")
```

![plot of chunk unnamed-chunk-7](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-7-1.png)


```r
#上三角形
ggcorrplot(corr, hc.order = TRUE, type = "upper", #上三角形
           outline.color = "white")
```

![plot of chunk unnamed-chunk-8](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-8-1.png)


```r
# 更改颜色以及主题
ggcorrplot(corr, hc.order = TRUE, type = "lower", outline.color = "white", ggtheme = ggplot2::theme_gray,
  colors = c("#6D9EC1", "white", "#E46726"))
```

![plot of chunk unnamed-chunk-9](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-9-1.png)


```r
# 添加相关系数
ggcorrplot(corr, hc.order = TRUE, type = "lower", lab = TRUE)
```

![plot of chunk unnamed-chunk-10](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-10-1.png)


```r
# 增加显著性水平，不显著的话就不添加了
ggcorrplot(corr, hc.order = TRUE, type = "lower", p.mat = p.mat)
```

![plot of chunk unnamed-chunk-11](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-11-1.png)


```r
# 将不显著的色块设置成空白
ggcorrplot(corr, p.mat = p.mat, hc.order = TRUE, type = "lower", insig = "blank")
```

![plot of chunk unnamed-chunk-12](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-12-1.png)

## **行列一致美化** 

即同一个文件内的指标，或两个文件的指标数目一致分析，是一个i\*j（i=j）的矩阵；


```r
library(ggcorrplot)
library(ggthemes)
ggcorrplot(corr, method = c("square"), type = c("full"), ggtheme = ggplot2::theme_void,
  title = " ", show.legend = TRUE, legend.title = "Corr_r2", show.diag = T, colors = c("#839EDB",
    "white", "#FF8D8D"), outline.color = "white", hc.order = T, hc.method = "single",
  lab = F, lab_col = "black", lab_size = 2, p.mat = NULL, sig.level = 0.05, insig = c("pch"),
  pch = 4, pch.col = "white", pch.cex = 4.5, tl.cex = 12, tl.col = "black", tl.srt = 45,
  digits = 2)
```

![plot of chunk unnamed-chunk-13](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-13-1.png)


```r
ggcorrplot(corr, method = "square", type = "upper", ggtheme = ggplot2::theme_void,
  title = "", show.legend = TRUE, legend.title = "Corr", show.diag = T, colors = c("#839EDB",
    "white", "#FF8D8D"), outline.color = "white", hc.order = T, hc.method = "single",
  lab = F, lab_col = "black", lab_size = 3, p.mat = p.mat, sig.level = 0.05, insig = c("pch"),
  pch = 22, pch.col = "white", pch.cex = 4, tl.cex = 12, tl.col = "black", tl.srt = 0,
  digits = 2)
```

![plot of chunk unnamed-chunk-14](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-14-1.png)

上图中需要注意的是：格子中含有小方框的格子表示该相关性不显著（0.05），且格子中小方框颜色表示p value 大小，可修改参数为：pch = 22。


```r
ggcorrplot(corr, method = "circle", type = "full", ggtheme = ggplot2::theme_void,
  title = "", show.legend = TRUE, legend.title = "Corr", show.diag = F, colors = c("#839EDB",
    "white", "#FF8D8D"), outline.color = "white", hc.order = T, hc.method = "complete",
  lab = FALSE, lab_col = "black", lab_size = 4, p.mat = NULL, sig.level = 0.05,
  insig = c("pch", "blank"), pch = 4, pch.col = "black", pch.cex = 5, tl.cex = 12,
  tl.col = "black", tl.srt = 45, digits = 2)
```

![plot of chunk unnamed-chunk-15](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-15-1.png)


```r
ggcorrplot(corr, method = "circle", type = "upper", ggtheme = ggplot2::theme_bw(),
  title = "", show.legend = TRUE, legend.title = "Corr", show.diag = T, colors = c("#839EDB",
    "white", "#FF8D8D"), outline.color = "white", hc.order = T, hc.method = "complete",
  lab = T, lab_col = "black", lab_size = 2, p.mat = p.mat, sig.level = 0.05, insig = "blank",
  pch = 4, pch.col = "black", pch.cex = 5, tl.cex = 12, tl.col = "black", tl.srt = 45,
  digits = 2)
```

![plot of chunk unnamed-chunk-16](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-16-1.png)

## **行列不一致美化**

行列不一致，在这里借助psych包来计算相关性和p value。


```r
library(ggcorrplot)
library(ggthemes)
library(psych)
```

```
## 
## Attaching package: 'psych'
```

```
## The following objects are masked from 'package:ggplot2':
## 
##     %+%, alpha
```

```r
data <- mtcars
data1 <- data[c(1:5)]
data2 <- data[c(6:11)]  #刻意截取不一致

cor <- corr.test(data1, data2, method = "spearman", adjust = "BH", ci = F)
cmt <- cor$r
pmt <- cor$p.adj
```


```r
ggcorrplot(cmt, method = "circle", outline.color = "white", ggtheme = theme_bw(),
  colors = c("#839EDB", "white", "#FF8D8D"), lab = T, lab_size = 2, p.mat = pmt,
  insig = "pch", pch.col = "red", pch.cex = 3, tl.cex = 12)
```

![plot of chunk unnamed-chunk-18](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-18-1.png)


```r
ggcorrplot(cmt, method = "circle", outline.color = "white", ggtheme = theme_bw(),
  colors = c("#839EDB", "white", "#FF8D8D"), lab = T, lab_size = 2, p.mat = pmt,
  insig = "blank", pch.col = "red", pch.cex = 3, tl.cex = 12)
```

![plot of chunk unnamed-chunk-19](/figures/course/2021-10-18-ggcorrplot/ggcorrplot/unnamed-chunk-19-1.png)
