---
title: 我的硕士论文电子版最后一页的代码
author: 欧阳松
date: '2021-08-30'
slug: 'lastpageformymasterthesis'
categories: []
tags: []
from_Rmd: yes
---

之前用bookdown复现了我的硕士论文，其中重新进行了统计计算，地址是<https://swcyo.github.io/masterthesis>，现分享一下方法：

# 原始临床数据表

以前的论文结果是用**SPSS**计算的，很方便。但是本部分内容尝试由R语言生成，原始数据在知网和万网上都可以看到，数据见下表。

    data<-read.csv("/Users/mac/Documents/data.csv")
    data<-as.data.frame(data)
    library(dplyr)
    ## 将位置的数字1和2改为左和右
    data<-data %>%
           mutate(location=factor(location,labels = c("Left","Right")))


| age| height.cm| height.m| weight|   BMI|location | size| operating.time| bleeding.V| eat.time| draw.V| drae.time| hospital.time| satificatiion| cosmics|group |
|---:|---------:|--------:|------:|-----:|:--------|----:|--------------:|----------:|--------:|------:|---------:|-------------:|-------------:|-------:|:-----|
|  48|       172|     1.72|     71| 24.00|Left     |  2.1|             65|         15|        2|     30|         6|             7|            85|      85|LUL   |
|  70|       162|     1.62|     56| 21.34|Right    |  1.3|             50|         35|        2|     10|         5|             6|            84|      80|LUL   |
|  25|       172|     1.72|     66| 22.31|Right    |  1.0|             60|         30|        1|     17|         6|             7|            80|      76|LUL   |
|  59|       180|     1.80|     75| 23.15|Right    |  0.9|             55|          5|        2|     20|         7|             8|            85|      85|LUL   |
|  64|       158|     1.58|     60| 24.03|Right    |  0.8|             65|         35|        2|     25|         6|             7|            90|      88|LUL   |
|  52|       160|     1.60|     66| 25.78|Right    |  1.6|             75|          5|        1|     16|         6|             7|            88|      85|LUL   |
|  52|       158|     1.58|     56| 22.43|Right    |  1.0|             45|         25|        2|     23|         7|             8|            87|      85|LUL   |
|  47|       170|     1.70|     86| 29.76|Left     |  1.4|             80|         30|        2|     20|         6|             7|            84|      80|LUL   |
|  39|       162|     1.62|     69| 26.29|Right    |  1.3|             55|         25|        2|      5|         6|             7|            88|      85|LUL   |
|  43|       173|     1.73|     70| 23.39|Right    |  1.0|             45|         20|        2|     10|         4|             5|            90|      90|LUL   |
|  50|       170|     1.70|     72| 24.91|Left     |  1.2|             70|         15|        1|     15|         4|             5|            80|      80|LUL   |
|  42|       165|     1.65|     68| 24.98|Left     |  1.4|             60|         25|        2|     17|         4|             5|            85|      85|LUL   |
|  38|       163|     1.63|     65| 24.46|Right    |  1.6|             45|         30|        2|     22|         5|             6|            85|      82|LUL   |
|  48|       177|     1.77|     78| 24.90|Left     |  1.2|             50|         20|        2|     17|         6|             7|            80|      76|LUL   |
|  51|       169|     1.69|     72| 25.21|Left     |  2.1|             45|         10|        1|     20|         6|             7|            79|      76|LUL   |
|  46|       176|     1.76|     90| 29.05|Right    |  1.7|            120|         15|        2|     25|         6|             8|            90|      88|LESS  |
|  62|       180|     1.80|     68| 20.99|Right    |  1.5|            110|         20|        2|     30|         5|             7|            92|      85|LESS  |
|  28|       172|     1.72|     76| 25.69|Right    |  1.2|            100|         10|        1|     20|         5|             6|            90|      88|LESS  |
|  60|       168|     1.68|     70| 24.80|Left     |  1.3|             95|         25|        1|     15|         5|             6|            83|      80|LESS  |
|  61|       172|     1.72|     65| 21.97|Left     |  0.9|             90|         20|        1|     15|         4|             5|            91|      85|LESS  |
|  49|       155|     1.55|     58| 24.14|Right    |  1.0|             75|         25|        2|     18|         5|             6|            88|      88|LESS  |
|  49|       163|     1.63|     54| 20.32|Left     |  0.9|             80|         15|        1|     10|         6|             7|            90|      89|LESS  |
|  48|       178|     1.78|     68| 21.46|Left     |  1.3|             65|         15|        2|     15|         5|             5|            93|      90|LESS  |
|  35|       172|     1.72|     64| 21.63|Right    |  1.5|             65|         20|        1|     16|         4|             6|            82|      80|LESS  |
|  56|       162|     1.62|     65| 24.77|Left     |  1.7|             83|          5|        2|     25|         5|             6|            92|      92|LESS  |
|  59|       178|     1.78|     73| 23.04|Left     |  1.3|             60|         10|        1|     25|         5|             7|            81|      80|LESS  |
|  45|       168|     1.68|     60| 21.26|Left     |  1.5|             55|         15|        1|     30|         5|             8|            90|      89|LESS  |

------------------------------------------------------------------------

## 数据总结

使用R语言的`epiDisplay`包的`summ(data)`函数可以很快的显示均数，标准差，最大值和最小值，注意，这里是所有的患者，没有算上分组，还有就是location（结石部位）只有左和右，属于因子，只统计数字即可，但是这个包当成数值计算了,用自带的`summary(data)`函数可以统计因子的个数，但是又不能计算标准差，还有一个psych的`describe(data)函数`也很优秀，当然还有很多包都可以一次性统计所有结果，怎么都比SPSS要好得多。

这里吐槽一下自己的硕士论文，结果一中的研究对象里面，我把A（单孔腹腔镜组）和B（标准腹腔镜）两组的年龄、BMI和结石大小数据搞反了，不过统计结果是正确的，毕竟我是正正规规用SPSS做的结果，不是那种瞎编的数据，但还是不得不骂骂自己当时脑子混乱了，好在后面的数据都正确。

### 所有数据汇总统计：

    library(epiDisplay)
    summ(data)

使用**epiDisplay**的`summ`函数可以一步进行统计，结果见下表：


```
## Loading required package: foreign
```

```
## Loading required package: survival
```

```
## Loading required package: MASS
```

```
## 
## Attaching package: 'MASS'
```

```
## The following object is masked from 'package:dplyr':
## 
##     select
```

```
## Loading required package: nnet
```



|Var. name      |obs. |mean   |median  |s.d.   |min.   |max.   |
|:--------------|:----|:------|:-------|:------|:------|:------|
|age            |27   |49.11  |49      |10.6   |25     |70     |
|height.cm      |27   |168.7  |170     |7.16   |155    |180    |
|height.m       |27   |1.69   |1.7     |0.07   |1.55   |1.8    |
|weight         |27   |68.19  |68      |8.45   |54     |90     |
|BMI            |27   |23.93  |24.03   |2.29   |20.32  |29.76  |
|location       |27   |1.519  |2       |0.509  |1      |2      |
|size           |27   |1.32   |1.3     |0.34   |0.8    |2.1    |
|operating.time |27   |69     |65      |20.37  |45     |120    |
|bleeding.V     |27   |19.26  |20      |8.74   |5      |35     |
|eat.time       |27   |1.59   |2       |0.5    |1      |2      |
|draw.V         |27   |18.93  |18      |6.41   |5      |30     |
|drae.time      |27   |5.33   |5       |0.88   |4      |7      |
|hospital.time  |27   |6.52   |7       |0.98   |5      |8      |
|satificatiion  |27   |86.37  |87      |4.25   |79     |93     |
|cosmics        |27   |84.15  |85      |4.6    |76     |92     |
|group          |     |       |        |       |       |       |

### 分组数据统计：

但是我们其实需要的是各组的汇总数据，因此需要把两组的数据提出来汇总，我们可以使用`subset`函数提出分组的数据，然后分别统计，这里附录一下代码吧。。。

### 标准后腹腔镜组的数据统计汇总：

    lul<-subset(data,group=="LUL") # 提取标准腹腔镜组数据
    summ(lul)


|Var. name      |obs. |mean   |median  |s.d.   |min.   |max.   |
|:--------------|:----|:------|:-------|:------|:------|:------|
|age            |15   |48.53  |48      |10.93  |25     |70     |
|height.cm      |15   |167.4  |169     |6.84   |158    |180    |
|height.m       |15   |1.67   |1.69    |0.07   |1.58   |1.8    |
|weight         |15   |68.67  |69      |7.93   |56     |86     |
|BMI            |15   |24.46  |24.46   |2      |21.34  |29.76  |
|location       |15   |1.6    |2       |0.507  |1      |2      |
|size           |15   |1.33   |1.3     |0.39   |0.8    |2.1    |
|operating.time |15   |57.67  |55      |11.47  |45     |80     |
|bleeding.V     |15   |21.67  |25      |9.94   |5      |35     |
|eat.time       |15   |1.73   |2       |0.46   |1      |2      |
|draw.V         |15   |17.8   |17      |6.32   |5      |30     |
|drae.time      |15   |5.6    |6       |0.99   |4      |7      |
|hospital.time  |15   |6.6    |7       |0.99   |5      |8      |
|satificatiion  |15   |84.67  |85      |3.62   |79     |90     |
|cosmics        |15   |82.53  |85      |4.39   |76     |90     |
|group          |     |       |        |       |       |       |

### 单孔腹后腔镜组的数据统计汇总：

    less<-subset(data,group=="LESS")# 提取单孔腹腔镜组数据
    summ(less)


|Var. name      |obs. |mean   |median  |s.d.   |min.   |max.   |
|:--------------|:----|:------|:-------|:------|:------|:------|
|age            |12   |49.83  |49      |10.61  |28     |62     |
|height.cm      |12   |170.33 |172     |7.51   |155    |180    |
|height.m       |12   |1.7    |1.72    |0.08   |1.55   |1.8    |
|weight         |12   |67.58  |66.5    |9.39   |54     |90     |
|BMI            |12   |23.26  |22.51   |2.53   |20.32  |29.05  |
|location       |12   |1.417  |1       |0.515  |1      |2      |
|size           |12   |1.32   |1.3     |0.28   |0.9    |1.7    |
|operating.time |12   |83.17  |81.5    |20.48  |55     |120    |
|bleeding.V     |12   |16.25  |15      |6.08   |5      |25     |
|eat.time       |12   |1.42   |1       |0.51   |1      |2      |
|draw.V         |12   |20.33  |19      |6.53   |10     |30     |
|drae.time      |12   |5      |5       |0.6    |4      |6      |
|hospital.time  |12   |6.42   |6       |1      |5      |8      |
|satificatiion  |12   |88.5   |90      |4.15   |81     |93     |
|cosmics        |12   |86.17  |88      |4.17   |80     |92     |
|group          |     |       |        |       |       |       |

### 一步法分组汇总：

但是这样需要多建两个表，还是不方便，我们可以用**psych**包，可以使用`describeBy`函数，直接就统计好了各组的统计结果，结果很丰富，而且因子还用\*标记出来了，而且统计量更多，代码如下：

    library(psych)
    describeBy(data,data$group)


```
## 
##  Descriptive statistics by group 
## group: LESS
##                vars  n   mean    sd median trimmed   mad    min    max range
## age               1 12  49.83 10.61  49.00   50.80 12.60  28.00  62.00 34.00
## height.cm         2 12 170.33  7.51 172.00  170.90  7.41 155.00 180.00 25.00
## height.m          3 12   1.70  0.08   1.72    1.71  0.07   1.55   1.80  0.25
## weight            4 12  67.58  9.39  66.50   66.70  7.41  54.00  90.00 36.00
## BMI               5 12  23.26  2.53  22.51   22.98  2.34  20.32  29.05  8.73
## location*         6 12   1.42  0.51   1.00    1.40  0.00   1.00   2.00  1.00
## size              7 12   1.32  0.28   1.30    1.32  0.30   0.90   1.70  0.80
## operating.time    8 12  83.17 20.48  81.50   82.30 24.46  55.00 120.00 65.00
## bleeding.V        9 12  16.25  6.08  15.00   16.50  7.41   5.00  25.00 20.00
## eat.time         10 12   1.42  0.51   1.00    1.40  0.00   1.00   2.00  1.00
## draw.V           11 12  20.33  6.53  19.00   20.40  7.41  10.00  30.00 20.00
## drae.time        12 12   5.00  0.60   5.00    5.00  0.00   4.00   6.00  2.00
## hospital.time    13 12   6.42  1.00   6.00    6.40  1.48   5.00   8.00  3.00
## satificatiion    14 12  88.50  4.15  90.00   88.80  2.97  81.00  93.00 12.00
## cosmics          15 12  86.17  4.17  88.00   86.20  3.71  80.00  92.00 12.00
## group*           16 12   1.00  0.00   1.00    1.00  0.00   1.00   1.00  0.00
##                 skew kurtosis   se
## age            -0.60    -0.81 3.06
## height.cm      -0.51    -0.91 2.17
## height.m       -0.51    -0.91 0.02
## weight          0.82     0.27 2.71
## BMI             0.82    -0.33 0.73
## location*       0.30    -2.06 0.15
## size           -0.18    -1.39 0.08
## operating.time  0.28    -1.29 5.91
## bleeding.V     -0.16    -1.08 1.75
## eat.time        0.30    -2.06 0.15
## draw.V          0.14    -1.48 1.88
## drae.time       0.00    -0.48 0.17
## hospital.time   0.21    -1.21 0.29
## satificatiion  -0.77    -1.13 1.20
## cosmics        -0.44    -1.39 1.20
## group*           NaN      NaN 0.00
## ------------------------------------------------------------ 
## group: LUL
##                vars  n   mean    sd median trimmed   mad    min    max range
## age               1 15  48.53 10.93  48.00   48.69  7.41  25.00  70.00 45.00
## height.cm         2 15 167.40  6.84 169.00  167.15  8.90 158.00 180.00 22.00
## height.m          3 15   1.67  0.07   1.69    1.67  0.09   1.58   1.80  0.22
## weight            4 15  68.67  7.93  69.00   68.31  4.45  56.00  86.00 30.00
## BMI               5 15  24.46  2.00  24.46   24.30  1.60  21.34  29.76  8.42
## location*         6 15   1.60  0.51   2.00    1.62  0.00   1.00   2.00  1.00
## size              7 15   1.33  0.39   1.30    1.31  0.44   0.80   2.10  1.30
## operating.time    8 15  57.67 11.47  55.00   56.92 14.83  45.00  80.00 35.00
## bleeding.V        9 15  21.67  9.94  25.00   21.92  7.41   5.00  35.00 30.00
## eat.time         10 15   1.73  0.46   2.00    1.77  0.00   1.00   2.00  1.00
## draw.V           11 15  17.80  6.32  17.00   17.85  4.45   5.00  30.00 25.00
## drae.time        12 15   5.60  0.99   6.00    5.62  0.00   4.00   7.00  3.00
## hospital.time    13 15   6.60  0.99   7.00    6.62  0.00   5.00   8.00  3.00
## satificatiion    14 15  84.67  3.62  85.00   84.69  4.45  79.00  90.00 11.00
## cosmics          15 15  82.53  4.39  85.00   82.46  4.45  76.00  90.00 14.00
## group*           16 15   2.00  0.00   2.00    2.00  0.00   2.00   2.00  0.00
##                 skew kurtosis   se
## age            -0.03    -0.15 2.82
## height.cm       0.17    -1.29 1.77
## height.m        0.17    -1.29 0.02
## weight          0.21    -0.36 2.05
## BMI             0.87     0.89 0.52
## location*      -0.37    -1.98 0.13
## size            0.69    -0.58 0.10
## operating.time  0.44    -1.16 2.96
## bleeding.V     -0.33    -1.26 2.57
## eat.time       -0.95    -1.16 0.12
## draw.V         -0.17    -0.47 1.63
## drae.time      -0.47    -1.04 0.25
## hospital.time  -0.47    -1.04 0.25
## satificatiion  -0.14    -1.31 0.93
## cosmics        -0.17    -1.24 1.13
## group*           NaN      NaN 0.00
```

------------------------------------------------------------------------

## Fisher精确概率检验

由于本文存在两个计数资料，即结石部位和性别，但是样本量又少，所以直接用Fisher精确概率检验，当然R语言里面包也很多。

首先，需要把数据提出来，一个table函数可以直接计算好。


```r
location <- table(data$location, data$group)
location  #看一下结果
```

```
##        
##         LESS LUL
##   Left     7   6
##   Right    5   9
```

```r
fisher.test(location)$p.value  # 只需要p值，如果要看所有结果，可以把$及后面去掉
```

```
## [1] 0.4495
```

一般情况下是要做**卡方检验**，首先需要计数理论频数，注意理论频数不是个数，需要统计，然后才知道选择那种统计学方法。不过`epiDisplay`包里有个很简单的`cc()`函数，也可以一步把卡方和Fisher的结果全部算好。


```r
epiDisplay::cc(cctable = location)  ##可以计算优势比OR值和两种列联表的检验
```

![plot of chunk unnamed-chunk-7](/figures/course/2021-08-30-master/index/unnamed-chunk-7-1.png)

```
## 
##        
##         LESS LUL Total
##   Left     7   6    13
##   Right    5   9    14
##   Total   12  15    27
## 
## OR =  2.1 
## 95% CI =  0.45, 9.84  
## Chi-squared = 0.9, 1 d.f., P value = 0.343
## Fisher's exact test (2-sided) P value = 0.449
```

这里把计算理论频数和各种的检验当时代码放出，自行选择运算方法


```r
# chisq.test(data)$expected #理论频数的计数；

# chisq.test(data,correct=FALSE)
# #一般的卡方检验，不用校正，注意默认是校正，所以一定要改；
# chisq.test(data,correct=TURE) #校正卡方检验，适用总数>40但理论频数<5 ；

# fisher.test(data) # Fisher精确概率检验。
```

## 两独立样本的T检验或非参数检验？

### 首先要做正态性检验

我的论文里用的是`Levene`检验，这里用`shapiro`检验。正确的统计方法应该是分组统计，我之前选择的是整体统计，其实这样也可以，但是不严谨。

最严谨的办法其实是把每一个变量按照分组分别进行正态检验，如果两组里面哪怕有一组正态分布，一组不正态分布，也是不正态分布，要选择非参数检验。

下面的函数是分别统计，可以用`tapply()`，也可以换成`by()`


```r
with(data, tapply(data$age, group, shapiro.test))
```

```
## $LESS
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.91, p-value = 0.2
## 
## 
## $LUL
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.97, p-value = 0.9
```

```r
with(data, tapply(data$BMI, group, shapiro.test))
```

```
## $LESS
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.9, p-value = 0.2
## 
## 
## $LUL
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.93, p-value = 0.2
```

```r
with(data, tapply(data$size, group, shapiro.test))
```

```
## $LESS
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.92, p-value = 0.3
## 
## 
## $LUL
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.91, p-value = 0.1
```

```r
with(data, tapply(data$operating.time, group, shapiro.test))
```

```
## $LESS
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.96, p-value = 0.8
## 
## 
## $LUL
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.91, p-value = 0.2
```

```r
with(data, tapply(data$bleeding.V, group, shapiro.test))
```

```
## $LESS
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.94, p-value = 0.5
## 
## 
## $LUL
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.93, p-value = 0.3
```

```r
with(data, tapply(data$eat.time, group, shapiro.test))
```

```
## $LESS
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.64, p-value = 2e-04
## 
## 
## $LUL
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.56, p-value = 1e-05
```

```r
with(data, tapply(data$draw.V, group, shapiro.test))
```

```
## $LESS
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.92, p-value = 0.3
## 
## 
## $LUL
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.97, p-value = 0.9
```

```r
with(data, tapply(data$drae.time, group, shapiro.test))
```

```
## $LESS
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.77, p-value = 0.005
## 
## 
## $LUL
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.83, p-value = 0.009
```

```r
with(data, tapply(data$hospital.time, group, shapiro.test))
```

```
## $LESS
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.9, p-value = 0.1
## 
## 
## $LUL
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.83, p-value = 0.009
```

```r
with(data, tapply(data$satificatiion, group, shapiro.test))
```

```
## $LESS
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.82, p-value = 0.02
## 
## 
## $LUL
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.92, p-value = 0.2
```

```r
with(data, tapply(data$cosmics, group, shapiro.test))
```

```
## $LESS
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.87, p-value = 0.07
## 
## 
## $LUL
## 
## 	Shapiro-Wilk normality test
## 
## data:  X[[i]]
## W = 0.9, p-value = 0.1
```

之后就是痛苦的手动观察了，我之前用SPSS是当作整体去观察的，不是很严谨，分组统计很严谨，但是也很费时费神，网上现在也有好的在线工具，比如仙桃学术，但是如果全部录入数据的话，统计虽然也是分组做正态性检验，但是最后却选择了整体的结果，也就是统一用一个方法做显著性统计，这样其实也不是很严谨，这种办法确实简化了流程，但是结果欠佳。

关于统计学方法，我懂的也不是很深刻，至于到底选择哪种办法，也只能算见仁见智了。

这里我们看到**年龄、BMI、结石大小、手术时间、出血量、引流量、美容评分**的p值，两组都大于0.05，符合正态分布，备选t检验。

而**肠道恢复时间、拔管时间、住院时间**（LUL组不正态分布）、**满意度（**LELL组不正态）的p值不完全大于0.05，因此要选择非参数检验（我们选择**Wilcoxon检验**）。

### 然后要做方差齐性检验

挑选需要的变量做方差齐性检验，如果P\>0.05就用t检验，如果P\>0.05就用校正的t检验

可以看到**手术时间**不符合方差齐性检验，要选择校正t检验，

而**年龄、BMI、结石大小、手术时间、出血量、引流量、美容评**直接用t检验。


```r
var.test(age ~ group, data = data)$p.value
```

```
## [1] 0.9353
```

```r
var.test(BMI ~ group, data = data)$p.value
```

```
## [1] 0.4075
```

```r
var.test(size ~ group, data = data)$p.value
```

```
## [1] 0.2568
```

```r
var.test(operating.time ~ group, data = data)$p.value
```

```
## [1] 0.04462
```

```r
var.test(bleeding.V ~ group, data = data)$p.value
```

```
## [1] 0.1078
```

```r
var.test(draw.V ~ group, data = data)$p.value
```

```
## [1] 0.8913
```

```r
var.test(cosmics ~ group, data = data)$p.value
```

```
## [1] 0.8812
```

```r
## 非正态分布，其实可以不用做方差齐性检验了，但是可以顺手做一下
var.test(eat.time ~ group, data = data)$p.value
```

```
## [1] 0.6676
```

```r
var.test(drae.time ~ group, data = data)$p.value
```

```
## [1] 0.1083
```

```r
var.test(hospital.time ~ group, data = data)$p.value
```

```
## [1] 0.9523
```

```r
var.test(satificatiion ~ group, data = data)$p.value
```

```
## [1] 0.6227
```

### 显著性统计检验

默认的其实是双尾检验，其实如果我们要想知道LESS是否比LUL好的时候，应该是要用单尾检验的，这里分为两种情况

1.  基本参数的比较用**双尾检验**，比如年龄、BMI和结石大小（这些都是术前的资料，不影响结果）

那么统计方法就是，记住默认的是不具有齐性，很奇怪的设定


```r
t.test(age ~ group, data = data, var.equal = TRUE, alternative = "two.sided")$p.value
```

```
## [1] 0.7583
```

```r
t.test(BMI ~ group, data = data, var.equal = TRUE, alternative = "two.sided")$p.value
```

```
## [1] 0.1798
```

```r
t.test(size ~ group, data = data, var.equal = TRUE, alternative = "two.sided")$p.value
```

```
## [1] 0.9414
```

结果与我论文的一模一样，可重复。。。

2.  单尾事件，其实如果不知道什么结果的话，直接用双尾没有问题，但是如果你想知道LESS是不是比LUL好，就要用单尾假设，理论上先用双尾检验，然后再次用单尾检验一次。

    这里非参数检验会提示注意消息，不用管


```r
## 方差不齐的要校正，也就是要var.equal=F，默认的就是不齐
t.test(operating.time ~ group, data = data, var.equal = FALSE, alternative = "two.sided")$p.value
```

```
## [1] 0.001341
```

```r
## 方差齐的不用校正，也就是var.equal=T，这个要手动设定
t.test(bleeding.V ~ group, data = data, var.equal = TRUE, alternative = "two.sided")$p.value
```

```
## [1] 0.1108
```

```r
t.test(draw.V ~ group, data = data, var.equal = TRUE, alternative = "two.sided")$p.value
```

```
## [1] 0.3173
```

```r
t.test(cosmics ~ group, data = data, var.equal = TRUE, alternative = "two.sided")$p.value
```

```
## [1] 0.03858
```

```r
## 非参数检验
wilcox.test(eat.time ~ group, data = data, var.equal = TRUE, alternative = "two.sided")$p.value
```

```
## Warning in wilcox.test.default(x = DATA[[1L]], y = DATA[[2L]], ...): cannot
## compute exact p-value with ties
```

```
## [1] 0.1087
```

```r
wilcox.test(drae.time ~ group, data = data, var.equal = TRUE, alternative = "two.sided")$p.value
```

```
## Warning in wilcox.test.default(x = DATA[[1L]], y = DATA[[2L]], ...): cannot
## compute exact p-value with ties
```

```
## [1] 0.05945
```

```r
wilcox.test(hospital.time ~ group, data = data, var.equal = TRUE, alternative = "two.sided")$p.value
```

```
## Warning in wilcox.test.default(x = DATA[[1L]], y = DATA[[2L]], ...): cannot
## compute exact p-value with ties
```

```
## [1] 0.5556
```

```r
wilcox.test(satificatiion ~ group, data = data, var.equal = TRUE, alternative = "two.sided")$p.value
```

```
## Warning in wilcox.test.default(x = DATA[[1L]], y = DATA[[2L]], ...): cannot
## compute exact p-value with ties
```

```
## [1] 0.01486
```

------------------------------------------------------------------------

这里的结果计算出来以后，T检验或近似T检验，与我的论文结果数字一致，而非参数检验有细微差异，但是结果趋势也是一模一样。。。

然后是单尾事件，可以自定义好还是坏，函数是greater和less，自己可以尝试一下，我就不演示了


```r
# t.test(operating.time ~ group,data = data,var.equal = FALSE,alternative =
# 'greater')$p.value 方差齐的不用校正，也就是var.equal=T，这个要手动设定
# t.test(bleeding.V ~ group,data = data,var.equal = TRUE,alternative =
# 'less')$p.value t.test(draw.V ~ group,data = data,var.equal =
# TRUE,alternative = 'less')$p.value t.test(cosmics ~ group,data =
# data,var.equal = TRUE,alternative = 'greater')$p.value 非参数检验
# wilcox.test(eat.time ~ group,data = data,var.equal = TRUE,alternative =
# 'less')$p.value wilcox.test(drae.time ~ group,data = data,var.equal =
# TRUE,alternative = 'less')$p.value wilcox.test(hospital.time ~ group,data =
# data,var.equal = TRUE,alternative = 'less')$p.value wilcox.test(satificatiion
# ~ group,data = data,var.equal = TRUE,alternative = 'greater')$p.value
```

## 手术曲线

我的论文里有个SPSS做的手术曲线，这里一步作图


```r
data$seq<-c(1:15,1:12) # 定义手术顺序
data$seq=factor(data$seq) #转换为因子，不然显示的是数字
suppressMessages(library(ggpubr))
ggline(data,"seq","operating.time",
               facet.by = "group",scale='free',ncol=1,
               xlab = "Operation sequence",
               ylab="Operation time (min)",
               ggtheme = theme_bw(base_size = 12)) + #主题和字号
              ylim(0,120) # 设置Y轴范围，不设置的话会从最小值开始
```

![plot of chunk unnamed-chunk-14](/figures/course/2021-08-30-master/index/unnamed-chunk-14-1.png)
