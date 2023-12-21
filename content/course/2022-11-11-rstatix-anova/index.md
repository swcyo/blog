---
title: 使用rstatix包快速进行ANOVA并可视化
author: 欧阳松
date: '2022-11-11'
slug: [rstatix-anova]
categories:
  - 单因素方差分析
  - anova
  - ANOVA
tags:
  - 多个样本两两比较
  - 多因素方差分析
  - ANOVA
from_Rmd: yes
---

前面写了[R到底如何做多组样本均数比较及两两比较？](/course/anova/)这个教程，从正态性检验、方差齐性检验和事后检验进行了系统介绍，但是代码比较繁琐，而且还设计了多个R包，本文介绍只用**rstatix**这一个包进行ANOVA分析。教程也可以参考[ANOVA in R: The Ultimate Guide - Datanovia](https://www.datanovia.com/en/lessons/anova-in-r/)这篇文章。

我们可以用经典的内置**ToothGrowth**数据。

## 构建数据


```r
data("ToothGrowth")  ## 加载数据
df <- ToothGrowth  ## 重命名数据
df$dose <- as.factor(df$dose)  ## 将剂量dose设置为因子
```


|  len|supp |dose |
|----:|:----|:----|
|  4.2|VC   |0.5  |
| 11.5|VC   |0.5  |
|  7.3|VC   |0.5  |
|  5.8|VC   |0.5  |
|  6.4|VC   |0.5  |
| 10.0|VC   |0.5  |
| 11.2|VC   |0.5  |
| 11.2|VC   |0.5  |
|  5.2|VC   |0.5  |
|  7.0|VC   |0.5  |
| 16.5|VC   |1    |
| 16.5|VC   |1    |
| 15.2|VC   |1    |
| 17.3|VC   |1    |
| 22.5|VC   |1    |
| 17.3|VC   |1    |
| 13.6|VC   |1    |
| 14.5|VC   |1    |
| 18.8|VC   |1    |
| 15.5|VC   |1    |
| 23.6|VC   |2    |
| 18.5|VC   |2    |
| 33.9|VC   |2    |
| 25.5|VC   |2    |
| 26.4|VC   |2    |
| 32.5|VC   |2    |
| 26.7|VC   |2    |
| 21.5|VC   |2    |
| 23.3|VC   |2    |
| 29.5|VC   |2    |
| 15.2|OJ   |0.5  |
| 21.5|OJ   |0.5  |
| 17.6|OJ   |0.5  |
|  9.7|OJ   |0.5  |
| 14.5|OJ   |0.5  |
| 10.0|OJ   |0.5  |
|  8.2|OJ   |0.5  |
|  9.4|OJ   |0.5  |
| 16.5|OJ   |0.5  |
|  9.7|OJ   |0.5  |
| 19.7|OJ   |1    |
| 23.3|OJ   |1    |
| 23.6|OJ   |1    |
| 26.4|OJ   |1    |
| 20.0|OJ   |1    |
| 25.2|OJ   |1    |
| 25.8|OJ   |1    |
| 21.2|OJ   |1    |
| 14.5|OJ   |1    |
| 27.3|OJ   |1    |
| 25.5|OJ   |2    |
| 26.4|OJ   |2    |
| 22.4|OJ   |2    |
| 24.5|OJ   |2    |
| 24.8|OJ   |2    |
| 30.9|OJ   |2    |
| 26.4|OJ   |2    |
| 27.3|OJ   |2    |
| 29.4|OJ   |2    |
| 23.0|OJ   |2    |

## 数据统计

    library(rstatix) 
    library(tidyr)
    ## 按dose分组计算各组的len均值和标准差
    df %>%
      group_by(dose) %>%
      get_summary_stats(len, type = "mean_sd") 


|dose |variable |  n|  mean|    sd|
|:----|:--------|--:|-----:|-----:|
|0.5  |len      | 20| 10.61| 4.500|
|1    |len      | 20| 19.73| 4.415|
|2    |len      | 20| 26.10| 3.774|

### 简单可视化数据


```r
library(ggpubr)
ggboxplot(df, x = "dose", y = "len")
```

![plot of chunk unnamed-chunk-4](/figures/course/2022-11-11-rstatix-anova/index/unnamed-chunk-4-1.png)

### 正态性检验

‎可以使用以下两种方法之一检查正态性假设：‎

1.  **‎分析方差分析模型残差‎**‎。以检查所有组的正态性。此方法更容易，并且当您有许多组或每个组的数据点很少时，它非常方便。‎

2.  **‎分别检查每个组的正态性‎**‎。当只有几个组并且每个组有许多数据点时，可以使用此方法。‎

#### 模型残差的正态性检验

首先构建残差的线性模型，以QQ图的形式展示结果，见Figure \@ref(fig:model)所示。


```r
# 构建线性模型
model <- lm(len ~ dose, data = df)
# 构建残差的QQ图
ggqqplot(residuals(model))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-11-11-rstatix-anova/index/model-1.png" alt="模型残差的QQ图"  />
<p class="caption">模型残差的QQ图</p>
</div>

计算Shapiro-Wilk正态性检验

    shapiro_test(residuals(model))


|variable         | statistic| p.value|
|:----------------|---------:|-------:|
|residuals(model) |    0.9673|  0.1076|

> 在QQ图中，由于所有点都大致沿着参考线落下，因此我们可以假设呈正态分布。这一结论得到了Shapiro Wilk检验的支持。p值不显著（p=0.0798），因此我们可以假设为正态。

------------------------------------------------------------------------

#### 各组的正态性检验

计算各组水平的Shapiro-Wilk检验。如果数据为正态分布，则p值应大于0.05。

    df %>%
      group_by(dose) %>% 
      shapiro_test(len)


|dose |variable | statistic|      p|
|:----|:--------|---------:|------:|
|0.5  |len      |    0.9406| 0.2466|
|1    |len      |    0.9313| 0.1639|
|2    |len      |    0.9778| 0.9019|

> 根据Shapiro-Wilk的正态性检验，各组均为正态分布（p\>0.05）。

请注意，如果样本数量大于 50，则首选正态QQ 图，因为在较大的样本量下，Shapiro-Wilk检验变得非常敏感，即使与正态性有轻微的偏差。

QQ图绘制了给定数据与正态分布之间的相关性。为每组数据绘制QQ图,见Figure \@ref(fig:qq)所示。


```r
ggqqplot(df, "len", facet.by = "dose")
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-11-11-rstatix-anova/index/qq-1.png" alt="各组数据分布的QQ图"  />
<p class="caption">各组数据分布的QQ图</p>
</div>

> 所有点都大致沿着参考线落下。所以我们可以假设数据是正态分布的。
>
> 如果对数据的正态性有疑问，可以使用*Kruskal-Wallis*检验，这是单因素ANOVA检验的非参数替代方法。

### 方差齐性检验

1.  *残差结合拟合图‎*‎可用于检查方差的同质性。‎

    
    ```r
    plot(model, 1)
    ```
    
    ![plot of chunk unnamed-chunk-7](/figures/course/2022-11-11-rstatix-anova/index/unnamed-chunk-7-1.png)

    > ‎在上图中，残差和拟合值（每个组的均值）之间没有明显的关系，结果很好。因此，我们可以假设方差的同质性。‎

2.  ‎也可以使用 ‎*‎Levene 检验‎*‎来检查‎*‎方差的同质性‎*‎：‎

<!-- -->

    df %>% levene_test(len ~ dose)


| df1| df2| statistic|      p|
|---:|---:|---------:|------:|
|   2|  57|    0.6457| 0.5281|

这时，我们要注意，一定要**将dose定义为因子**，否则会因为结果是数字而报错。

> ‎上面的结果中，我们可以看到 p 值\> 0.05，这意味组间方差之间没有显著差异。因此，我们可以假设不同组中方差具有齐性或同质性。‎
>
> 在不满足方差齐性假设的情况下，可以使用函数`Welch_ANOVA_test()`函数计算Welch单因素方差分析测试。该测试不需要假设方差相等。

## 计算检验


```r
res.aov <- df %>%
  anova_test(len ~ dose)
```

    res.aov


|Effect | DFn| DFd|     F|  p|p<.05 |   ges|
|:------|---:|---:|-----:|--:|:-----|-----:|
|dose   |   2|  57| 67.42|  0|*     | 0.703|

在上表中，该列对应于广义eta平方（效应大小）。它测量结果变量（这里是植物）中的变异性比例，可以用预测因子（这里是治疗）来解释。效应大小为0.703（26%）意味着70.3%的变化可归因于剂量条件

> 从上述ANOVA表中可以看出，各组之间存在显著差异（p=9.53e-16），显示为"\*"，F（2，57）=67.416，p=9.53e-16，eta2[g]=0.703。
>
> 该结果可以在可视化的时候引用上。

## 事后检验

常用单因素方差分析用于多组间的两两比较的方法是**Tukey事后检验**（而不是简单的t检验）。可用rstati的`tukey_hsd()`函数计算。

> 而对于方差不齐的情况下可以使用**Games Howell事后检验**，即rstati的`games_howell_test` `()`函数


```r
pwc <- tukey_hsd(df, len ~ dose)
```

    pwc

> 另外，还有几个同等的代码，结果一样
>
> df %\>% tukey_hsd(len \~ dose)
>
> aov(len \~ dose, data = df) %\>% tukey_hsd()


|term |group1 |group2 | null.value| estimate| conf.low| conf.high| p.adj|p.adj.signif |
|:----|:------|:------|----------:|--------:|--------:|---------:|-----:|:------------|
|dose |0.5    |1      |          0|    9.130|    5.902|    12.358|     0|****         |
|dose |0.5    |2      |          0|   15.495|   12.267|    18.723|     0|****         |
|dose |1      |2      |          0|    6.365|    3.137|     9.593|     0|****         |

## 可视化报告

我们可以将单因素方差分析的结果报告如下：

> 进行单因素方差分析，以评估3个不同剂量组的牙齿生长是否不同：0.5（n=20）、1.0（n=20）和2.0（n=20）。
>
> 数据表示为平均值+/-标准差。不同剂量组之间的牙齿生长在统计学上有显著差异，F（2，57）=67.416，p=9.53e-16，广义eta平方=0.703。
>
> Tukey事后分析显示，三组数据具有统计学意义（p=0.012）

### 带有p值的箱示图可视化结果

使用`add_xy_position()`函数自动进行两两分组比较，同时确定x和y轴位置。


```r
## 添加各组比较和坐标轴位置
pwc <- pwc %>%
  add_xy_position(x = "dose")
```


|term |group1 |group2 | null.value| estimate| conf.low| conf.high| p.adj|p.adj.signif | y.position|groups | xmin| xmax|
|:----|:------|:------|----------:|--------:|--------:|---------:|-----:|:------------|----------:|:------|----:|----:|
|dose |0.5    |1      |          0|    9.130|    5.902|    12.358|     0|****         |      35.39|0.5, 1 |    1|    2|
|dose |0.5    |2      |          0|   15.495|   12.267|    18.723|     0|****         |      37.62|0.5, 2 |    1|    3|
|dose |1      |2      |          0|    6.365|    3.137|     9.593|     0|****         |      39.85|1, 2   |    2|    3|

使用stat_pvalue_manual()函数添加两两比较的p值，使用labs()函数添加整体结果和方法


```r
## ggpubr绘图，加整体p值和两两比较p值
ggboxplot(df, x = "dose", y = "len") +
    stat_pvalue_manual(pwc,hide.ns = TRUE ) +#隐藏无意义的结果
    labs(subtitle = get_test_label(res.aov, detailed = TRUE),
        caption = get_pwc_label(pwc)
    )
```

![plot of chunk unnamed-chunk-15](/figures/course/2022-11-11-rstatix-anova/index/unnamed-chunk-15-1.png)

当然，我们也可以适当美化，比如配色，主题什么的，见Figure \@ref(fig:box1)所示。


```r
ggboxplot(df, x = "dose", y = "len",
          add = 'jitter',color = 'dose',
          palette = 'lancet', #设置lancet配色
          ggtheme = theme_bw(), # 设置背景
          legend='none' ## 去除分组标签
          ) +stat_pvalue_manual(pwc, hide.ns = TRUE) +
  labs( subtitle = get_test_label(res.aov, detailed = TRUE), caption = get_pwc_label(pwc))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-11-11-rstatix-anova/index/box1-1.png" alt="各组的两两比较"  />
<p class="caption">各组的两两比较</p>
</div>

我们也可以用柱状图来可视化，见Figure \@ref(fig:box2)所示。


```r
ggbarplot(df, x = "dose", y = "len",
          add = 'mean_sd',fill = 'dose',
          palette = 'npg', #设置lancet配色
          ggtheme = theme_pubclean(), # 设置背景
          legend='none' ## 去除分组标签
          ) +stat_pvalue_manual(pwc, hide.ns = TRUE) +
  labs( subtitle = get_test_label(res.aov, detailed = TRUE), caption = get_pwc_label(pwc))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-11-11-rstatix-anova/index/box2-1.png" alt="各组的两两比较"  />
<p class="caption">各组的两两比较</p>
</div>

## 放宽方差齐性假设

经典的单因素方差分析检验要求所有组的方差相等。在本例，Levene检验不显著，提示方差齐性假设证明是良好的。

在违反方差齐性假设的情况下，我们如何进行ANOVA检验呢？

在无法假设方差齐性的情况下（即Levene检验显著时），使用Welch单因素检验是标准单单因素方差分析的替代方法。

在这种情况下，可以使用Games-Howell事后检验**或**成对t检验（不假设方差相等）来比较所有可能的群体差异组合。


```r
# Welch One way ANOVA test
res.aov2 <- df %>% welch_anova_test(len ~ dose)
# Pairwise comparisons (Games-Howell)
pwc2 <- df %>% games_howell_test(len ~ dose)
# 可视化: box plots with p-values
pwc2 <- pwc2 %>% add_xy_position(x = "dose", step.increase = 1)

ggboxplot(df, x = "dose", y = "len",
          add = 'jitter',color = 'dose',
          palette = 'lancet', #设置lancet配色
          ggtheme = theme_bw(), # 设置背景
          legend='none' ## 去除分组标签
          )  +
  stat_pvalue_manual(pwc2, hide.ns = TRUE) +
  labs(
    subtitle = get_test_label(res.aov2, detailed = TRUE),
    caption = get_pwc_label(pwc2)
    )
```

![plot of chunk unnamed-chunk-16](/figures/course/2022-11-11-rstatix-anova/index/unnamed-chunk-16-1.png)

## 两组方法的比较

将两种方法进行比较，见Figure \@ref(fig:fig2)所示。


```r
p1<-ggboxplot(df, x = "dose", y = "len",
          add = 'jitter',color = 'dose',
          palette = 'lancet', #设置lancet配色
          ggtheme = theme_bw(), # 设置背景
          legend='none', ## 去除分组标签
          title = 'ANOVA text') +stat_pvalue_manual(pwc, hide.ns = TRUE,label = 'p.adj') +
  labs( subtitle = get_test_label(res.aov, detailed = TRUE), caption = get_pwc_label(pwc))

p2<-ggboxplot(df, x = "dose", y = "len",
          add = 'jitter',color = 'dose',
          palette = 'lancet', #设置lancet配色
          ggtheme = theme_bw(), # 设置背景
          legend='none', ## 去除分组标签
         title = 'Welch one-way test' )  +
  stat_pvalue_manual(pwc2, hide.ns = TRUE,label = 'p.adj') +
  labs(subtitle = get_test_label(res.aov2, detailed = TRUE),
    caption = get_pwc_label(pwc2)
    )
## 拼图
cowplot::plot_grid(p1,p2)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-11-11-rstatix-anova/index/fig2-1.png" alt="两种方法的比较"  />
<p class="caption">两种方法的比较</p>
</div>

当然，如果使用放宽条件的情况，其实还有一个更简单的办法，就是直接使用**ggstatsplot**这个包


```r
library(ggstatsplot)
```

```
## You can cite this package as:
##      Patil, I. (2021). Visualizations with statistical details: The 'ggstatsplot' approach.
##      Journal of Open Source Software, 6(61), 3167, doi:10.21105/joss.03167
```

```r
p3 <- ggbetweenstats(df, dose, len, p.adjust.method = "none")
p3
```

![plot of chunk unnamed-chunk-17](/figures/course/2022-11-11-rstatix-anova/index/unnamed-chunk-17-1.png)


```r
cowplot::plot_grid(p3, p2)
```

![plot of chunk unnamed-chunk-18](/figures/course/2022-11-11-rstatix-anova/index/unnamed-chunk-18-1.png)

可以看到结果是一样的.

------------------------------------------------------------------------

另外，还有双因素、三因素ANOVA也都是可以从教程中找到的。
