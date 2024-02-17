---
title: 如何在R中放大或圈注目标选区？
author: 欧阳松
date: '2024-02-17'
slug: r-zoom2
categories:
  - R
  - ggplot2
  - 局部放大
  - ggforce
tags:
  - R
  - 画图
  - 局部放大
  - ggplot2
  - ggforce
from_Rmd: yes
---

之前写了一个[R语言画图之图片局部放大](/course/2024-01-21-r-zoom/r-zoom/)的教程。使用的**ggmagnify**这个包实现，今天介绍用**ggforce**这个包对图表的目标区域进行放大或圈选标注。

同样使用*iris*数据集进行示意使用实战。

## 相关R包下载与载入

```
#相关R包下载与载入：
install.packages("ggforce") #稳定版
devtools::install_github("thomasp85/ggforce") #开发版
library(ggforce)
library(ggplot2)
library(dplyr)
#使用鸢尾数据集进行测试：
dt <- iris
head(dt)
```


| Sepal.Length| Sepal.Width| Petal.Length| Petal.Width|Species |
|------------:|-----------:|------------:|-----------:|:-------|
|          5.1|         3.5|          1.4|         0.2|setosa  |
|          4.9|         3.0|          1.4|         0.2|setosa  |
|          4.7|         3.2|          1.3|         0.2|setosa  |
|          4.6|         3.1|          1.5|         0.2|setosa  |
|          5.0|         3.6|          1.4|         0.2|setosa  |
|          5.4|         3.9|          1.7|         0.4|setosa  |

## 绘制散点图

首先绘制一个简单的散点图，同之前教程。

```r
# 设置一个配色
mycol <- c("#2fa1dd", "#e6b707", "#f87669")
# 绘图
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) + geom_point() + scale_color_manual(values = rev(mycol)) +
  theme_bw()
```

![plot of chunk unnamed-chunk-2](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-2-1.png)

## 分面：局部放大目标选区

使用`facet_zoom()`函数进行分面的局部放大。

### 在X轴上放大局部选区

```r
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) +
facet_zoom(x = Species == "versicolor") + #放大versicolor所在区域的散点
geom_point() +
scale_color_manual(values=rev(mycol)) +
theme_bw()
```

![plot of chunk unnamed-chunk-3](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-3-1.png)

还可以放大自定义区域

```r
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) +
facet_zoom(xlim = c(4.5, 5.5)) + #放大x轴数据处于4.5-5.5的区间
geom_point() +
scale_color_manual(values=rev(mycol)) +
theme_bw()
```

![plot of chunk unnamed-chunk-4](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-4-1.png)

### 在y轴上放大局部选区

```r
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) + facet_zoom(y = Species ==
  "virginica") + geom_point() + scale_color_manual(values = rev(mycol)) + theme_bw()
```

![plot of chunk unnamed-chunk-5](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-5-1.png)

### 在xy轴上同时放大局部选区


```r
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) + facet_zoom(xy = Species ==
  "versicolor") + geom_point() + scale_color_manual(values = rev(mycol)) + theme_bw()
```

![plot of chunk unnamed-chunk-6](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-6-1.png)

同样，我们还可以使用`ylim`以及`x/ylim`参数来自定义在y轴或x/y轴上的放大区间范围，这里不再赘述。

## 为图表添加几何轮廓及标注

### 矩形框选添加

#### 对所有分组添加

```r
## 对所有分组添加：
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) + geom_mark_rect(aes(fill = Species)) +
  geom_point() + scale_color_manual(values = rev(mycol)) + scale_fill_manual(values = rev(mycol)) +
  theme_bw()
```

![plot of chunk unnamed-chunk-7](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-7-1.png)

#### 特定分组添加

```r
# 对特定分组添加：
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) + geom_mark_rect(aes(fill = Species,
  filter = Species == "setosa")) + geom_point() + scale_color_manual(values = rev(mycol)) +
  scale_fill_manual(values = rev(mycol)) + theme_bw()
```

![plot of chunk unnamed-chunk-8](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-8-1.png)

#### 图中添加文字标注


```r
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) +
geom_mark_rect(aes(fill = Species, label = Species), con.cap = 0) + #con.cap用于调整连线和图形之前的距离，0时为直接相连
geom_point() +
scale_color_manual(values=rev(mycol)) +
scale_fill_manual(values=rev(mycol)) +
theme_bw()
```

![plot of chunk unnamed-chunk-9](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-9-1.png)

### 圆形框选添加

```r
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) + geom_mark_circle(aes(fill = Species,
  label = Species), con.cap = 0) + geom_point() + scale_color_manual(values = rev(mycol)) +
  scale_fill_manual(values = rev(mycol)) + theme_bw()
```

![plot of chunk unnamed-chunk-10](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-10-1.png)

### 椭圆框选添加

```r
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) + geom_mark_ellipse(aes(fill = Species,
  label = Species), con.cap = 0) + geom_point() + scale_color_manual(values = rev(mycol)) +
  scale_fill_manual(values = rev(mycol)) + theme_bw()
```

![plot of chunk unnamed-chunk-11](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-11-1.png)

### 不规则区域圈选

```r
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) + geom_mark_hull(aes(fill = Species,
  label = Species), con.cap = 0) + geom_point() + scale_color_manual(values = rev(mycol)) +
  scale_fill_manual(values = rev(mycol)) + theme_bw()
```

![plot of chunk unnamed-chunk-12](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-12-1.png)

### 将不规则的边缘调平整

```r
ggplot(dt, aes(Petal.Length, Petal.Width, colour = Species)) +
geom_mark_hull(aes(fill = Species, label = Species), con.cap = 0, concavity = 3.5) + #concavity默认为2，数值越大凹凸度越低
geom_point() +
scale_color_manual(values=rev(mycol)) +
scale_fill_manual(values=rev(mycol)) +
theme_bw()
```

![plot of chunk unnamed-chunk-13](/figures/course/2024-02-17-r-zoom2/r-zoom2/unnamed-chunk-13-1.png)
