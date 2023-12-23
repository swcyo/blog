---
title: R语言处理PCR数据，一步画柱状图、添加显著性标志并实现截断
author: R package build
date: '2021-09-01'
slug: r语言处理pcr数据-一步画柱状图-添加显著性标志并实现截断
categories:
  - 教程
  - R
tags:
  - 教程
  - R
  - PCR
from_Rmd: yes
---

临床上经常需要处理PCR数据，但是一般下机给的都是Ct值（或者Cq值），这时候我们需要用一个相对恒定的内参基因（一般是GAPDH或actin）去校正，一般而言，Ct值越大，其实代表他的相对表达量最少。

现在最普遍使用的方法是**-2^∆∆Ct^**法，当然还有针对PCR array的专门算法，这里不说。

PCR数据要有三列，一列是组名，一列是内参基因的Ct值，一列是目的基因的Ct值，计算方法是-2^∆∆Ct^ 法，实现一步出图用的是`ggpubr`，实现截断则是Y叔出手的`ggbreak`

\- 比如有下面这个表，保存并定义为`PCR.csv`，放桌面上：

| group   | GAPDH | XXX   |
|---------|-------|-------|
| control | 12.17 | 23.76 |
| control | 11.88 | 23.79 |
| control | 11.92 | 23.57 |
| treat   | 11.81 | 19.06 |
| treat   | 11.66 | 19.14 |
| treat   | 11.59 | 18.81 |

## 读取表格，并计算相对mRNA定量

```
PCR <- read.csv("~/Desktop/PCR.csv") #读表
PCR$dct=PCR$XXX-PCR$GAPDH  ##目的基因Ct-内存基因Ct，即∆Ct
PCR$ddct=PCR$dct-mean(PCR$dct[1:3])  ##∆Ct-对照组Ct均值，即∆∆Ct
PCR$mrna=2^-PCR$ddct  ##取-∆∆Ct的2次放，即-2^∆∆Ct
```
  


很快的，我们就计算好了，表格现在是这样的。


|group   | GAPDH|   XXX|   dct|    ddct|    mrna|
|:-------|-----:|-----:|-----:|-------:|-------:|
|control | 12.17| 23.76| 11.59| -0.1267|  1.0918|
|control | 11.88| 23.79| 11.91|  0.1933|  0.8746|
|control | 11.92| 23.57| 11.65| -0.0667|  1.0473|
|treat   | 11.81| 19.06|  7.25| -4.4667| 22.1106|
|treat   | 11.66| 19.14|  7.48| -4.2367| 18.8523|
|treat   | 11.59| 18.81|  7.22| -4.4967| 22.5752|

## 一步基础作图

我们需要的数值都有了，一步出图可以用ggpubr，当然也可以用ggplot2，这个可以看我之前关于柱状图的教程，我们先画个基础图


```r
library(ggpubr) 
p<-ggbarplot(PCR,
        'group',
        'mrna',
        fill = 'group',# 按组填充颜色，当然如果喜欢单色，就用‘black’
        color = 'group', # 按组填充颜色
        palette = "jco",  ## "npg", "aaas", "lancet"等主题任意选
        add = "mean_sd", #计算均数和标准差
        xlab = F,ylab = 'Relative mRNA expression',legend='none',
        ggtheme = theme_bw()) #选一个自己喜欢的主题
p
```

![plot of chunk unnamed-chunk-3](/figures/course/2021-09-01-r语言处理pcr数据-一步画柱状图-添加显著性标志并实现截断/index/unnamed-chunk-3-1.png)

可以看到差别很大，可以截断一下

PS：不知道为什么我这一版（0.04版）的截断结果变样了，哪天去Y叔那投诉一下


```r
library(ggbreak)
p + scale_y_break(c(1.5, 15))
```

![plot of chunk unnamed-chunk-4](/figures/course/2021-09-01-r语言处理pcr数据-一步画柱状图-添加显著性标志并实现截断/index/unnamed-chunk-4-1.png)

## 科学的统计方法

我们知道简单的t检验或者非参数检验都是偷懒的办法，两独立样本我们需要先做正态检验，，然后是方差齐性检验，最后才是选择哪种检验方法。

我们首先可以看一下数据的分布情况，用**psych**包里的`describeBy()`函数，可以分别统计各组数据


```r
library(psych)
describeBy(PCR[c(1, 6)], PCR$group)  # 我们只看分组和最终的mrna结果
```

```
## 
##  Descriptive statistics by group 
## group: control
##        vars n mean   sd median trimmed  mad  min  max range  skew kurtosis   se
## group*    1 3    1 0.00   1.00       1 0.00 1.00 1.00  0.00   NaN      NaN 0.00
## mrna      2 3    1 0.11   1.05       1 0.07 0.87 1.09  0.22 -0.32    -2.33 0.07
## ------------------------------------------------------------ 
## group: treat
##        vars n  mean   sd median trimmed  mad   min   max range  skew kurtosis
## group*    1 3  2.00 0.00   2.00    2.00 0.00  2.00  2.00  0.00   NaN      NaN
## mrna      2 3 21.18 2.03  22.11   21.18 0.69 18.85 22.58  3.72 -0.36    -2.33
##          se
## group* 0.00
## mrna   1.17
```

可以很快的看到control和treat组的均数、标准差、最大值、最小值、组距、标准误等结果。

### 正态性检验

一般两组的比较推荐`shapiro检验`，这里我们要主要不是做整体数据的正态性检验，e而应该是把每一个变量按照分组分别进行正态检验，如果两组里面哪怕有一组正态分布和一组不正态分布，那么它也是不正态分布，这种情况也要选择非参数检验。

下面的函数是分别统计，可以用`tapply()`，也可以用`by()`，结果可以发现两组的p值都\>0.5，也就是说都符合正态分布，这时候我们还要看两组数据有没有方差齐性。


```r
with(PCR, tapply(PCR$mrna, group, shapiro.test))
```

```
## $control
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.9, p-value = 0.4
## 
## 
## $treat
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.84, p-value = 0.2
```

### 方差齐性检验

我们可以选择`var.test()`做方差齐性检验，也就是F检验。

-   如果方差具有齐性，我们直接选择t检验，

-   如果方差不具有齐性，则要选择校正过的近似t检验


```r
var.test(mrna ~ group, data = PCR)$p.value  #如果想看完整的结果，可以把$p.value去掉
```

```
## [1] 0.006376
```

我们可以看到这里的p值<0.05，说明了这个数据的方差不具有齐性，那么我们就要用校正的t检验

### 校正t检验

由于F检验的P值小于0.05，我们选择校正的t检验，在R语言里，默认的其实就是校正的t检验，所以对于方差具有齐性的数据，一定要注意，这里不要偷懒，一定要用完整的代码，不用总是默认；


```r
t.test(mrna ~ group,
       data = PCR,
       var.equal = FALSE, #默认的就是FALSE，但是我们要记住如果方差齐就要改TURE
       alternative = "two.sided") #双尾事件，如果是单尾，可以用greater或less
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  mrna by group
## t = -17, df = 2, p-value = 0.003
## alternative hypothesis: true difference in means between group control and group treat is not equal to 0
## 95 percent confidence interval:
##  -25.19 -15.16
## sample estimates:
## mean in group control   mean in group treat 
##                 1.005                21.179
```

结果可知用的是Welch Two Sample t-test，也就是**近似t检验**，这个p值小于0.05，差异有显著性意义，这个时候，我们在回过来给之前的图加显著性标识，一定要知道选择的统计方法是什么。

------------------------------------------------------------------------

由于默认的t检验，就是校正的t检验，这里我们继续用ggpubr自带的统计学方法，当然也可以事先检验验证一下，代码是:


```r
compare_means(mrna ~ group, data = PCR, method = "t.test")
```

```
## # A tibble: 1 × 8
##   .y.   group1  group2       p  p.adj p.format p.signif method
##   <chr> <chr>   <chr>    <dbl>  <dbl> <chr>    <chr>    <chr> 
## 1 mrna  control treat  0.00327 0.0033 0.0033   **       T-test
```

如果是不需要校正的t检验，也就是正规的t检验，方法其实是anova。

> compare_means(mrna\~group, data=PCR, method = 'anova')

最后加个显著性标识作图，顺便再添加个数据点


```r
p+stat_compare_means(aes(label = ..p.signif..),  
                     comparisons = list(c('control','treat')),  
                     method = 't.test')+#正规的t检验好像是anova
  geom_jitter(color='black',size=2) 
```

![plot of chunk unnamed-chunk-10](/figures/course/2021-09-01-r语言处理pcr数据-一步画柱状图-添加显著性标志并实现截断/index/unnamed-chunk-10-1.png)


这样t检验的统计学方法学会了吗？
