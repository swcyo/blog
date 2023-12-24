---
title: 用ggpubr画火山图
author: 欧阳松
date: '2021-10-05'
slug: ggpubr-volcano
categories:
  - 火山图
  - ggpubr
tags:
  - 火山图
enableToc: no
from_Rmd: yes
---

我们介绍了用**ggplot2**画火山图，接着介绍用**ggpubr**画火山图，其实ggpubr是ggplot2的扩展包，底层被限制死了，一般是不大推荐的，但是如果想偷懒的画，用ggpubr的散点图（`ggscatter`）也可以画一个简单的火山图。

我们继续用之前的示例数据格式进行演示，同法进行动态差异倍数以及上下调基因的计算，计算的结果见下表所示。

    demo<-read.csv('~/demo.csv')
    logFC_t <- with(demo,mean(abs(logFC)) + 2*sd(abs(logFC))) 
    demo$Change = as.factor(ifelse(demo$adj.P.Val < 0.05 & abs(demo$logFC) > logFC_t,
                                  ifelse(demo$logFC > logFC_t ,'UP','DOWN'),'STABLE'))


Table: 差异表达基因

|   |Gene.symbol  | adj.P.Val| P.Value|  logFC|Change |
|:--|:------------|---------:|-------:|------:|:------|
|1  |             |     0e+00|       0| -1.890|DOWN   |
|4  |EPS8L3       |     0e+00|       0| -2.170|DOWN   |
|7  |NPAS2        |     1e-04|       0| -1.590|DOWN   |
|9  |CAPN5        |     2e-04|       0| -3.200|DOWN   |
|10 |ATF3         |     2e-04|       0| -1.760|DOWN   |
|11 |BCAS1        |     2e-04|       0| -3.640|DOWN   |
|13 |ZSCAN4       |     2e-04|       0| -1.670|DOWN   |
|14 |PIGL         |     3e-04|       0| -1.200|STABLE |
|15 |DENND2D      |     3e-04|       0| -1.700|DOWN   |
|16 |POSTN        |     3e-04|       0|  4.790|UP     |
|18 |TMEM51-AS1   |     4e-04|       0| -2.110|DOWN   |
|19 |ZNF440       |     4e-04|       0| -1.750|DOWN   |
|22 |PCMTD1       |     5e-04|       0| -1.200|STABLE |
|24 |SLC14A1      |     5e-04|       0| -5.200|DOWN   |
|26 |DEGS1        |     5e-04|       0|  1.720|UP     |
|27 |NSMAF        |     5e-04|       0| -1.160|STABLE |
|28 |LOC105375839 |     5e-04|       0| -0.943|STABLE |
|30 |CASC4        |     7e-04|       0| -1.150|STABLE |
|33 |ZNF281       |     7e-04|       0|  1.240|STABLE |
|36 |TCF12        |     9e-04|       0| -0.787|STABLE |

ggpubr的缺点是不能对y轴进行-log10的计算，所以我们首先要重新定义一个p值，简单的定义后，直接使用ggscatter画图，见Figure \@ref(fig:fig1)所示。


```r
library(ggpubr)
```

```
## Loading required package: ggplot2
```

```r
demo$p = -log10(demo$adj.P.Val)
ggscatter(demo, x = "logFC", y = "p", color = "Change", size = 0.5, xlab = "Log2(Fold change)",
  ylab = "-Log10(adj.P.Val)", palette = c("steelblue", "gray", "brown"), ggtheme = theme_bw())
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-10-05-ggpubr-volcano/ggpubr-volcano/fig1-1.png" alt="简单的散点图"  />
<p class="caption">简单的散点图</p>
</div>

我们也可以加上几点线，见Figure \@ref(fig:fig2)所示。


```r
ggscatter(demo, x = "logFC", y = "p", color = "Change", size = 0.5, xlab = "Log2(Fold change)",
  ylab = "-Log10(adj.P.Val)", palette = c("steelblue", "gray", "brown"), ggtheme = theme_bw()) +
  geom_hline(yintercept = -log10(0.05), lty = 4) + geom_vline(xintercept = c(-logFC_t,
  logFC_t), lty = 4)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-10-05-ggpubr-volcano/ggpubr-volcano/fig2-1.png" alt="火山图"  />
<p class="caption">火山图</p>
</div>
