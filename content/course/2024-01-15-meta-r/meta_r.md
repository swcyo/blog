---
title: Meta分析方法及其在医学领域的应用
author: 欧阳松
date: '2024-01-15'
slug: meta_r
categories:
  - meta分析
  - R语言实战
  - R语言医学数据分析实战
tags:
  - meta分析
  - R语言实战
  - R语言医学数据分析实战
from_Rmd: yes
---

-   本次教程来自于《R语言医学数据分析实战-赵军编著》

## Meta分析的基本步骤

Meta分析本质上是一种观察性研究，包括提出问题、收集和分析数据、报告结果等基本研究过程，其具体步骤可概括如下：

### 提出问题，指定研究计划

### 检索相关文献

### 选择符合要求的纳入文献

### 文献的信息提取

### 纳入研究的质量评价

### 资料的统计学处理

### 结果的分析和讨论

## Meta分析的常用统计方法

纳入Meta分析的各个独立研究可能会报告多种不同的结果变量，选择哪种结果变量作为效应指标，是我们首先需要考虑的。

-   对于二分类结果变量，常用的用于合并效应量有：

> 相对危险度(Relative Ratio, RR) 优势比(Odds Ratio, OR) 危险度差(Risk Difference, RD)

-   对于连续型变量，有均值、均值差(Mean Difference, MD)、标准化均值差(Standardized Mean Difference, SMD)、相关系数等。

在合并效应量时，首先需要区分变异的来源

在合并效应量时，常用的统计方法有Mantel-Haenszel法、Peto法、Fleiss法、方差倒数法、DerSimonian-Laird法等。

-   Mantel-Haenszel法适用于二分类变量

-   Peto法使用于结局变量时优势比的资料

-   Fleiss法适用于只提供了试验组（病例组）和对照组的发病率（或暴露率）的资料

Meta分析设计的领域和应用范围极为广泛，能够使用的统计方法也很多，在R中有一系列用于Meta分析的包，其中比较流行的时候**meta**包。该包里用于Meta分析的函数都以`meta`开头，函数中的参数`sm`用于设置合并效应量的类型。常见的函数及其对应的合并效应量的类别见下表。

| **函数**    | **参数sm 的取值** | **合并效应量**                   |
|-------------|-------------------|----------------------------------|
| metabin(）  | RR（默认）        | 相对危险度                       |
|             | OR                | 优势比                           |
|             | RD                | 危险度差                         |
|             | ASD               | 反正弦差                         |
| metacont()  | MD（默认）        | 均值差                           |
|             | SMD               | 标准化均值差                     |
|             | ROM               | 均值                             |
| metacor()   | ZCOR（默认）      | Fisher-Z变换相关系数             |
|             | COR               | 相关系数                         |
| metainc(）  | IRR（默认）       | 发病率比                         |
|             | IRD               | 发病率差                         |
| metamean()  | MRAW（默认）      | 原始均值                         |
|             | MLN               | 对数均值                         |
| metaprop(） | PLOGIT（默认）    | Logit比例                        |
|             | PFT               | Freeman-Tukey 双重反正弦变换比例 |
|             | PLN               | 对数比例                         |
|             | PRAW              | 原始比例                         |
| metarate()  | IRLN（默认）      | 对数发病率                       |
|             | IR                | 发病率                           |

下面仅侧重介绍医学研究中最为常见的OR（或者RR）值的合并、RD的合并、MD的合并的Meta分析方法。

## 二分类变量资料的Meta分析

| **null**                 | **OR**             | **RR**       |
|--------------------------|--------------------|--------------|
| 无效值                   | 1                  | 1            |
| 可解释性                 | 较差               | 好           |
| 适合的研究类型           | 所有有对照的研究生 | 前瞻性研究   |
| 控制协变量时的应用性     | 好                 | 较差         |
| 一致性                   | 好                 | 好           |
| 以事件或非事件计算结果时 | 不变               | 可能差异较大 |

### OR、RR或RD的合并

下面以meta包里的数据集Fless93为例重点介绍合并OR值饿meta分析流程。该数据集时20世纪70年代到80年度完成的有关阿斯匹林预防心肌梗死后死亡的7个临床试验研究结果。加载该数据集

```
library(meta) ##版本不要太高，4.0即可
data(Fleiss93)
Fleiss93
```


```
##    study year event.e  n.e event.c  n.c
## 1  MRC-1 1974      49  615      67  624
## 2    CDP 1976      44  758      64  771
## 3  MRC-2 1979     102  832     126  850
## 4   GASP 1979      32  317      38  309
## 5  PARIS 1980      85  810      52  406
## 6   AMIS 1980     246 2267     219 2257
## 7 ISIS-2 1988    1570 8587    1720 8600
```
  
  在该数据中，变量study表示研究的标签，变量year表示研究的年份，变量event.e表示治疗组的死亡人数，变量n.e表示治疗组的总人数，变量event.c表示对照组的死亡人数，变量n.c表示对照组的总人数。
  1. 合并OR值
  函数`metabin()`用于合并OR值，代码如下：

```r
metabin(event.e, n.e, event.c, n.c, data = Fleiss93, sm = "OR")
```

```
##       OR           95%-CI %W(fixed) %W(random)
## 1 0.7197 [0.4890; 1.0593]      3.18       8.21
## 2 0.6808 [0.4574; 1.0132]      3.10       7.85
## 3 0.8029 [0.6065; 1.0629]      5.68      13.23
## 4 0.8007 [0.4863; 1.3186]      1.80       5.36
## 5 0.7981 [0.5526; 1.1529]      3.22       8.89
## 6 1.1327 [0.9347; 1.3728]     10.15      20.70
## 7 0.8950 [0.8294; 0.9657]     72.88      35.77
## 
## Number of studies combined: k=7
## 
##                          OR           95%-CI      z  p-value
## Fixed effect model   0.8969 [0.8405; 0.9570] -3.288   0.001 
## Random effects model 0.8763 [0.7743; 0.9917] -2.092   0.0365
## 
## Quantifying heterogeneity:
## tau^2 = 0.0096; H = 1.29 [1; 1.99]; I^2 = 39.7% [0%; 74.6%]
## 
## Test of heterogeneity:
##     Q d.f.  p-value
##  9.95    6   0.1269
## 
## Details on meta-analytical method:
## - Mantel-Haenszel method
## - DerSimonian-Laird estimator for tau^2
```
  
#### 绘制森林图
**meta**包的`forest()`函数可用于绘制森林图

```r
m <- metabin(event.e, n.e, event.c, n.c, data = Fleiss93, sm = "OR", studlab = paste(study,
  year))
forest(m, comb.random = F)
```

![plot of chunk unnamed-chunk-3](/figures/course/2024-01-15-meta-r/meta_r/unnamed-chunk-3-1.png)

 2. 合并RR值

```r
metabin(event.e, n.e, event.c, n.c, data = Fleiss93, sm = "RR")
```

```
##       RR           95%-CI %W(fixed) %W(random)
## 1 0.7420 [0.5223; 1.0543]      2.89       7.84
## 2 0.6993 [0.4828; 1.0129]      2.76       7.18
## 3 0.8270 [0.6487; 1.0545]      5.42      13.59
## 4 0.8209 [0.5269; 1.2789]      1.67       5.29
## 5 0.8193 [0.5927; 1.1326]      3.01       8.92
## 6 1.1183 [0.9411; 1.3289]      9.54      20.41
## 7 0.9142 [0.8596; 0.9722]     74.71      36.79
## 
## Number of studies combined: k=7
## 
##                          RR           95%-CI      z  p-value
## Fixed effect model   0.9136 [0.8657; 0.9642] -3.288   0.001 
## Random effects model 0.8929 [0.8006; 0.9959] -2.035   0.0419
## 
## Quantifying heterogeneity:
## tau^2 = 0.0074; H = 1.29 [1; 1.98]; I^2 = 39.6% [0%; 74.6%]
## 
## Test of heterogeneity:
##     Q d.f.  p-value
##  9.93    6   0.1277
## 
## Details on meta-analytical method:
## - Mantel-Haenszel method
## - DerSimonian-Laird estimator for tau^2
```
 3. 合并RD值

```r
metabin(event.e, n.e, event.c, n.c, data = Fleiss93, sm = "RD")
```

```
##        RD             95%-CI %W(fixed) %W(random)
## 1 -0.0277 [-0.0601;  0.0047]      4.45      10.84
## 2 -0.0250 [-0.0506;  0.0007]      5.49      14.76
## 3 -0.0256 [-0.0583;  0.0070]      6.03      10.70
## 4 -0.0220 [-0.0714;  0.0274]      2.25       5.58
## 5 -0.0231 [-0.0619;  0.0156]      3.88       8.29
## 6  0.0115 [-0.0062;  0.0292]     16.23      21.59
## 7 -0.0172 [-0.0289; -0.0054]     61.67      28.24
## 
## Number of studies combined: k=7
## 
##                           RD             95%-CI      z  p-value
## Fixed effect model   -0.0143 [-0.0228; -0.0058] -3.288   0.001 
## Random effects model -0.0149 [-0.0276; -0.0023] -2.315   0.0206
## 
## Quantifying heterogeneity:
## tau^2 = 0.0001; H = 1.32 [1; 2.04]; I^2 = 42.6% [0%; 75.9%]
## 
## Test of heterogeneity:
##      Q d.f.  p-value
##  10.46    6   0.1065
## 
## Details on meta-analytical method:
## - Mantel-Haenszel method
## - DerSimonian-Laird estimator for tau^2
```
### 发表偏倚的识别

#### 绘制漏斗图
**meta**包的`funnel()`函数可用于绘制漏斗图

```r
funnel(m)
```

![plot of chunk unnamed-chunk-6](/figures/course/2024-01-15-meta-r/meta_r/unnamed-chunk-6-1.png)
从上图可以看出，漏斗图的散点分布不对称，该Meta分析存在发表偏倚的可能。
### 敏感性分析
**meta**包的`metainf()`函数可用于效应量的合并

```r
metainf(m, pooled = "fixed")
```

```
## 
## Influential analysis (Fixed effect model)
## 
##                            OR           95%-CI  p-value    tau^2     I^2
## Omitting MRC-1 1974    0.9027 [0.8452; 0.9641]   0.0023   0.0099   42.3%
## Omitting CDP 1976      0.9038 [0.8462; 0.9652]   0.0026   0.0082   37.9%
## Omitting MRC-2 1979    0.9025 [0.8443; 0.9648]   0.0026   0.0129   46.3%
## Omitting GASP 1979     0.8986 [0.8417; 0.9594]   0.0014   0.0123   48.7%
## Omitting PARIS 1980    0.9001 [0.8427; 0.9615]   0.0018   0.0124   47.6%
## Omitting AMIS 1980     0.8702 [0.8122; 0.9324] < 0.0001   0         0.0%
## Omitting ISIS-2 1988   0.9020 [0.7965; 1.0214]   0.104    0.0268   49.7%
##                                                                         
## Pooled estimate        0.8969 [0.8405; 0.9570]   0.001    0.0096   39.7%
## 
## Details on meta-analytical method:
## - Mantel-Haenszel method
```

## 连续型变量资料的Meta分析
以**meta**包的数据集`Fleiss1993cont`为例进行介绍，该数据集包含5项关于心理健康治疗对医疗利用影响的研究结果，

```r
data("Fleiss93cont")
Fleiss93cont
```

```
##     study year n.e mean.e sd.e n.c mean.c  sd.c
## 1   Davis 1973  13    5.0 4.70  13   6.50  3.80
## 2 Florell 1971  30    4.9 1.71  50   6.10  2.30
## 3   Gruen 1975  35   22.5 3.44  35  24.90 10.65
## 4    Hart 1975  20   12.5 1.47  20  12.30  1.66
## 5  Wilson 1977   8    6.5 0.76   8   7.38  1.41
```
使用函数`metacont()`合并SMD值

```r
metacont(n.e, mean.e, sd.e, n.c, mean.c, sd.c, sm = "SMD", studlab = paste(study,
  year), data = Fleiss93cont)
```

```
##                  SMD             95%-CI %W(fixed) %W(random)
## Davis 1973   -0.3399 [-1.1152;  0.4354]     11.54      11.54
## Florell 1971 -0.5659 [-1.0274; -0.1044]     32.58      32.58
## Gruen 1975   -0.2999 [-0.7712;  0.1714]     31.23      31.23
## Hart 1975     0.1250 [-0.4954;  0.7455]     18.02      18.02
## Wilson 1977  -0.7346 [-1.7575;  0.2883]      6.63       6.63
## 
## Number of studies combined: k=5
## 
##                          SMD           95%-CI      z  p-value
## Fixed effect model   -0.3434 [-0.6068; -0.08] -2.555   0.0106
## Random effects model -0.3434 [-0.6068; -0.08] -2.555   0.0106
## 
## Quantifying heterogeneity:
## tau^2 = 0; H = 1 [1; 2.1]; I^2 = 0% [0%; 77.4%]
## 
## Test of heterogeneity:
##     Q d.f.  p-value
##  3.68    4   0.4515
## 
## Details on meta-analytical method:
## - Inverse variance method
## - DerSimonian-Laird estimator for tau^2
```
