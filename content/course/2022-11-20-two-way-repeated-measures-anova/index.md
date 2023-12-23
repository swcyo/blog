---
title: 双组折线图的统计学绘制（两因素重复测量方差分析）
author: 欧阳松
date: '2022-11-20'
slug: Two-way-repeated-measures-ANOVA
categories:
  - 双因素重复测量方差分析
  - 单因素方差分析
tags:
  - 双因素重复测量
  - 多因素方差分析
from_Rmd: yes
---

我之前写了一个[R语言处理CCK8或MTT数据，一步绘制生长曲线](/course/2021-08-14-r-cck8-mtt/r-cck8/)的教程，现在看来还是粗躁的很，虽然也可视化了结果，但是**统计学方法估计是不科学的**，最近看了[Repeated Measures ANOVA in R: The Ultimate Guide](https://www.datanovia.com/en/lessons/repeated-measures-anova-in-r/#two-way-repeated-measures-anova)这个教程，加上仙桃学术的[示例数据](https://bioinfomatics.xiantao.love/biotools/data/demo/free/linePlot/%E6%8A%98%E7%BA%BF%E5%9B%BE.xlsx)和[脚本代码](https://bioinfomatics.xiantao.love/biotools/code/open/lineplot.R)，对两组的折线图的分析又了更新的理解，并且写了一篇简书教程[R进行两因素重复测量方差分析并可视化（双组折线图）](https://www.jianshu.com/p/9eae52c4a090?v=1668870325891)。也就是说这是一种**两因素重复测量方差分析**（**Two-way repeated measures ANOVA**）

还是用之间的数据运行一下，原始数据如下：

| Time | NC          | NC          | NC          | OE          | OE          | OE          |
|:----------|:----------|:----------|:----------|:----------|:----------|:----------|
| day1 | 0.549070969 | 0.549570976 | 0.547750963 | 0.543670962 | 0.536370963 | 0.545970956 |
| day2 | 0.675742972 | 0.696628983 | 0.690574949 | 0.637390961 | 0.630422963 | 0.653620952 |
| day3 | 0.894142977 | 0.884125994 | 0.882670941 | 0.82057096  | 0.803780962 | 0.819401947 |
| day4 | 1.179670983 | 1.18457301  | 1.178542929 | 1.074040958 | 1.067993961 | 1.052209937 |
| day5 | 1.505662991 | 1.507610027 | 1.500910915 | 1.387150955 | 1.32420196  | 1.389946927 |

**但是**，这个表格数据形式不适合今天的教程，我们把数据转置一下，见下表所示，数据不多的话可以使用excel或者wps完成，然后保存为csv格式，比如我们放在桌面上，定义为`data.csv`。

```         
data<-read.csv("~data.csv")
```


Table: 需要的数据格式

|group |   day1|   day2|   day3|  day4|  day5|
|:-----|------:|------:|------:|-----:|-----:|
|NC    | 0.5491| 0.6757| 0.8941| 1.180| 1.506|
|NC    | 0.5496| 0.6966| 0.8841| 1.185| 1.508|
|NC    | 0.5478| 0.6906| 0.8827| 1.179| 1.501|
|OE    | 0.5437| 0.6374| 0.8206| 1.074| 1.387|
|OE    | 0.5364| 0.6304| 0.8038| 1.068| 1.324|
|OE    | 0.5460| 0.6536| 0.8194| 1.052| 1.390|

## 加载需要的包并导入数据


```r
library(reshape2)  # 转换数据
library(car)  # 方差齐性检验
library(rstatix)  # 整体统计
library(ggpubr)  # 画图
```

## 新增一列id，id即为数字，用于后续分析，必不可少


```r
data$id <- 1:nrow(data)
```


Table: 新增1列id

|group |   day1|   day2|   day3|  day4|  day5| id|
|:-----|------:|------:|------:|-----:|-----:|--:|
|NC    | 0.5491| 0.6757| 0.8941| 1.180| 1.506|  1|
|NC    | 0.5496| 0.6966| 0.8841| 1.185| 1.508|  2|
|NC    | 0.5478| 0.6906| 0.8827| 1.179| 1.501|  3|
|OE    | 0.5437| 0.6374| 0.8206| 1.074| 1.387|  4|
|OE    | 0.5364| 0.6304| 0.8038| 1.068| 1.324|  5|
|OE    | 0.5460| 0.6536| 0.8194| 1.052| 1.390|  6|

## 将短数据转换为长数据


```r
data2 <- gather(data, key = "time", value = "value", -group, -id)  #数据转换，保留group和id组
data2 <- data2 %>%
  convert_as_factor(group, id, time)  # 转换为因子
```


Table: 短数据转换为长数据

|group |id |time |  value|
|:-----|:--|:----|------:|
|NC    |1  |day1 | 0.5491|
|NC    |2  |day1 | 0.5496|
|NC    |3  |day1 | 0.5478|
|OE    |4  |day1 | 0.5437|
|OE    |5  |day1 | 0.5364|
|OE    |6  |day1 | 0.5460|
|NC    |1  |day2 | 0.6757|
|NC    |2  |day2 | 0.6966|
|NC    |3  |day2 | 0.6906|
|OE    |4  |day2 | 0.6374|
|OE    |5  |day2 | 0.6304|
|OE    |6  |day2 | 0.6536|
|NC    |1  |day3 | 0.8941|
|NC    |2  |day3 | 0.8841|
|NC    |3  |day3 | 0.8827|
|OE    |4  |day3 | 0.8206|
|OE    |5  |day3 | 0.8038|
|OE    |6  |day3 | 0.8194|
|NC    |1  |day4 | 1.1797|
|NC    |2  |day4 | 1.1846|
|NC    |3  |day4 | 1.1785|
|OE    |4  |day4 | 1.0740|
|OE    |5  |day4 | 1.0680|
|OE    |6  |day4 | 1.0522|
|NC    |1  |day5 | 1.5057|
|NC    |2  |day5 | 1.5076|
|NC    |3  |day5 | 1.5009|
|OE    |4  |day5 | 1.3872|
|OE    |5  |day5 | 1.3242|
|OE    |6  |day5 | 1.3899|

## 对各组进行统计学描述

使用**rstatix**包可以很快速的统计各组在不同时间上的值，包括了均数、标准差、标准误、最大值、最小值等等结果


```r
data3 <- data2 %>%
  group_by(group, time) %>%
  get_summary_stats(value)
```


Table: 各组统计学描述

|group |time |variable |  n|   min|   max| median|    q1|    q3|   iqr|   mad|  mean|    sd|    se|    ci|
|:-----|:----|:--------|--:|-----:|-----:|------:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|NC    |day1 |value    |  3| 0.548| 0.550|  0.549| 0.548| 0.549| 0.001| 0.001| 0.549| 0.001| 0.001| 0.002|
|NC    |day2 |value    |  3| 0.676| 0.697|  0.691| 0.683| 0.694| 0.010| 0.009| 0.688| 0.011| 0.006| 0.027|
|NC    |day3 |value    |  3| 0.883| 0.894|  0.884| 0.883| 0.889| 0.006| 0.002| 0.887| 0.006| 0.004| 0.016|
|NC    |day4 |value    |  3| 1.179| 1.185|  1.180| 1.179| 1.182| 0.003| 0.002| 1.181| 0.003| 0.002| 0.008|
|NC    |day5 |value    |  3| 1.501| 1.508|  1.506| 1.503| 1.507| 0.003| 0.003| 1.505| 0.003| 0.002| 0.009|
|OE    |day1 |value    |  3| 0.536| 0.546|  0.544| 0.540| 0.545| 0.005| 0.003| 0.542| 0.005| 0.003| 0.012|
|OE    |day2 |value    |  3| 0.630| 0.654|  0.637| 0.634| 0.646| 0.012| 0.010| 0.640| 0.012| 0.007| 0.030|
|OE    |day3 |value    |  3| 0.804| 0.821|  0.819| 0.812| 0.820| 0.008| 0.002| 0.815| 0.009| 0.005| 0.023|
|OE    |day4 |value    |  3| 1.052| 1.074|  1.068| 1.060| 1.071| 0.011| 0.009| 1.065| 0.011| 0.007| 0.028|
|OE    |day5 |value    |  3| 1.324| 1.390|  1.387| 1.356| 1.389| 0.033| 0.004| 1.367| 0.037| 0.021| 0.092|

## 异常值分析

我们可以使用`identify_outliers()`函数检测一下有无异常值。结果发现没有异常值。


```r
data2 %>%
  group_by(group, time) %>%
  identify_outliers(value)
```

```
## [1] group      time       id         value      is.outlier is.extreme
## <0 rows> (or 0-length row.names)
```

## 批量运行正态性检验（Shapiro-Wilk normality test）

可以简单设置一个函数对各组进行正态性检验，我们要保证每一个数据都是正态性分布才能进行ANOVA，否则就是非参数检验分析。

可以看到


```r
for (i in unique(data[, 1])) {
  data1 <- data[data[, 1] == i, ]
  print(lapply(data1[, -1], function(x) shapiro.test(x)))
}
```

```
## $day1
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 0.94, p-value = 0.5
## 
## 
## $day2
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 0.94, p-value = 0.5
## 
## 
## $day3
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 0.84, p-value = 0.2
## 
## 
## $day4
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 0.88, p-value = 0.3
## 
## 
## $day5
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 0.94, p-value = 0.5
## 
## 
## $id
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 1, p-value = 1
## 
## 
## $day1
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 0.92, p-value = 0.4
## 
## 
## $day2
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 0.95, p-value = 0.6
## 
## 
## $day3
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 0.8, p-value = 0.1
## 
## 
## $day4
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 0.94, p-value = 0.5
## 
## 
## $day5
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 0.78, p-value = 0.07
## 
## 
## $id
## 
## 	Shapiro-Wilk normality test
## 
## data:  x
## W = 1, p-value = 1
```

也可以使用下面的函数进行正态性检验。


```r
data2 %>%
  group_by(group, time) %>%
  shapiro_test(value)
```

```
## # A tibble: 10 × 5
##    group time  variable statistic      p
##    <fct> <fct> <chr>        <dbl>  <dbl>
##  1 NC    day1  value        0.937 0.514 
##  2 NC    day2  value        0.944 0.545 
##  3 NC    day3  value        0.843 0.223 
##  4 NC    day4  value        0.885 0.338 
##  5 NC    day5  value        0.945 0.547 
##  6 OE    day1  value        0.917 0.442 
##  7 OE    day2  value        0.950 0.567 
##  8 OE    day3  value        0.802 0.119 
##  9 OE    day4  value        0.938 0.519 
## 10 OE    day5  value        0.782 0.0718
```


### QQ图目测一下正态性分析


```r
ggqqplot(data2, "value", ggtheme = theme_bw()) + facet_grid(time ~ group, labeller = "label_both")
```

![plot of chunk unnamed-chunk-12](/figures/course/2022-11-20-two-way-repeated-measures-anova/index/unnamed-chunk-12-1.png)

### 批量运行方差齐性检验

方差齐性检验(Levene's test)显示，组内的观测变量的方差相等(P \> 0.05)


```r
for (i in unique(data2$time)) {
  data1 <- data2[data2$time == i, ]
  print(leveneTest(value ~ group, data = data1))
}
```

```
## Levene's Test for Homogeneity of Variance (center = median)
##       Df F value Pr(>F)
## group  1     1.4    0.3
##        4               
## Levene's Test for Homogeneity of Variance (center = median)
##       Df F value Pr(>F)
## group  1    0.01   0.91
##        4               
## Levene's Test for Homogeneity of Variance (center = median)
##       Df F value Pr(>F)
## group  1    0.09   0.78
##        4               
## Levene's Test for Homogeneity of Variance (center = median)
##       Df F value Pr(>F)
## group  1    1.19   0.34
##        4               
## Levene's Test for Homogeneity of Variance (center = median)
##       Df F value Pr(>F)
## group  1    0.91   0.39
##        4
```

| group | df1 | df2 | F     | Pr(\>F) |
|-------|-----|-----|-------|---------|
| day1  | 1   | 4   | 1.404 | 0.302   |
| day2  | 1   | 4   | 0.015 | 0.910   |
| day3  | 1   | 4   | 0.090 | 0.779   |
| day4  | 1   | 4   | 1.189 | 0.337   |
| day5  | 1   | 4   | 0.915 | 0.393   |

## 统计学分析

### 方差分析


```r
res.aov <- anova_test(data = data2, dv = value, wid = id, within = time, between = group)

res.aov
```

```
## ANOVA Table (type II tests)
## 
## $ANOVA
##       Effect DFn DFd       F        p p<.05   ges
## 1      group   1   4  126.75 3.55e-04     * 0.918
## 2       time   4  16 4942.90 1.64e-24     * 0.999
## 3 group:time   4  16   26.36 7.22e-07     * 0.810
## 
## $`Mauchly's Test for Sphericity`
##       Effect        W    p p<.05
## 1       time 0.000315 0.05     *
## 2 group:time 0.000315 0.05     *
## 
## $`Sphericity Corrections`
##       Effect   GGe    DF[GG]    p[GG] p[GG]<.05   HFe      DF[HF]    p[HF]
## 1       time 0.407 1.63, 6.5 5.99e-11         * 0.646 2.58, 10.33 1.99e-16
## 2 group:time 0.407 1.63, 6.5 9.55e-04         * 0.646 2.58, 10.33 5.11e-05
##   p[HF]<.05
## 1         *
## 2         *
```

### 事后检验

对于正态分布又方差齐性的数据，可以用Tukey检验进行事后检验。


```r
Pairwise <- data2 %>%
  group_by(time) %>%
  tukey_hsd(value ~ group)
```


Table: 土耳其检验

|time |term  |group1 |group2 | null.value| estimate| conf.low| conf.high|  p.adj|p.adj.signif |
|:----|:-----|:------|:------|----------:|--------:|--------:|---------:|------:|:------------|
|day1 |group |NC     |OE     |          0|  -0.0068|  -0.0150|    0.0014| 0.0823|ns           |
|day2 |group |NC     |OE     |          0|  -0.0472|  -0.0729|   -0.0215| 0.0070|**           |
|day3 |group |NC     |OE     |          0|  -0.0724|  -0.0905|   -0.0543| 0.0004|***          |
|day4 |group |NC     |OE     |          0|  -0.1162|  -0.1350|   -0.0974| 0.0001|****         |
|day5 |group |NC     |OE     |          0|  -0.1376|  -0.1975|   -0.0778| 0.0031|**           |

我们也可以试试配对t检验，可以看到结果相差不大。


```r
pwc <- data2 %>%
  group_by(time) %>%
  pairwise_t_test(value ~ group, paired = TRUE, p.adjust.method = "bonferroni")
```


Table: 配对T检验

|time |.y.   |group1 |group2 | n1| n2| statistic| df|     p| p.adj|p.adj.signif |
|:----|:-----|:------|:------|--:|--:|---------:|--:|-----:|-----:|:------------|
|day1 |value |NC     |OE     |  3|  3|     2.016|  2| 0.181| 0.181|ns           |
|day2 |value |NC     |OE     |  3|  3|     4.952|  2| 0.038| 0.038|*            |
|day3 |value |NC     |OE     |  3|  3|    14.583|  2| 0.005| 0.005|**           |
|day4 |value |NC     |OE     |  3|  3|    19.429|  2| 0.003| 0.003|**           |
|day5 |value |NC     |OE     |  3|  3|     5.986|  2| 0.027| 0.027|*            |

### 自动两两比较，添加xy轴位置

我们使用**rstatix**包的`add_xy_position()`函数可以添加两两比较列表和x轴y轴位置。


```r
Pairwise <- Pairwise %>%
  add_xy_position(x = "time")
```

## 可视化绘图

我们使用**ggpubr**包的`ggline()`函数进行折线图的可视化，使用`stat_pvalue_manual()`自动标记p值和位置


```r
ggline(data2,x='time',y='value',
color = 'group', #按组配色
add = 'mean_sd', #添加均数标准差，也可以设置均数标准误，CI等。
palette = "aaas",# aaas杂志配色
ggtheme = theme_bw(base_size = 12),
title = "CCK8 assay",xlab = "Time",ylab = "OD 450 value",legend.title="Group",legend=c(0.1,0.8)
)+stat_pvalue_manual(Pairwise, bracket.size = 0) + # 添加两两比较，隐藏无意义
    labs(subtitle = get_test_label(res.aov, detailed = TRUE), # 添加整体差异
        caption = get_pwc_label(Pairwise) # 右下角显示两两比较方法。
    )
```

![plot of chunk unnamed-chunk-20](/figures/course/2022-11-20-two-way-repeated-measures-anova/index/unnamed-chunk-20-1.png)

可以看到结果跟之前是一样的，但是细节更多.
