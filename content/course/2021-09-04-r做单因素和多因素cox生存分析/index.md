---
title: R做单因素和多因素Cox生存分析
author: 欧阳松
date: '2021-09-04'
slug: Cox-survival-analysis
categories:
  - 教程
  - R
tags:
  - 教程
  - Cox
from_Rmd: yes
---

比如你筛选了某个基因，或者某个因素，突然审稿人跟你说要你做单因素和多因素的Cox分析，让你评估是否是某个危险因素的独立影响因素，这个可以在survival包里实现。

关于单因素Cox回归分析，很好理解，只要挖掘处*p*\<0.05的因素就行，但是对于多因素Cox分析，有的文献说是要挑选*p*\<0.05的因素，有的文献说要挑选*p*\<0.2的因素，这个其实都是可以设置的。。。

我们以survival包自带的lung数据进行演示


```r
data <- survival::lung  #加载示例数据
```

我们查看一下这个数据的前几个示例

```         
# head(data)
```


| inst| time| status| age| sex| ph.ecog| ph.karno| pat.karno| meal.cal| wt.loss|
|----:|----:|------:|---:|---:|-------:|--------:|---------:|--------:|-------:|
|    3|  306|      2|  74|   1|       1|       90|       100|     1175|      NA|
|    3|  455|      2|  68|   1|       0|       90|        90|     1225|      15|
|    3| 1010|      1|  56|   1|       0|       90|        90|       NA|      15|
|    5|  210|      2|  57|   1|       1|       90|        60|     1150|      11|
|    1|  883|      2|  60|   1|       0|      100|        90|       NA|       0|
|   12| 1022|      1|  74|   1|       1|       50|        80|      513|       0|

在运行之前，我们需要加载这几个包

```         
library(dplyr)
library(survival)
library(plyr)
```



## **单因素Cox回归**

我们可以先单独看某一个因素的结果，比如说sex是否对生存有影响，可以这样


```r
surv <- Surv(time = data$time, event = data$status)
data$surv <- with(data, surv)  #添加一列
sexCox <- coxph(surv ~ sex, data = data)  #计算sex的Cox
sexSum <- summary(sexCox)  #总结一下
sexSum$coefficients  #检索出HR和P值
```

```
##       coef exp(coef) se(coef)      z Pr(>|z|)
## sex -0.531     0.588   0.1672 -3.176 0.001491
```

```r
sexSum$conf.int  #检索出HR饿95%CI
```

```
##     exp(coef) exp(-coef) lower .95 upper .95
## sex     0.588      1.701    0.4237     0.816
```

```r
round(sexSum$conf.int[, 3:4], 2)  #设置小数点两位数
```

```
## lower .95 upper .95 
##      0.42      0.82
```

```r
CI <- paste0(round(sexSum$conf.int[, 3:4], 2), collapse = "-")  #将95%CI合并
Pvalue <- round(sexSum$coefficients[, 5], 3)  #提取小数点三位数的p值
HR <- round(sexSum$coefficients[, 2], 2)  #提取小数点两位数的HR值

### 查看一下结果
Unicox <- data.frame(Characteristics = "sex", `Hazard Ratio` = HR, CI95 = CI, `P value` = Pvalue)
```

我们看一下sex的单因素结果，见表 \@ref(tab:sexunicox)所示。


Table: sex的单因素Cox结果

|Characteristics | Hazard.Ratio|CI95      | P.value|
|:---------------|------------:|:---------|-------:|
|sex             |         0.59|0.42-0.82 |   0.001|

然而，对于多个因素，我们就可以设置一个函数，可以把所有的因素都算进去


```r
# 添加多个参数,定义函数
UniCox <- function(x) {
  FML <- as.formula(paste0("surv~", x))
  Cox <- coxph(FML, data = data)
  Sum <- summary(Cox)
  CI <- paste0(round(Sum$conf.int[, 3:4], 2), collapse = "-")
  Pvalue <- round(Sum$coefficients[, 5], 3)
  HR <- round(Sum$coefficients[, 2], 2)
  Unicox <- data.frame(Characteristics = x, `Hazard Ratio` = HR, CI95 = CI, `P value` = Pvalue)
  return(Unicox)
}

# 如查看sex
UniCox("sex")
```

```
##   Characteristics Hazard.Ratio      CI95 P.value
## 1             sex         0.59 0.42-0.82   0.001
```

```r
varNames <- colnames(data)[c(4:10)]  #[]里代表需要的列数
UniVar <- lapply(varNames, UniCox)
UniVar <- ldply(UniVar, data.frame)
```

我们看一下所有统计的结果，见表 \@ref(tab:unicox)所示。


Table: 单因素Cox结果

|Characteristics | Hazard.Ratio|CI95      | P.value|
|:---------------|------------:|:---------|-------:|
|age             |         1.02|1-1.04    |   0.042|
|sex             |         0.59|0.42-0.82 |   0.001|
|ph.ecog         |         1.61|1.29-2.01 |   0.000|
|ph.karno        |         0.98|0.97-1    |   0.005|
|pat.karno       |         0.98|0.97-0.99 |   0.000|
|meal.cal        |         1.00|1-1       |   0.593|
|wt.loss         |         1.00|0.99-1.01 |   0.828|

我们还可以筛选一下所有p值小于0.05的因素


```r
# 筛选p<0.05
UniVar$Characteristics[UniVar$P.value < 0.05]
```

```
## [1] "age"       "sex"       "ph.ecog"   "ph.karno"  "pat.karno"
```

------------------------------------------------------------------------

## **多因素Cox分析**

提取出p\<0.05的元素以后，我们可以对所有p\<0.05的因素再次进行多因素的Cox回归分析，如果说普通的多因素Cox，我们用+号选择需要的因素进行计算，比如下面这样：

```         
MultiCox<-coxph(surv~age+sex+ph.ecog+ph.karno,data =data) #查某几个因素就使用+
```

但是，我们我们需要所有单因素Cox结果里面p\<0.05的因素，我们就可以用函数批量计算\
我们先定义函数，然后整合到一个表里


```r
# 定义p<0.05的函数
fml <- as.formula(paste0("surv~", paste0(UniVar$Characteristics[UniVar$P.value <
  0.05], collapse = "+")))

multicox <- coxph(fml, data = data)  #定义所有p<0.05的参数
multisum <- summary(multicox)  #提取结果

multiname <- as.character(UniVar$Characteristics[UniVar$P.value < 0.05])
mCIL <- round(multisum$conf.int[, 3], 2)
mCIU <- round(multisum$conf.int[, 4], 2)
mCI <- paste0(mCIL, "-", mCIU)
# 或者这样 mCI<-paste0(round(multisum$conf.int[,3:4],2),collapse = '-')
mPvalue <- round(multisum$coefficients[, 5], 3)
mHR <- round(multisum$coefficients[, 2], 2)
# 把所有结果导入到一个mulcox的表格里
mulcox <- data.frame(Characteristics = multiname, `Hazard Ratio` = mHR, CI95 = mCI,
  `P value` = mPvalue)
```

这样，我们生存一个叫mulcox的多因素Cox回归结果的表格，见表 \@ref(tab:mulcox)所示。


Table: 多因素Cox结果

|          |Characteristics | Hazard.Ratio|CI95      | P.value|
|:---------|:---------------|------------:|:---------|-------:|
|age       |age             |         1.01|0.99-1.03 |   0.231|
|sex       |sex             |         0.57|0.41-0.8  |   0.001|
|ph.ecog   |ph.ecog         |         1.76|1.22-2.54 |   0.002|
|ph.karno  |ph.karno        |         1.02|1-1.04    |   0.108|
|pat.karno |pat.karno       |         0.99|0.98-1    |   0.142|

------------------------------------------------------------------------

## **单因素和多因素的整合**

我们分别获得了单因素和多因素Cox回归的结果，最后我们需要把两个结果合并到一个表格里面，这里我们就可以用到`merge()`函数，见表 \@ref(tab:Final)所示


```r
Final <- merge.data.frame(UniVar, mulcox, by = "Characteristics", all = T, sort = T)
```


Table: 单因素和多因素Cox整合结果

|Characteristics | Hazard.Ratio.x|CI95.x    | P.value.x| Hazard.Ratio.y|CI95.y    | P.value.y|
|:---------------|--------------:|:---------|---------:|--------------:|:---------|---------:|
|age             |           1.02|1-1.04    |     0.042|           1.01|0.99-1.03 |     0.231|
|meal.cal        |           1.00|1-1       |     0.593|             NA|NA        |        NA|
|pat.karno       |           0.98|0.97-0.99 |     0.000|           0.99|0.98-1    |     0.142|
|ph.ecog         |           1.61|1.29-2.01 |     0.000|           1.76|1.22-2.54 |     0.002|
|ph.karno        |           0.98|0.97-1    |     0.005|           1.02|1-1.04    |     0.108|
|sex             |           0.59|0.42-0.82 |     0.001|           0.57|0.41-0.8  |     0.001|
|wt.loss         |           1.00|0.99-1.01 |     0.828|             NA|NA        |        NA|

------------------------------------------------------------------------

这样一个代表所有单因素和多因素Cox回归生存分析的表格就自动合成了，学会了吗？如果想导出成excel格式就可以输入下面的代码：

```         
write.csv(Final,"coxreg.csv", row.names = FALSE)
write.csv(Final,"coxreg.csv",row.names = F,showNA=F ) #不显示NA值
```
