---
title: 用ggplot2画好看的火山图
author: 欧阳松
date: '2021-10-05'
slug: ggplot2-volcano
categories:
  - 火山图
  - ggplot2
  - 博客
tags:
  - ggplot2
  - 火山图
from_Rmd: yes
---

做差异分析的时候，第一张图就是火山图，用来表示不同分组的差异，而绘制火山图最起码的是需要**基因名**、**差异倍数**和**P值**三列数据。

随着生信的发展，越来越多的包被开发出来了，有很多包可以用来画火山图，但是基本底层都是基于**ggplot2**，而只有熟悉**ggplot2**的运用，才能更好的了解画图的意义，所以这个教程先介绍用**ggplot2**如何画火山图，后期我再介绍一下别的包如何画火山图。

---

这里用我在`GEO2R`计算出来的一个数据，命名为demo.csv，算法用的是`limma`（下次有机会给大家单独介绍如何计算差异基因的几种办法），首先加载数据，原始基因有5万多个，见下表

``` 
demo<-read.csv('~/demo.csv')
```




Table: Demo数据

|Gene.symbol             | adj.P.Val| P.Value|  logFC|
|:-----------------------|---------:|-------:|------:|
|NA                      |     0e+00|       0| -1.890|
|NA                      |     0e+00|       0| -3.290|
|NA                      |     0e+00|       0| -1.130|
|EPS8L3                  |     0e+00|       0| -2.170|
|NA                      |     1e-04|       0| -3.540|
|NA                      |     1e-04|       0| -1.290|
|NPAS2                   |     1e-04|       0| -1.590|
|NA                      |     1e-04|       0| -2.670|
|CAPN5                   |     2e-04|       0| -3.200|
|ATF3                    |     2e-04|       0| -1.760|
|BCAS1                   |     2e-04|       0| -3.640|
|NA                      |     2e-04|       0| -1.750|
|ZSCAN4                  |     2e-04|       0| -1.670|
|PIGL                    |     3e-04|       0| -1.200|
|DENND2D                 |     3e-04|       0| -1.700|
|POSTN                   |     3e-04|       0|  4.790|
|NA                      |     4e-04|       0| -1.790|
|TMEM51-AS1              |     4e-04|       0| -2.110|
|ZNF440                  |     4e-04|       0| -1.750|
|ZSCAN4                  |     4e-04|       0| -1.660|
|NA                      |     4e-04|       0| -0.695|
|PCMTD1                  |     5e-04|       0| -1.200|
|NA                      |     5e-04|       0| -0.852|
|SLC14A1                 |     5e-04|       0| -5.200|
|NA                      |     5e-04|       0| -2.070|
|DEGS1                   |     5e-04|       0|  1.720|
|NSMAF                   |     5e-04|       0| -1.160|
|LOC105375839            |     5e-04|       0| -0.943|
|POSTN                   |     6e-04|       0|  4.890|
|CASC4                   |     7e-04|       0| -1.150|
|NA                      |     7e-04|       0| -1.910|
|NA                      |     7e-04|       0| -1.380|
|ZNF281                  |     7e-04|       0|  1.240|
|NA                      |     8e-04|       0| -1.070|
|NA                      |     8e-04|       0| -0.881|
|TCF12                   |     9e-04|       0| -0.787|
|CD9                     |     9e-04|       0| -1.210|
|MIR29C///MIR29B2        |     9e-04|       0| -2.230|
|PLA2G4B///JMJD7-PLA2G4B |     9e-04|       0| -1.080|
|GPX8                    |     9e-04|       0|  2.910|
|RAB23                   |     9e-04|       0|  1.550|
|SLC14A1                 |     9e-04|       0| -4.500|
|NA                      |     9e-04|       0| -1.360|
|SPPL2B                  |     9e-04|       0| -0.588|
|COPRS                   |     9e-04|       0|  1.250|
|KDELC2                  |     9e-04|       0|  1.520|
|FAM219B                 |     9e-04|       0| -1.290|
|TMEM51                  |     9e-04|       0| -1.290|
|MPZL3                   |     1e-03|       0| -1.310|
|TRIM31                  |     1e-03|       0| -2.660|

-   可以看到有些基因没有名字，而且还有一些基因重复，这里我们可以去除NA值和重复值，一下子接近三万个基因就没有了。


```r
demo <- demo[!duplicated(demo$Gene.symbol), ]
```

我们看看前50列数据，是不是干净了很多：


Table: 去除NA值和重复值后的前50列Demo数据

|Gene.symbol  | adj.P.Val| P.Value|  logFC|
|:------------|---------:|-------:|------:|
|NA           |     0e+00|       0| -1.890|
|EPS8L3       |     0e+00|       0| -2.170|
|NPAS2        |     1e-04|       0| -1.590|
|CAPN5        |     2e-04|       0| -3.200|
|ATF3         |     2e-04|       0| -1.760|
|BCAS1        |     2e-04|       0| -3.640|
|ZSCAN4       |     2e-04|       0| -1.670|
|PIGL         |     3e-04|       0| -1.200|
|DENND2D      |     3e-04|       0| -1.700|
|POSTN        |     3e-04|       0|  4.790|
|TMEM51-AS1   |     4e-04|       0| -2.110|
|ZNF440       |     4e-04|       0| -1.750|
|PCMTD1       |     5e-04|       0| -1.200|
|SLC14A1      |     5e-04|       0| -5.200|
|DEGS1        |     5e-04|       0|  1.720|
|NSMAF        |     5e-04|       0| -1.160|
|LOC105375839 |     5e-04|       0| -0.943|
|CASC4        |     7e-04|       0| -1.150|
|ZNF281       |     7e-04|       0|  1.240|
|TCF12        |     9e-04|       0| -0.787|

---

接下来我们需要确定差异倍数和P值，一般建议是log2FC=1，P\<0.05，但这里我想演示一种动态的差异倍数，即差异倍数的**均数+2倍标准差**的形式，这样的好处是结果更为科学，而P值可以选择p值也可以选择校正p值，这里我们选择更为科学的校正p值。

## 确定差异表达倍数


```r
logFC_t <- with(demo, mean(abs(logFC)) + 2 * sd(abs(logFC)))
## 这里使用的是动态阈值，也可以自定义例如logFC=1或2这种静态阈值
logFC_t <- round(logFC_t, 3)  # 取前三位小数，这步也可以不运行
logFC_t  #看一下动态阈值
```

```
## [1] 1.252
```

可以看到我们的动态差异倍数的阈值是1.25，接近1，但是貌似更合理了。

## 确定上下调表达基因


```r
demo$Change = as.factor(ifelse(demo$adj.P.Val < 0.05 & abs(demo$logFC) > logFC_t,

                              ifelse(demo$logFC > logFC_t ,'UP','DOWN'),'STABLE'))
## 定义校正p值<0.05和差异倍数大于动态阈值的结果
table(demo$Change) #看一下上下调基因的数量
```

```
## 
##   DOWN STABLE     UP 
##    222  21668    298
```

-   可以看到上调基因是298个，下调基因221，稳定基因是21668个.

这个时候我们也先可以定义一个标题，加上我们想显示具体的上下调的基因数。


```r
this_tile <- paste0("Volcano plot for demo", "\nCutoff for Log2FC is ", round(logFC_t,
  3), "\nThe number of up genes is ", nrow(demo[demo$Change == "UP", ]), "\nThe number of down genes is ",
  nrow(demo[demo$Change == "DOWN", ]))
```

## 画一个没有基因标签的火山图

用**ggplot2**进行画图，结果见Figure \@ref(fig:fig1)所示


```r
library(ggplot2)
ggplot(demo, aes(x=logFC, y=-log10(adj.P.Val),color=Change)) + 
  geom_point(alpha=0.4, size=2) +  # 设置点的透明度和大小
  theme_bw(base_size = 12) +  #设置一个主题背景
  xlab("Log2(Fold change)") + # x轴名字
  ylab("-Log10(P.adj)") + # y轴名字
  theme(plot.title = element_text(size=15,hjust = 0.5)) + 
  scale_colour_manual(values = c('steelblue','gray','brown')) + # 各自的颜色
  geom_hline(yintercept = -log10(0.05), lty = 4) + #定义p值和线形
  geom_vline(xintercept = c(-logFC_t, logFC_t), lty = 4)+ #定义差异倍数和线形
  labs(title = this_tile) #加上题目
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-10-05-ggplot2-volcano/ggplot2-volcano/fig1-1.png" alt="无基因标签的火山图"  />
<p class="caption">无基因标签的火山图</p>
</div>

## 画一个带基因标签的火山图

有时候，我们想看一些显著基因的名字，这时候我们可以用**ggrepel**这个包定义一些，比如我们想显示差异倍数的绝对值\>2，校正p值\<0.0005的基因，结果可见下表所示：


```r
demo$label <- ifelse(demo$adj.P.Val < 5e-04 & abs(demo$logFC) >= 2, demo$Gene.symbol,
  "")
```


Table: 定义显示标签的基因

|Gene.symbol | adj.P.Val| P.Value| logFC|Change |label      |
|:-----------|---------:|-------:|-----:|:------|:----------|
|NA          |     0e+00|       0| -1.89|DOWN   |           |
|EPS8L3      |     0e+00|       0| -2.17|DOWN   |EPS8L3     |
|NPAS2       |     1e-04|       0| -1.59|DOWN   |           |
|CAPN5       |     2e-04|       0| -3.20|DOWN   |CAPN5      |
|ATF3        |     2e-04|       0| -1.76|DOWN   |           |
|BCAS1       |     2e-04|       0| -3.64|DOWN   |BCAS1      |
|ZSCAN4      |     2e-04|       0| -1.67|DOWN   |           |
|PIGL        |     3e-04|       0| -1.20|STABLE |           |
|DENND2D     |     3e-04|       0| -1.70|DOWN   |           |
|POSTN       |     3e-04|       0|  4.79|UP     |POSTN      |
|TMEM51-AS1  |     4e-04|       0| -2.11|DOWN   |TMEM51-AS1 |
|ZNF440      |     4e-04|       0| -1.75|DOWN   |           |
|PCMTD1      |     5e-04|       0| -1.20|STABLE |           |
|SLC14A1     |     5e-04|       0| -5.20|DOWN   |SLC14A1    |
|DEGS1       |     5e-04|       0|  1.72|UP     |           |

用下面的代码进行画图，结果见Figure \@ref(fig:fig2)所示。


```r
library(ggrepel)
ggplot(demo, aes(x = logFC, y = -log10(adj.P.Val), color = Change)) + geom_point(alpha = 0.4,
  size = 2) + theme_bw(base_size = 12) + xlab("Log2(Fold change)") + ylab("-Log10(P.adj)") +
  theme(plot.title = element_text(size = 15, hjust = 0.5)) + scale_colour_manual(values = c("steelblue",
  "gray", "brown")) + geom_hline(yintercept = -log10(0.05), lty = 4) + geom_vline(xintercept = c(-logFC_t,
  logFC_t), lty = 4) + labs(title = this_tile) + geom_label_repel(data = demo,
  aes(label = label), size = 3, box.padding = unit(0.5, "lines"), point.padding = unit(0.8,
    "lines"), segment.color = "black", show.legend = FALSE, max.overlaps = 10000)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-10-05-ggplot2-volcano/ggplot2-volcano/fig2-1.png" alt="无基因标签的火山图"  />
<p class="caption">无基因标签的火山图</p>
</div>

---

这样的颜值还是在线的，配色可以自己多试。后期我们再用这个数据介绍别的包画火山图。
