---
title: 应用R语言BUGSnet程序包实现贝叶斯网状Meta分析
date: '2024-01-01'
slug: bugsnet-meta
categories:
  - 网状meta
  - meta分析
tags:
  - meta分析
  - 网状meta
from_Rmd: yes
---

-   向虹,岳磊,赵红,李华,谢天宏,杨婷,王杰,张宇昊,谢忠平.应用R语言BUGSnet程序包实现贝叶斯网状Meta分析[J].中国循证医学杂志,2022,22(05):600-608.

传统的meta分析为两组数据比较，如果需要多组数据的两两比较，则需要应用网状meta分析，本文分享使用**BUGSnet**包使用的方法

## 准备数据

数据来源于一项关于类固醇佐剂治疗天疱疮的网状Meta分析研究，该研究纳入 10 个试验，纳入了592 例患者，评估了 7 种方法治疗天疱疮的效果，下载地址[meta.csv](/course/2024-01-01-r-bugsnet-meta/meta.csv)。

```
data_total<-read.csv("meta.csv")
data_total[,1:5]
```


```r
data_total <- read.csv("/Users/mac/Documents/GitHub/blog/content/course/2024-01-01-r-bugsnet-meta/meta.csv")
knitr::kable(data_total[, 1:5])
```



|Study          |Treatment     | Number.of.patients| Number.of.remissions| Number.of.relapses|
|:--------------|:-------------|------------------:|--------------------:|------------------:|
|Beissert 2006  |MMF           |                 21|                   20|                 NA|
|Beissert 2006  |AZA           |                 19|                   13|                 NA|
|Beissert 2010  |Steroid alone |                 37|                   23|                 14|
|Beissert 2010  |MMF           |                 59|                   40|                 16|
|Davatchi 2007  |Steroid alone |                 30|                   23|                 NA|
|Davatchi 2007  |AZA           |                 30|                   24|                 NA|
|Davatchi 2007  |MMF           |                 30|                   21|                 NA|
|Davatchi 2007  |CP_P          |                 30|                   22|                 NA|
|Davatchi 2013  |Steroid alone |                 28|                   11|                  9|
|Davatchi 2013  |AZA           |                 28|                   15|                  6|
|Ioannides 2000 |Steroid alone |                 17|                   11|                  2|
|Ioannides 2000 |Cyclosporine  |                 16|                   11|                  1|
|Ioannides 2012 |Steroid alone |                 23|                   18|                  2|
|Ioannides 2012 |MMF           |                 24|                   20|                  3|
|Rose 2005      |AZA           |                 11|                    8|                 NA|
|Rose 2005      |DCP_C(6M)     |                 11|                    4|                 NA|
|Sethy 2009     |DCP_C(12M)    |                 15|                    9|                  5|
|Sethy 2009     |CP_P          |                 13|                    6|                  4|
|Sharma 2013    |CP_P          |                 34|                   30|                 14|
|Sharma 2013    |Steroid alone |                 26|                   19|                 10|
|Joly 2017      |Rituximab     |                 46|                   41|                 11|
|Joly 2017      |Steroid alone |                 44|                   16|                 20|
## 安装软件包

R语言**BUGSnet包**实际就是来调用JAGS软件来实现贝叶斯网状meta分析的，所以要**事先安装好JAGS**

[JAGS官网](https://mcmc-jags.sourceforge.io/)在此，大家自行下载安装

目前[BUGSnet包](https://github.com/audrey-b/BUGSnet)托管在GitHub上，需要一点技术去安装

```
remotes::install_github("audrey-b/BUGSnet")
## ipkg::install_github("audrey-b/BUGSnet")
```
## 网状meta分析具体实现过程

### 录入、整理数据

```r
library(dplyr)  #导入dplyr包
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(tidyr)  #导入tidyr包；目的是实现管道操作及一系列tidy风格的函数

data_total <- read.csv("meta.csv", 1)  #导入编辑好的数据文件,csv格式，meta.csv是文件名称
str(data_total)  #查看导入的数据信息
```

```
## 'data.frame':	22 obs. of  8 variables:
##  $ Study                                      : chr  "Beissert 2006" "Beissert 2006" "Beissert 2010" "Beissert 2010" ...
##  $ Treatment                                  : chr  "MMF" "AZA" "Steroid alone" "MMF" ...
##  $ Number.of.patients                         : int  21 19 37 59 30 30 30 30 28 28 ...
##  $ Number.of.remissions                       : int  20 13 23 40 23 24 21 22 11 15 ...
##  $ Number.of.relapses                         : int  NA NA 14 16 NA NA NA NA 9 6 ...
##  $ Number.of.adverse.event.related.withdrawals: int  0 2 1 2 1 2 1 0 NA NA ...
##  $ Cumulative.dose.of.steroid_Mean            : num  9334 8916 8570 8078 11631 ...
##  $ Cumulative.dose.of.steroid_SD              : num  13280 29844 3501 3494 7740 ...
```

```r
data_remission <- data_total[, c(1:4)]  #拿到第1~4列，并命名为data_remission
data_relapse <- data_total[, c(1:3, 5)]  #拿到第1~3列和第5列，并重命名
data_withdrawal <- data_total[, c(1:3, 6)]  #拿到第1~3列和第6列，并重命名
data_dose <- data_total[, c(1:3, 7:8)]  #拿到第1~3列和第7~8列，并重命名

# 下面代码重命名结局指标 data_remission代表缓解病例数据，为主要疗效指标
data_remission <- data_remission %>%
  rename(sampleSize = Number.of.patients, events = Number.of.remissions)  #将data_remission内的变量重命名，然后将整个数据依然命名为data_remission
# 去除缺失值后重命名 data_relapse代表复发病例数据，为次要疗效指标
data_relapse <- data_relapse %>%
  drop_na() %>%
  rename(sampleSize = Number.of.patients, events = Number.of.relapses)  #重命名

# data_withdrawal代表不良事件相关退出病例数据，为次要安全性指标
data_withdrawal <- data_withdrawal %>%
  drop_na() %>%
  rename(sampleSize = Number.of.patients, events = Number.of.adverse.event.related.withdrawals)  #重命名

# data_dose代表固醇的累积使用剂量，为主要安全性指标
data_dose <- data_dose %>%
  drop_na() %>%
  rename(sampleSize = Number.of.patients, mean = Cumulative.dose.of.steroid_Mean,
    SD = Cumulative.dose.of.steroid_SD)  #重命名
```

### 绘制网络关系图


```r
library(BUGSnet)  #导入BUGSnet包
```

```
## 
## This version of BUGSnet is distributed under the Creative Commons
## Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0).
## You are responsible for conforming to the terms of this license.
## Commercial use is strictly prohibited. Contact the authors for more information.
```

```r
# 预处理，指定目标数据及变量名
data_remission <- data.prep(arm.data = data_remission, varname.t = "Treatment", varname.s = "Study")
data_dose <- data.prep(arm.data = data_dose, varname.t = "Treatment", varname.s = "Study")
# 绘制描述性统计图（网络关系图）
par(mfrow = c(1, 2))  #在同一张图上显示1x2个图
net.plot(data_remission, node.colour = "darkgrey")  #绘制散点图，连线颜色为darkgrey
net.plot(data_dose, node.colour = "darkgrey")
```

![输出的网络关系图](/figures/course/2024-01-01-r-bugsnet-meta/bugsnet-meta/fig1-1.png)

### 设置模型类型


```r
# 使用nma.model函数设置拟合哪种模型(固定效应模型或随机效应模型)
# 设置固定效应模型
fixed_effects_model_remission <- nma.model(data_remission, outcome = "events", N = "sampleSize",
  reference = "Steroid alone", family = "binomial", link = "logit", effects = "fixed")

fixed_effects_model_dose <- nma.model(data = data_dose, outcome = "mean", N = "sampleSize",
  sd = "SD", reference = "Steroid alone", family = "normal", link = "identity",
  effects = "fixed")
# nma.model参数解释 outcome指定结局变量 reference设置对照组，本例为Steroid
# alone（单独使用类固醇治疗）
# family用于指定结果的分布类型binomial（二分类资料），normal（计量资料），poisson（计数资料）；
# link用于指定NMA模型使用的函数，logit用于OR，log用于RR或计数数据的比率（Rate
# Ratio）； cloglog用于二分类数据危险比（HR）；identity用于计量资料；
# effects用于设置效应模型的类型。

# 设置随机效应模型
random_effects_model_remission <- nma.model(data_remission, outcome = "events", N = "sampleSize",
  reference = "Steroid alone", family = "binomial", link = "logit", effects = "random")

random_effects_model_dose <- nma.model(data = data_dose, outcome = "mean", N = "sampleSize",
  sd = "SD", reference = "Steroid alone", family = "normal", link = "identity",
  effects = "random")
```

### 执行贝叶斯网状Meta分析


```r
# 使用nma.run函数执行贝叶斯网状Meta分析
set.seed(20222022)  #随机数字生成器
fixed_effects_results_remission <- nma.run(fixed_effects_model_remission, n.adapt = 1000,
  n.burnin = 1000, n.iter = 10000)
```

```
## Compiling model graph
##    Resolving undeclared variables
##    Allocating nodes
## Graph information:
##    Observed stochastic nodes: 22
##    Unobserved stochastic nodes: 17
##    Total graph size: 459
## 
## Initializing model
```

```r
fixed_effects_results_dose <- nma.run(fixed_effects_model_dose, n.adapt = 1000, n.burnin = 1000,
  n.iter = 10000)
```

```
## Compiling model graph
##    Resolving undeclared variables
##    Allocating nodes
## Graph information:
##    Observed stochastic nodes: 12
##    Unobserved stochastic nodes: 10
##    Total graph size: 134
## 
## Initializing model
```

```r
random_effects_results_remission <- nma.run(random_effects_model_remission, n.adapt = 1000,
  n.burnin = 1000, n.iter = 10000)
```

```
## Compiling model graph
##    Resolving undeclared variables
##    Allocating nodes
## Graph information:
##    Observed stochastic nodes: 22
##    Unobserved stochastic nodes: 30
##    Total graph size: 514
## 
## Initializing model
```

```r
random_effects_results_dose <- nma.run(random_effects_model_dose, n.adapt = 1000,
  n.burnin = 1000, n.iter = 10000)
```

```
## Compiling model graph
##    Resolving undeclared variables
##    Allocating nodes
## Graph information:
##    Observed stochastic nodes: 12
##    Unobserved stochastic nodes: 18
##    Total graph size: 179
## 
## Initializing model
```

```r
# 评估效应模型，并输出杠杆图
par(mfrow = c(2, 2))
nma.fit(fixed_effects_results_remission)
```

```
## $DIC
## [1] 43.04
## 
## $Dres
## [1] 25.62
## 
## $pD
## [1] 17.43
## 
## $leverage
##                                                                          
## 1 0.9517 0.675 0.4356 0.6945 1.035 0.6248 1.019 1.054 1.027 0.8333 0.2667
##                                                                          
## 1 0.8006 0.5404 0.7124 1.025 0.5566 1.024 1.047 1.031 0.5652 0.8732 0.635
## 
## $w
##  r.1.1.  r.2.1.  r.3.1.  r.4.1.  r.5.1.  r.6.1.  r.7.1.  r.8.1.  r.9.1. r.10.1. 
## -1.7101  0.8399  1.0196 -0.9396 -1.0175 -0.7904 -1.0095  1.0268 -1.0137 -1.1131 
##  r.1.2.  r.2.2.  r.3.2.  r.4.2.  r.5.2.  r.6.2.  r.7.2.  r.8.2.  r.9.2. r.10.2. 
##  1.7877 -0.9044  0.9641  0.9442 -1.0122 -0.7462  1.0118 -1.0233  1.0155  0.9938 
##  r.3.3.  r.3.4. 
## -1.0852 -1.1746 
## 
## $pmdev
##  dev_a.1.1.  dev_a.2.1.  dev_a.3.1.  dev_a.4.1.  dev_a.5.1.  dev_a.6.1. 
##      2.9246      0.7054      1.0396      0.8828      1.0354      0.6248 
##  dev_a.7.1.  dev_a.8.1.  dev_a.9.1. dev_a.10.1.  dev_a.1.2.  dev_a.2.2. 
##      1.0191      1.0542      1.0276      1.2391      3.1958      0.8180 
##  dev_a.3.2.  dev_a.4.2.  dev_a.5.2.  dev_a.6.2.  dev_a.7.2.  dev_a.8.2. 
##      0.9294      0.8915      1.0245      0.5568      1.0238      1.0471 
##  dev_a.9.2. dev_a.10.2.  dev_a.3.3.  dev_a.3.4. 
##      1.0311      0.9876      1.1777      1.3797
```

```r
nma.fit(fixed_effects_results_dose)
```

```
## $DIC
## [1] 20.91
## 
## $Dres
## [1] 10.79
## 
## $pD
## [1] 10.12
## 
## $leverage
##                                                                              
## 1 0.1575 0.9275 0.3865 1.012 0.9899 0.8439 0.9449 0.9902 1.012 1 1.005 0.8527
## 
## $w
##  y.1.1.  y.2.1.  y.3.1.  y.4.1.  y.5.1.  y.1.2.  y.2.2.  y.3.2.  y.4.2.  y.5.2. 
##  0.4568 -0.9877  0.9268  1.0062  0.9950 -0.9238  0.9869 -0.9951 -1.0061 -1.0001 
##  y.3.3.  y.3.4. 
##  1.0024 -0.9510 
## 
## $pmdev
## dev_a.1.1. dev_a.2.1. dev_a.3.1. dev_a.4.1. dev_a.5.1. dev_a.1.2. dev_a.2.2. 
##     0.2087     0.9756     0.8589     1.0125     0.9900     0.8535     0.9740 
## dev_a.3.2. dev_a.4.2. dev_a.5.2. dev_a.3.3. dev_a.3.4. 
##     0.9902     1.0123     1.0002     1.0048     0.9045
```

```r
nma.fit(random_effects_results_remission)
```

```
## $DIC
## [1] 43.41
## 
## $Dres
## [1] 23.16
## 
## $pD
## [1] 20.25
## 
## $leverage
##                                                                          
## 1 1.166 0.8502 0.7564 0.8672 1.067 0.8125 1.009 1.095 1.017 0.9692 0.5095
##                                                                            
## 1 0.9211 0.7463 0.8866 1.042 0.7735 0.9867 1.057 1.027 0.7945 0.9718 0.9279
## 
## $w
##  r.1.1.  r.2.1.  r.3.1.  r.4.1.  r.5.1.  r.6.1.  r.7.1.  r.8.1.  r.9.1. r.10.1. 
## -1.3355  0.9299  0.9806 -0.9541 -1.0332  0.9021 -1.0046 -1.0473  1.0084 -1.0255 
##  r.1.2.  r.2.2.  r.3.2.  r.4.2.  r.5.2.  r.6.2.  r.7.2.  r.8.2.  r.9.2. r.10.2. 
##  1.3514 -0.9646  0.9427  0.9633  1.0210 -0.8807  0.9935  1.0285 -1.0134  0.9540 
##  r.3.3.  r.3.4. 
## -1.0343 -1.0689 
## 
## $pmdev
##  dev_a.1.1.  dev_a.2.1.  dev_a.3.1.  dev_a.4.1.  dev_a.5.1.  dev_a.6.1. 
##      1.7836      0.8647      0.9616      0.9103      1.0676      0.8139 
##  dev_a.7.1.  dev_a.8.1.  dev_a.9.1. dev_a.10.1.  dev_a.1.2.  dev_a.2.2. 
##      1.0092      1.0967      1.0168      1.0516      1.8263      0.9305 
##  dev_a.3.2.  dev_a.4.2.  dev_a.5.2.  dev_a.6.2.  dev_a.7.2.  dev_a.8.2. 
##      0.8887      0.9280      1.0424      0.7756      0.9870      1.0577 
##  dev_a.9.2. dev_a.10.2.  dev_a.3.3.  dev_a.3.4. 
##      1.0269      0.9102      1.0697      1.1426
```

```r
nma.fit(random_effects_results_dose)
```

![plot of chunk unnamed-chunk-4](/figures/course/2024-01-01-r-bugsnet-meta/bugsnet-meta/unnamed-chunk-4-1.png)

```
## $DIC
## [1] 21.92
## 
## $Dres
## [1] 11.01
## 
## $pD
## [1] 10.91
## 
## $leverage
##                                                                             
## 1 0.3687 0.9731 0.7946 0.9909 0.9844 0.8818 0.9752 1.005 0.9896 0.9905 1.009
##         
## 1 0.9498
## 
## $w
##  y.1.1.  y.2.1.  y.3.1.  y.4.1.  y.5.1.  y.1.2.  y.2.2.  y.3.2.  y.4.2.  y.5.2. 
##  0.6316 -0.9927  0.9107 -0.9956 -0.9922 -0.9423  0.9905 -1.0025 -0.9948 -0.9953 
##  y.3.3.  y.3.4. 
##  1.0043 -0.9788 
## 
## $pmdev
## dev_a.1.1. dev_a.2.1. dev_a.3.1. dev_a.4.1. dev_a.5.1. dev_a.1.2. dev_a.2.2. 
##     0.3989     0.9855     0.8294     0.9913     0.9844     0.8879     0.9811 
## dev_a.3.2. dev_a.4.2. dev_a.5.2. dev_a.3.3. dev_a.3.4. 
##     1.0051     0.9896     0.9905     1.0086     0.9580
```

### 绘制排名图（SUCRA 和 rankogram ）


```r
# 使用nma.rank函数 绘制治疗排名结果图，largerbetter表示更大更好
sucra_out_remission <- nma.rank(random_effects_results_remission, largerbetter = TRUE)
```

```
## Warning: `separate_()` was deprecated in tidyr 1.2.0.
## ℹ Please use `separate()` instead.
## ℹ The deprecated feature was likely used in the BUGSnet package.
##   Please report the issue at <https://github.com/audrey-b/BUGSnet/issues>.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

```r
sucra_out_dose <- nma.rank(fixed_effects_results_dose, largerbetter = FALSE)

# 使用ggplot2（绘图神奇）进行绘图 install.packages('ggplot2')
# #安装包，如果已有请忽略
library(ggplot2)  #导入包

# 设置颜色
z <- scale_colour_manual(values = c(AZA = "#FF6600", MMF = "#FF0088", `Steroid alone` = "#00FF00",
  CP_P = "#007700", Cyclosporine = "#6633CC", `DCP_C(6M)` = "#00CCFF", `DCP_C(12M)` = "#FFFF00",
  Rituximab = "#AA0000"))

# 绘制累积排序概率图下面积（SUCRA）
s1 <- sucra_out_remission$sucraplot + z
```

```
## Scale for colour is already present.
## Adding another scale for colour, which will replace the existing scale.
```

```r
s4 <- sucra_out_dose$sucraplot + z
```

```
## Scale for colour is already present.
## Adding another scale for colour, which will replace the existing scale.
```

```r
# 绘制排序概率图（rankogram）
r1 <- sucra_out_remission$rankogram + theme(axis.text.x = element_text(angle = 30,
  hjust = 1, vjust = 1))
# theme参数是设置主题，包括文字等
r4 <- sucra_out_dose$rankogram + theme(axis.text.x = element_text(angle = 30, hjust = 1,
  vjust = 1))

# 输出图片 install.packages('gridExtra')#安装包
library(gridExtra)  #导包
```

```
## 
## Attaching package: 'gridExtra'
## 
## The following object is masked from 'package:dplyr':
## 
##     combine
```

```r
grid.arrange(s1, s4, r1, r4, ncol = 2)  #2x2
```

![plot of chunk unnamed-chunk-5](/figures/course/2024-01-01-r-bugsnet-meta/bugsnet-meta/unnamed-chunk-5-1.png)
解读：前两幅图是越高越好，后两幅颜色越淡越好，总结：Rituximab疗效最好

### 绘制排名表热图


```r
# nma.league() 函数用于获得排名表热图
league.out_remission <- nma.league(random_effects_results_remission, central.tdcy = "median",
  order = sucra_out_remission$order, log.scale = TRUE, low.colour = "springgreen4",
  mid.colour = "white", high.colour = "red", digits = 2)

# 参数解释 central.tdcy用于设置统计数据，可以设置“mean”或“median” order用来排序
# log.scale=TRUE 表示用对数形式显示数据
# colour用于设置颜色，本例低、中、高设置了3种不同颜色
# digits设定小数点后的显示位数
league.out_dose <- nma.league(fixed_effects_results_dose, central.tdcy = "median",
  order = sucra_out_dose$order, log.scale = TRUE, low.colour = "springgreen4",
  mid.colour = "white", high.colour = "red", digits = 2)

# 绘制排名表热图
l1 <- league.out_remission$heatplot
l4 <- league.out_dose$heatplot
grid.arrange(l1, l4, ncol = 1)  #图片放在一列上输出
```

![plot of chunk unnamed-chunk-6](/figures/course/2024-01-01-r-bugsnet-meta/bugsnet-meta/unnamed-chunk-6-1.png)

```r
# 可以将排名表热图作为表格输出
league.out_remission$table
```

```
##               Rituximab              DCP_C(12M)             
## Rituximab     "Rituximab"            "-1.65 (-5.35 to 2.00)"
## DCP_C(12M)    "1.65 (-2.00 to 5.35)" "DCP_C(12M)"           
## MMF           "2.31 (-0.25 to 4.92)" "0.69 (-2.39 to 3.56)" 
## CP_P          "2.27 (-0.38 to 5.09)" "0.63 (-1.86 to 3.12)" 
## AZA           "2.50 (0.05 to 5.33)"  "0.89 (-2.02 to 4.02)" 
## Cyclosporine  "2.57 (-0.75 to 5.93)" "0.93 (-2.92 to 4.66)" 
## Steroid alone "2.73 (0.58 to 5.08)"  "1.11 (-1.80 to 4.00)" 
## DCP_C(6M)     "4.32 (0.71 to 8.10)"  "2.63 (-1.30 to 6.80)" 
##               MMF                     CP_P                   
## Rituximab     "-2.31 (-4.92 to 0.25)" "-2.27 (-5.09 to 0.38)"
## DCP_C(12M)    "-0.69 (-3.56 to 2.39)" "-0.63 (-3.12 to 1.86)"
## MMF           "MMF"                   "0.04 (-1.72 to 1.65)" 
## CP_P          "-0.04 (-1.65 to 1.72)" "CP_P"                 
## AZA           "0.21 (-1.07 to 1.85)"  "0.24 (-1.40 to 2.16)" 
## Cyclosporine  "0.25 (-2.45 to 3.04)"  "0.30 (-2.62 to 3.16)" 
## Steroid alone "0.44 (-0.72 to 1.71)"  "0.47 (-1.02 to 2.01)" 
## DCP_C(6M)     "1.99 (-0.94 to 5.12)"  "2.03 (-1.12 to 5.31)" 
##               AZA                      Cyclosporine           
## Rituximab     "-2.50 (-5.33 to -0.05)" "-2.57 (-5.93 to 0.75)"
## DCP_C(12M)    "-0.89 (-4.02 to 2.02)"  "-0.93 (-4.66 to 2.92)"
## MMF           "-0.21 (-1.85 to 1.07)"  "-0.25 (-3.04 to 2.45)"
## CP_P          "-0.24 (-2.16 to 1.40)"  "-0.30 (-3.16 to 2.62)"
## AZA           "AZA"                    "-0.04 (-2.72 to 2.90)"
## Cyclosporine  "0.04 (-2.90 to 2.72)"   "Cyclosporine"         
## Steroid alone "0.23 (-1.29 to 1.50)"   "0.17 (-2.25 to 2.67)" 
## DCP_C(6M)     "1.74 (-0.94 to 4.47)"   "1.69 (-2.07 to 5.72)" 
##               Steroid alone            DCP_C(6M)               
## Rituximab     "-2.73 (-5.08 to -0.58)" "-4.32 (-8.10 to -0.71)"
## DCP_C(12M)    "-1.11 (-4.00 to 1.80)"  "-2.63 (-6.80 to 1.30)" 
## MMF           "-0.44 (-1.71 to 0.72)"  "-1.99 (-5.12 to 0.94)" 
## CP_P          "-0.47 (-2.01 to 1.02)"  "-2.03 (-5.31 to 1.12)" 
## AZA           "-0.23 (-1.50 to 1.29)"  "-1.74 (-4.47 to 0.94)" 
## Cyclosporine  "-0.17 (-2.67 to 2.25)"  "-1.69 (-5.72 to 2.07)" 
## Steroid alone "Steroid alone"          "-1.56 (-4.62 to 1.37)" 
## DCP_C(6M)     "1.56 (-1.37 to 4.62)"   "DCP_C(6M)"
```

```r
league.out_remission$longtable
```

```
##        Treatment    Comparator   median      lci      uci
## 1      Rituximab     Rituximab  0.00000  0.00000  0.00000
## 2     DCP_C(12M)     Rituximab -1.65080 -5.34720  2.00022
## 3            MMF     Rituximab -2.31006 -4.91748  0.25449
## 4           CP_P     Rituximab -2.26994 -5.09348  0.37906
## 5            AZA     Rituximab -2.50417 -5.33034 -0.05243
## 6   Cyclosporine     Rituximab -2.57034 -5.92851  0.75029
## 7  Steroid alone     Rituximab -2.73117 -5.08205 -0.57898
## 8      DCP_C(6M)     Rituximab -4.31879 -8.10040 -0.70938
## 9      Rituximab    DCP_C(12M)  1.65080 -2.00022  5.34720
## 10    DCP_C(12M)    DCP_C(12M)  0.00000  0.00000  0.00000
## 11           MMF    DCP_C(12M) -0.68938 -3.56267  2.39296
## 12          CP_P    DCP_C(12M) -0.63007 -3.12118  1.85552
## 13           AZA    DCP_C(12M) -0.88667 -4.02428  2.02140
## 14  Cyclosporine    DCP_C(12M) -0.93265 -4.65594  2.92095
## 15 Steroid alone    DCP_C(12M) -1.11172 -4.00350  1.80100
## 16     DCP_C(6M)    DCP_C(12M) -2.63288 -6.79521  1.29658
## 17     Rituximab           MMF  2.31006 -0.25449  4.91748
## 18    DCP_C(12M)           MMF  0.68938 -2.39296  3.56267
## 19           MMF           MMF  0.00000  0.00000  0.00000
## 20          CP_P           MMF  0.03775 -1.72317  1.65472
## 21           AZA           MMF -0.21027 -1.85249  1.07409
## 22  Cyclosporine           MMF -0.25008 -3.04152  2.45248
## 23 Steroid alone           MMF -0.43702 -1.71354  0.71791
## 24     DCP_C(6M)           MMF -1.99076 -5.12367  0.93547
## 25     Rituximab          CP_P  2.26994 -0.37906  5.09348
## 26    DCP_C(12M)          CP_P  0.63007 -1.85552  3.12118
## 27           MMF          CP_P -0.03775 -1.65472  1.72317
## 28          CP_P          CP_P  0.00000  0.00000  0.00000
## 29           AZA          CP_P -0.24452 -2.15743  1.40092
## 30  Cyclosporine          CP_P -0.30421 -3.15761  2.62355
## 31 Steroid alone          CP_P -0.46592 -2.00531  1.02470
## 32     DCP_C(6M)          CP_P -2.02546 -5.30633  1.12177
## 33     Rituximab           AZA  2.50417  0.05243  5.33034
## 34    DCP_C(12M)           AZA  0.88667 -2.02140  4.02428
## 35           MMF           AZA  0.21027 -1.07409  1.85249
## 36          CP_P           AZA  0.24452 -1.40092  2.15743
## 37           AZA           AZA  0.00000  0.00000  0.00000
## 38  Cyclosporine           AZA -0.04109 -2.71582  2.89848
## 39 Steroid alone           AZA -0.22781 -1.49731  1.28569
## 40     DCP_C(6M)           AZA -1.74382 -4.46540  0.94412
## 41     Rituximab  Cyclosporine  2.57034 -0.75029  5.92851
## 42    DCP_C(12M)  Cyclosporine  0.93265 -2.92095  4.65594
## 43           MMF  Cyclosporine  0.25008 -2.45248  3.04152
## 44          CP_P  Cyclosporine  0.30421 -2.62355  3.15761
## 45           AZA  Cyclosporine  0.04109 -2.89848  2.71582
## 46  Cyclosporine  Cyclosporine  0.00000  0.00000  0.00000
## 47 Steroid alone  Cyclosporine -0.17369 -2.67493  2.24686
## 48     DCP_C(6M)  Cyclosporine -1.69313 -5.72384  2.06672
## 49     Rituximab Steroid alone  2.73117  0.57898  5.08205
## 50    DCP_C(12M) Steroid alone  1.11172 -1.80100  4.00350
## 51           MMF Steroid alone  0.43702 -0.71791  1.71354
## 52          CP_P Steroid alone  0.46592 -1.02470  2.00531
## 53           AZA Steroid alone  0.22781 -1.28569  1.49731
## 54  Cyclosporine Steroid alone  0.17369 -2.24686  2.67493
## 55 Steroid alone Steroid alone  0.00000  0.00000  0.00000
## 56     DCP_C(6M) Steroid alone -1.56154 -4.61597  1.36957
## 57     Rituximab     DCP_C(6M)  4.31879  0.70938  8.10040
## 58    DCP_C(12M)     DCP_C(6M)  2.63288 -1.29658  6.79521
## 59           MMF     DCP_C(6M)  1.99076 -0.93547  5.12367
## 60          CP_P     DCP_C(6M)  2.02546 -1.12177  5.30633
## 61           AZA     DCP_C(6M)  1.74382 -0.94412  4.46540
## 62  Cyclosporine     DCP_C(6M)  1.69313 -2.06672  5.72384
## 63 Steroid alone     DCP_C(6M)  1.56154 -1.36957  4.61597
## 64     DCP_C(6M)     DCP_C(6M)  0.00000  0.00000  0.00000
```
解读：不同颜色代表不同的效果

### 绘制森林图


```r
# 使用nma.forest() 函数绘制 森林图 参数解释与排名表热图一样
f1 <- nma.forest(random_effects_results_remission, central.tdcy = "median", log.scale = TRUE,
  comparator = "Steroid alone")
f4 <- nma.forest(random_effects_results_remission, central.tdcy = "median", log.scale = TRUE,
  comparator = "Steroid alone")
grid.arrange(f1, f4, ncol = 2)  #输出森林图
```

![plot of chunk unnamed-chunk-7](/figures/course/2024-01-01-r-bugsnet-meta/bugsnet-meta/unnamed-chunk-7-1.png)
解读：Rituximab是最有效的疾病缓解干预措施
