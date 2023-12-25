---
title: 使用tinyarray做主成分分析
author: 欧阳松
date: '2022-03-10'
slug: tinyarray-PCA
categories:
  - 教程
  - tinyarray
tags:
  - 主成分分析
  - PCA
  - tinyarray
from_Rmd: yes
---

主成分分析（PCA）是一种降维分析，说实话我也不怎么懂什么叫PCA，只知道他要画一个分组的圈圈图，网上也有很多教程，这里我只介绍简单的一种，说实话**tinyarray**这个包是真的好，可以用的地方很多，这几天突然发现他的draw_pca又加了几个主题，试了一下，确实很不错，代码相当的简单，当然**你只安装tinyarray这个包肯定是不行，你是永远跑不出来结果的**，因为所有的东西都是一环套一环，tinyarray是一个搬运工，嵌入了很多包的函数，所以才会那么的快捷。

基础命令如下：

    draw_pca(
      exp,
      group_list,
      color = c("#2874C5", "#f87669", "#e6b707", "#868686", "#92C5DE", "#F4A582",
        "#66C2A5", "#FC8D62", "#8DA0CB", "#E78AC3", "#A6D854", "#FFD92F", "#E5C494",
        "#B3B3B3"),
      addEllipses = TRUE,
      style = "default",
      color.label = "Group"
    )

可以看到做PCA，你需要两个数据，一个是表达矩阵，另一个是分组，当然这个分组，你也是可以放在表达矩阵里的，我们用默认的介绍函数跑一下，结果见Figure \@ref(fig:fig1)所示，这里一定要注意的是**矩阵必须要倒置**，即`t(iris)`


```r
library(tinyarray)
draw_pca(t(iris[, 1:4]), iris$Species)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-03-10-tinyarray-pca/tinyarray-pca/fig1-1.png" alt="默认的iris数据出默认的图"  />
<p class="caption">默认的iris数据出默认的图</p>
</div>

图是很禁欲的图，我们再回过头来看看到底是如何实现的？首先看看数据,见Table \@ref(tab:table)所示

我们可以看到四列数据的表达矩阵，和一列Species的分组

    iris[1:10,1:5]


Table: iris数据前十行

| Sepal.Length | Sepal.Width | Petal.Length | Petal.Width | Species |
|:------------:|:-----------:|:------------:|:-----------:|:-------:|
|     5.1      |     3.5     |     1.4      |     0.2     | setosa  |
|     4.9      |     3.0     |     1.4      |     0.2     | setosa  |
|     4.7      |     3.2     |     1.3      |     0.2     | setosa  |
|     4.6      |     3.1     |     1.5      |     0.2     | setosa  |
|     5.0      |     3.6     |     1.4      |     0.2     | setosa  |
|     5.4      |     3.9     |     1.7      |     0.4     | setosa  |
|     4.6      |     3.4     |     1.4      |     0.3     | setosa  |
|     5.0      |     3.4     |     1.5      |     0.2     | setosa  |
|     4.4      |     2.9     |     1.4      |     0.2     | setosa  |
|     4.9      |     3.1     |     1.5      |     0.1     | setosa  |

如果我们不想要外面那个圈的画，设置`addEllipses = F`即可，见Figure \@ref(fig:fig2)所示，但是这样太凌乱，所以还是建议加个外圈圈，也就是默认的函数


```r
draw_pca(t(iris[, 1:4]), iris$Species, addEllipses = F  # 不加圈
)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-03-10-tinyarray-pca/tinyarray-pca/fig2-1.png" alt="不加圈"  />
<p class="caption">不加圈</p>
</div>

仔细看函数，我们发现有一个`style =`的设置，介绍是style	
plot style,"default","ggplot2"and "3D"，也就是说有默认，ggplot2和3D这三种模式，我们试着改改ggplot2（Figure \@ref(fig:fig3)）和3D（Figure \@ref(fig:fig4)）风格，这里可以看到只有ggplot2风格才能使用`color.label=`这个设置，但是这两个风格却没有来Dim1和Dim2的值来。


```r
p1 <- draw_pca(t(iris[, 1:4]), iris$Species, color.label = "Species", style = "ggplot2")
p1
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-03-10-tinyarray-pca/tinyarray-pca/fig3-1.png" alt="ggplot2风格"  />
<p class="caption">ggplot2风格</p>
</div>


```r
p2 <- draw_pca(t(iris[, 1:4]), iris$Species, style = "3D")
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-03-10-tinyarray-pca/tinyarray-pca/fig4-1.png" alt="3D风格"  />
<p class="caption">3D风格</p>
</div>

```r
p2
```

```
## $rect
## $rect$w
## [1] 7.67
## 
## $rect$h
## [1] 1.048
## 
## $rect$left
## [1] -1.408
## 
## $rect$top
## [1] -2.915
## 
## 
## $text
## $text$x
## [1] -0.669  1.826  4.321
## 
## $text$y
## [1] -3.439 -3.439 -3.439
```

---

既然有ggplot2风格，我们当然可以继续增加元素，比如不见了的标题和比例值


```r
library(ggplot2)
p1 + theme_bw() + ggtitle("Individuals - PCA") + xlab("Dim1 (73%)") + ylab("Dim2 (22.9%)")
```

![plot of chunk unnamed-chunk-1](/figures/course/2022-03-10-tinyarray-pca/tinyarray-pca/unnamed-chunk-1-1.png)

如果你安装了**ggprism**这个包，我们可以冒充一下prism主题


```r
p1 + ggprism::theme_prism() + ggtitle("Individuals - PCA") + xlab("Dim1 (73%)") +
  ylab("Dim2 (22.9%)")
```

![plot of chunk unnamed-chunk-2](/figures/course/2022-03-10-tinyarray-pca/tinyarray-pca/unnamed-chunk-2-1.png)

---

对于没有分组信息的数据，我们需要人为添加一个分组，并且设置为因子格式，我们稍微修改一下示例，见Table \@ref(tab:table2)所示


```r
exp <- matrix(rnorm(120), nrow = 15)
colnames(exp) <- paste0("sample", 1:8)
rownames(exp) <- paste0("gene", 1:15)
```


Table: 随机生成exp

|       | sample1 | sample2 | sample3 | sample4 | sample5 | sample6 | sample7 | sample8 |
|:------|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|
|gene1  | -1.0101 | 0.1360  | 0.3231  | -1.8829 | -0.2634 | -1.0921 | 0.9418  | 0.3909  |
|gene2  | 0.6235  | -0.3331 | 0.9556  | -1.7392 | -1.2584 | 0.9534  | -0.1679 | -1.2846 |
|gene3  | -2.6387 | -0.2474 | -0.2115 | -0.0571 | -1.5527 | -1.9686 | -0.1642 | 0.9660  |
|gene4  | 1.4683  | 0.0247  | -0.2439 | 0.2175  | 0.5249  | 1.5417  | 0.3813  | -1.0547 |
|gene5  | 2.1036  | 0.5635  | -0.1229 | 0.1100  | -0.7072 | 1.2593  | 0.5820  | -0.9344 |
|gene6  | 0.4819  | -1.0017 | -2.1266 | -0.7670 | 2.2573  | 1.0973  | -0.9580 | -1.8656 |
|gene7  | 0.1802  | 1.1374  | 0.3123  | -0.8885 | -2.4292 | -0.9596 | -1.6023 | 0.6566  |
|gene8  | 0.1167  | 0.0226  | 0.3032  | 0.1792  | -1.2856 | -2.3993 | 0.6460  | 2.2072  |
|gene9  | -0.2534 | 1.2564  | -0.0424 | 1.5202  | 0.8816  | -0.1992 | 0.2774  | 0.1756  |
|gene10 | -1.0269 | -0.8300 | -0.0301 | 0.9521  | -0.1542 | -0.2779 | -0.6367 | -0.8123 |
|gene11 | -1.5962 | 1.4320  | 0.6395  | -0.9307 | 1.4417  | -0.2535 | -1.8476 | 0.8073  |
|gene12 | -2.1421 | -0.2920 | 0.3713  | -0.5708 | 0.1927  | -0.3286 | -0.5075 | 0.0902  |
|gene13 | 0.7019  | -0.3167 | 1.0461  | -0.0239 | 0.8250  | -1.2163 | 0.3425  | -2.2276 |
|gene14 | -0.6830 | 0.5904  | -1.1058 | -0.1906 | 0.4191  | 0.0000  | 0.5167  | 1.3981  |
|gene15 | -0.6337 | -0.5923 | -0.3127 | 0.8701  | -0.6494 | -0.0545 | -0.0720 | 0.7839  |


```r
group_list <- factor(rep(c("A", "B"), each = 4))  ## 随机定义分组
```

简简单单出个默认图，改个背景


```r
draw_pca(exp, group_list) + theme_bw()
```

![plot of chunk unnamed-chunk-5](/figures/course/2022-03-10-tinyarray-pca/tinyarray-pca/unnamed-chunk-5-1.png)

或者我们再改个配色


```r
draw_pca(exp, group_list, color = c("steelblue", "brown")) + theme_bw()
```

![plot of chunk unnamed-chunk-6](/figures/course/2022-03-10-tinyarray-pca/tinyarray-pca/unnamed-chunk-6-1.png)
