---
title: 从原始数据到单因素方差分析，再到组间两两比较
author: 欧阳松
date: '2022-11-18'
slug: anova2
categories:
  - 单因素方差分析
  - ANOVA
tags:
  - 多因素方差分析
  - 多个样本两两比较
  - ANOVA
from_Rmd: yes
---

前面写了两期关于ANOVA的教程

[使用rstatix包快速进行ANOVA并可视化](/course/2022-11-11-rstatix-anova/)

[R到底如何做多组样本均数比较及两两比较？](/course/anova/)

其中我特别推荐用**rstatix**这个包进行处理，他几乎包含了所有的统计函数，今天在微信公众好看了另一个教程[R语言------从原始数据到单因素方差分析，再到组间两两比较，简直保姆级教程！](https://mp.weixin.qq.com/s/yNGU26deKwxR5yZ4n7byFw)。诚然，之前看了很多教程都没有将正态性检验和方差齐性检验，这个教程还是很不错的，今天使用之前的数据复现一下结果。

## 构建模拟数据

构建一个包含A、B、C、D四组数据，每组30个变量数据。


```r
df <- data.frame(A = c(3.53, 4.59, 4.34, 2.66, 3.59, 3.13, 2.64, 2.56, 3.5, 3.25,
  3.3, 4.04, 3.53, 3.56, 3.85, 4.07, 3.52, 3.93, 4.19, 2.96, 1.37, 3.93, 2.33,
  2.98, 4, 3.55, 2.96, 4.3, 4.16, 2.59), B = c(2.42, 3.36, 4.32, 2.34, 2.68, 2.95,
  1.56, 3.11, 1.81, 1.77, 1.98, 2.63, 2.86, 2.93, 2.17, 2.72, 2.65, 2.22, 2.9,
  2.97, 2.36, 2.56, 2.52, 2.27, 2.98, 3.72, 2.8, 3.57, 4.02, 2.31), C = c(2.86,
  2.28, 2.39, 2.28, 2.48, 2.28, 3.21, 2.23, 2.32, 2.68, 2.66, 2.32, 2.61, 3.64,
  2.58, 3.65, 2.66, 3.68, 2.65, 3.02, 3.48, 2.42, 2.41, 2.66, 3.29, 2.7, 3.04,
  2.81, 1.97, 1.68), D = c(0.89, 1.06, 1.08, 1.27, 1.63, 1.89, 1.19, 2.17, 2.28,
  1.72, 1.98, 1.74, 2.16, 3.37, 2.97, 1.69, 0.94, 2.11, 2.81, 2.52, 1.31, 2.51,
  1.88, 1.41, 3.19, 1.92, 2.47, 1.02, 2.1, 3.71))
# df
```


|    A|    B|    C|    D|
|----:|----:|----:|----:|
| 3.53| 2.42| 2.86| 0.89|
| 4.59| 3.36| 2.28| 1.06|
| 4.34| 4.32| 2.39| 1.08|
| 2.66| 2.34| 2.28| 1.27|
| 3.59| 2.68| 2.48| 1.63|
| 3.13| 2.95| 2.28| 1.89|
| 2.64| 1.56| 3.21| 1.19|
| 2.56| 3.11| 2.23| 2.17|
| 3.50| 1.81| 2.32| 2.28|
| 3.25| 1.77| 2.68| 1.72|
| 3.30| 1.98| 2.66| 1.98|
| 4.04| 2.63| 2.32| 1.74|
| 3.53| 2.86| 2.61| 2.16|
| 3.56| 2.93| 3.64| 3.37|
| 3.85| 2.17| 2.58| 2.97|
| 4.07| 2.72| 3.65| 1.69|
| 3.52| 2.65| 2.66| 0.94|
| 3.93| 2.22| 3.68| 2.11|
| 4.19| 2.90| 2.65| 2.81|
| 2.96| 2.97| 3.02| 2.52|
| 1.37| 2.36| 3.48| 1.31|
| 3.93| 2.56| 2.42| 2.51|
| 2.33| 2.52| 2.41| 1.88|
| 2.98| 2.27| 2.66| 1.41|
| 4.00| 2.98| 3.29| 3.19|
| 3.55| 3.72| 2.70| 1.92|
| 2.96| 2.80| 3.04| 2.47|
| 4.30| 3.57| 2.81| 1.02|
| 4.16| 4.02| 1.97| 2.10|
| 2.59| 2.31| 1.68| 3.71|

## 数据处理

由于数据是各组的宽数据，我们需要先将数据进行转换。

> 我们看到里面有个数值不正确，也可以在R语言中进行更改，使用`edit()`函数，可以对数据进行更改

```         
# 更改数据
df <- edit(df) ## 该函数可以直接修改数据，很方便
```

之前使用的是**tidyr**或者**tidyverse**包可以**将宽数据转化为长数据**。本文介绍基础包自带的**stack()**函数即可转换


```r
# 宽数据转换长数据
df2 <- stack(df)
```

转换后的列名是values和ind，我们可以使用`names()`函数对数据集的列名进行重命名，并查看


```r
names(df2) <- c("value", "group")
## View(df2)
```

### 正态性检验

跟之前教程一样，可以使用使用基础包的`tapply()`函数可以分组检验，正态性检验方法是`shapiro.test`

由于使用函数需要调研数据，为了方便统计，可以直接使用`attach()`函数将数据读取进R，方便调取


```r
attach(df2)
```

使用`tapply()`函数对数据进行正态性检测，在下方，我们可以观察到p值，如果p值大于0.05，我们则认为数据符合正态分布，当然我们的分析逻辑如下：

> 1\. **数据为正态性，则继续进行方差分析**；
>
> 2\. **如果数据不符合正态，则直接进行非参数检验分析**。


```r
tapply(value, group, shapiro.test)
```

```
## $A
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.95, p-value = 0.2
## 
## 
## $B
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.97, p-value = 0.5
## 
## 
## $C
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.94, p-value = 0.1
## 
## 
## $D
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.96, p-value = 0.3
```

当然，我们也可以绘制QQ图看一下数据分布趋势，见Figure \@ref(fig:qq)所示。


```r
ggpubr::ggqqplot(df2, "value", facet.by = "group")
```

<div class="figure" style="text-align: center">
<img src="/figures/course/anova2/anova2/qq-1.png" alt="Q-Q图"  />
<p class="caption">Q-Q图</p>
</div>

### 方差齐性检验

在各个组数据为正态性数据的基础上，我们要继续看各个组之间的方差是否齐。

> 1.  **如果方差齐，则继续进行组间两两比较，也就是单因素方差分析。**
>
> 2.  **如果组间方差不齐，则直接进行非参数检验。**

如果数据呈正态性分析，我们可以直接使用基础包`bartlett.test()`直接进行运算


```r
bartlett.test(value ~ group)
```

```
## 
## 	Bartlett test of homogeneity of variances
## 
## data:  value by group
## Bartlett's K-squared = 5.2, df = 3, p-value = 0.2
```

当然，对于广泛的情况，我们还可以用Levene检验，这个需要需要安装**car**这个包，进行计算前，最好将将组别的数据形式转换为因子形式，ps: `stack()`函数已经将组别直接转换成了因子形式。


```r
## install.packages('car')
car::leveneTest(value ~ group)
```

```
## Levene's Test for Homogeneity of Variance (center = median)
##        Df F value Pr(>F)
## group   3    1.49   0.22
##       116
```

可以看到两种方法的结果的p值都\>0.05，也就是说都符合方差齐性分析，这时我们就可以进行方差分析的正式检验了，我们可以使用基础包的aov函数就行方差分析模型构建。

> 如果p值小于0.05，提示组间方差不齐，那么该数据是应该使用非参数检验进行分析。

### 组间整体显著性检验

我们可以使用基础包的aov函数就行方差分析模型构建。结果可以看到p为1.67e-12，提示差异有统计学意义。


```r
# 查看一下组间是否具有显著性
AOV <- aov(value ~ group)
summary(AOV)
```

```
##              Df Sum Sq Mean Sq F value  Pr(>F)    
## group         3   32.2   10.72    24.9 1.7e-12 ***
## Residuals   116   50.0    0.43                    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

### 组间两两比较

由于方差具有齐性，我们可以使用基础包的`TukeyHSD()`函数进行两两比较。可以看到，出来c-b两组外，其余均有显著意义。


```r
TukeyHSD(AOV)
```

```
##   Tukey multiple comparisons of means
##     95% family-wise confidence level
## 
## Fit: aov(formula = value ~ group)
## 
## $group
##         diff     lwr     upr  p adj
## B-A -0.71500 -1.1567 -0.2733 0.0003
## C-A -0.73233 -1.1741 -0.2906 0.0002
## D-A -1.46400 -1.9057 -1.0223 0.0000
## C-B -0.01733 -0.4591  0.4244 0.9996
## D-B -0.74900 -1.1907 -0.3073 0.0001
## D-C -0.73167 -1.1734 -0.2899 0.0002
```

### 非参数检验

在整体数据不符合正态分布，或者组间方差不齐的条件下，我们进行非参数检验。通过`kruskal.test()函数`进行非参数检验，查看后，我们发现，组间也具有显著性差异。


```r
kruskal.test(value ~ group)
```

```
## 
## 	Kruskal-Wallis rank sum test
## 
## data:  value by group
## Kruskal-Wallis chi-squared = 45, df = 3, p-value = 9e-10
```

对于非参数检验的事后检验，我们可以使用**PMCMRplus**这个包的`bwsAllPairsTest()`函数直接进行组间的两两比较。没有安装的可以先进行安装包，然后加载使用。通过查看后面标注的\*，就可以发现各个组间的差异了。


```r
# 直接进行组间的两两比较 install.packages('PMCMRplus')
library(PMCMRplus)
compare <- bwsAllPairsTest(value ~ group, data = df2)
summary(compare)
```

```
## 
## 	Pairwise comparisons using BWS All-Pairs Test
```

```
## data: value by group
```

```
## alternative hypothesis: two.sided
```

```
## P value adjustment method: holm
```

```
## H0
```

```
##            B value Pr(>|B|)    
## B - A == 0   8.556  0.00013 ***
## C - A == 0  10.448  3.5e-05 ***
## D - A == 0  22.602  < 2e-16 ***
## C - B == 0   0.489  0.75805    
## D - B == 0   9.223  9.4e-05 ***
## D - C == 0  11.781  1.1e-05 ***
```

```
## ---
```

```
## Signif. codes: 0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

可以看到，在这个数据里，参数检验和非参数检验的结果基本都一致。

------------------------------------------------------------------------

## 数据可视化

使用**rstatix**包可以计算结果，并自动进行两两比较和添加p值和xy轴的位置


```r
## rstatix进行统计学计算
library(rstatix)
### 进行组间整体显著性检验
res.aov <- anova_test(df2, value ~ group)
### 进行组间两两比较
pwc <- tukey_hsd(df2, value ~ group)
## 自动进行各组比较和添加p值坐标轴位置
pwc <- pwc %>%
  add_xy_position(x = "group")
```

使用**ggpubr**包可以快速绘图，比如我们画一个boxplot，见Figure \@ref(fig:box)所示。


```r
## ggpubr进行结果可视化
library(ggpubr)
ggboxplot(df2, x = "group", y = "value",
          add = 'jitter',color = 'group',
          palette = 'lancet', #设置lancet配色
          ggtheme = theme_bw(), # 设置背景
          legend='none' ## 去除分组标签
          ) +stat_pvalue_manual(pwc, hide.ns = TRUE) +
  labs( subtitle = get_test_label(res.aov, detailed = TRUE), 
        caption = get_pwc_label(pwc))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/anova2/anova2/box-1.png" alt="Boxplot"  />
<p class="caption">Boxplot</p>
</div>

我们可以画柱状图，见Figure \@ref(fig:bar)所示。


```r
ggbarplot(df2, x = "group", y = "value",
          add = 'mean_sd',color = 'group',
          palette = 'lancet', #设置lancet配色
          ggtheme = theme_bw(), # 设置背景
          legend='none' ## 去除分组标签
          ) +stat_pvalue_manual(pwc, hide.ns = TRUE) +
  labs( subtitle = get_test_label(res.aov, detailed = TRUE), 
        caption = get_pwc_label(pwc))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/anova2/anova2/bar-1.png" alt="Barplot"  />
<p class="caption">Barplot</p>
</div>
