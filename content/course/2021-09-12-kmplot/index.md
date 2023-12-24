---
title: 画一个好看的KM生存曲线
author: ''
date: '2021-09-12'
slug: kmplot
categories:
  - 教程
  - R
tags: [生存分析]
from_Rmd: yes
---

之前我写过一个用[R做单因素和多因素Cox生存分析](http://swcyo.rbind.io/zh/course/cox-survival-analysis/)的教程，可以用来评估某个基因是否作为某个生存状态的独立影响因素，而有时候，我们还需要画生存曲线，用于区分不同组在生存时间上的不同，那么一看好看的生存曲线，需要哪些数据？又是如何利用R画一个好看的生存曲线呢？

目前我们的生存曲线，叫KM法生存曲线，KM法即乘积极限法（product-limit method），是现在生存分析最常用的方法，是由Kaplan和Meier于1958年提出，因此称Kaplan-Meier法，通常简称KM法。KM法首先计算出活过一定时期的病人再活过下一时期的概率（即生存概率），然后将逐个生存概率相乘，即为相应时段的生存率。

------------------------------------------------------------------------

我们不要看那么多前因后果，只需要了解基本的理论就行，我们依然以survival包自带的lung数据进行演示


```r
data <- survival::lung  #加载示例数据
```

我们查看一下这个数据的前10个示例，如表\@ref(tab:lung)所示。


Table: survival包自带的lung数据前10展示

| inst | time | status | age | sex | ph.ecog | ph.karno | pat.karno | meal.cal | wt.loss |
|:----:|:----:|:------:|:---:|:---:|:-------:|:--------:|:---------:|:--------:|:-------:|
|  3   | 306  |   2    | 74  |  1  |    1    |    90    |    100    |   1175   |   NA    |
|  3   | 455  |   2    | 68  |  1  |    0    |    90    |    90     |   1225   |   15    |
|  3   | 1010 |   1    | 56  |  1  |    0    |    90    |    90     |    NA    |   15    |
|  5   | 210  |   2    | 57  |  1  |    1    |    90    |    60     |   1150   |   11    |
|  1   | 883  |   2    | 60  |  1  |    0    |   100    |    90     |    NA    |    0    |
|  12  | 1022 |   1    | 74  |  1  |    1    |    50    |    80     |   513    |    0    |
|  7   | 310  |   2    | 68  |  2  |    2    |    70    |    60     |   384    |   10    |
|  11  | 361  |   2    | 71  |  2  |    2    |    60    |    80     |   538    |    1    |
|  1   | 218  |   2    | 53  |  1  |    1    |    70    |    80     |   825    |   16    |
|  7   | 166  |   2    | 61  |  1  |    2    |    70    |    70     |   271    |   34    |

## 需要的数据格式

比如我们想要比较男女之间的生存曲线差异，那么首先我们就要将性别进行分组，然后统计生存时间和生存状态，这就要求我们在做生存曲线时，至少要有三列数据：

1.  生存时间

2.  生存状态（生还是死）

3.  生存分组

在lung这个数据里，时间是time（天数），状态是status（生或死），分组是sex（1或0），有了这三列数据，就可以绘制一个KM曲线。

在这个分组中，由于sex列中是1和0，我们并不知道哪个是男，哪个是女，所以我们首先要将数字转换为字符，所以我们首先要将其换行为因子形式，假设我们定义1是男，2是女。这里要注意，生存状态必须是数字形式，如果生存状态是"Dead"和"Alive"这种字符的话，我们需要先转换为数字，这是与分组不同的地方。最后，我们可以看一下我们需要的三列数据，如表\@ref(tab:lung2)所示。


```r
data$sex<-factor(data$sex,levels = c(1,2), #设置需要转换的数字
                 labels = c('Female','Male') #定义数字相应的标签
                 )
### 如果生存状态是字符串的话，需要先转换为数字，代码如下，
# data$status=ifelse(data$status=='Dead',1,0) #意思就是如果出现Dead则定义为1，其余为0
```


Table: 将性别的数字转换为字符

| time | status |  sex   |
|:----:|:------:|:------:|
| 306  |   2    | Female |
| 455  |   2    | Female |
| 1010 |   1    | Female |
| 210  |   2    | Female |
| 883  |   2    | Female |
| 1022 |   1    | Female |
| 310  |   2    |  Male  |
| 361  |   2    |  Male  |
| 218  |   2    | Female |
| 166  |   2    | Female |

## 计算生存曲线

使用**survival**包可以很快的计算生存数据，我们先创建生存对象fit，所有的结果都在fit里面


```r
library(survival)
fit <- survfit(Surv(time, status) ~ sex, data = data)
```

我们可以用最简单的`plot()`函数看一下结果，见图\@ref(fig:fit)所示，显然这个图是不够看的。


```r
plot(fit)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-12-kmplot/index/fit-1.png" alt="简单的plot函数"  />
<p class="caption">简单的plot函数</p>
</div>

画出好看的图，需要用到**survminer**这个包，简单需要用`ggsurvplot()`函数，我们先看简单的图，如图\@ref(fig:fit2)所示。

    library(survminer)
    ggsurvplot(fit,data = data)

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-12-kmplot/index/fit2-1.png" alt="最简单的ggsurvplot作图"  />
<p class="caption">最简单的ggsurvplot作图</p>
</div>

这个图细节多了很多，但是仍然缺少一些重要信息，比如p值、置信区间，还有有些杂志可能会要求危险表（risk table），为了知道这些细节，我们就要去看包里的示例和帮助文件

    ggsurvplot(
      fit, #拟合的对象
      data = NULL, #数据
      fun = NULL, #公式
      color = NULL,#自定义颜色
      palette = NULL,#ggsci的配色
      linetype = 1, #线的类型
      conf.int = FALSE, #置信区间
      pval = FALSE, #是否显示P值
      pval.method = FALSE, #是否显示P值的计算方法
      test.for.trend = FALSE,
      surv.median.line = "none", #是否显示中位线
      risk.table = FALSE, #是否显示危险表
      cumevents = FALSE,
      cumcensor = FALSE,
      tables.height = 0.25, #表的高度
      group.by = NULL, #按什么分组
      facet.by = NULL, #按什么分面，适当多组数据
      add.all = FALSE,
      combine = FALSE,
      ggtheme = theme_survminer(), #ggplot2的主题
      tables.theme = ggtheme,
      ...
    )

关于美化生存曲线的教程有很多，具体大家可以自行百度，我这里只介绍我自己喜欢的一个作图风格，主要有下面这几点：

1.  Lancet配色

2.  要显示p值

3.  要显示中位生存时间

4.  要有置信区间，不过不是一大片的置信曲线，而且虚线显示

5.  要有危险表，不过表不是在图的下方，而是在图的内侧

6.  分组位于右上角

7.  要有爽朗的主题

最后的图就是图\@ref(fig:fit3)这个效果


```r
p1<-ggsurvplot(fit,data = data,
           palette = 'lancet', #你可以试着用jcp
           linetype = 1, #曲线类型，默认为1即可
           conf.int = T,conf.int.style='step', #置信区间，按虚线分布
           pval = T,pval.method = T,#显示P值和方法
           surv.median.line = 'hv',#显示中位生存时间
           risk.table = T,risk.table.pos='in',#显示危险表，并置入图内
           legend=c(0.85,0.85),#标签位置
           legend.title="Sex",#更改分组名
           legend.labs=c("Female","Male"),#更改组内数据名，记得不要搞错，最好先看一下
           title="Survival curve", #以前是main=，现在改成了title=
           ggtheme = theme_bw(base_size = 12) #改一下主题，改一下字体大小
           )
p1
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-12-kmplot/index/fit3-1.png" alt="我喜欢的生存曲线风格"  />
<p class="caption">我喜欢的生存曲线风格</p>
</div>

当然，还有很多很多可以调节的地方，具体多看函数多练习。

## 添加HR和95%CI

刚才的图就已经可以满足绝大多数需求了，有时候我们还想知道HR和95%CI怎么办，这个的话就需要先`coxph(Surv())`计算cox回归。

首先构建Cox分析对象，我们可以使用`summary()`函数找到我们所需要的信息


```r
cox <- coxph(Surv(time, status) ~ sex, data = data)  #构建cox对象
res <- summary(cox)  #对cox对象进行总结统计
res  #显示总结数据
```

```
## Call:
## coxph(formula = Surv(time, status) ~ sex, data = data)
## 
##   n= 228, number of events= 165 
## 
##           coef exp(coef) se(coef)     z Pr(>|z|)   
## sexMale -0.531     0.588    0.167 -3.18   0.0015 **
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
##         exp(coef) exp(-coef) lower .95 upper .95
## sexMale     0.588        1.7     0.424     0.816
## 
## Concordance= 0.579  (se = 0.021 )
## Likelihood ratio test= 10.6  on 1 df,   p=0.001
## Wald test            = 10.1  on 1 df,   p=0.001
## Score (logrank) test = 10.3  on 1 df,   p=0.001
```

HR和CI其实就是res中conf.int的结果，第一列的exp(coef) 就是HR，第三列的lower .95和第四列的upper .95分布就是95%CI的区间

而对于Cox分析，也有一个p值，通过res\$coefficients得出的最后一个就是P值，这里要注意，自带的fit对象的P值计算方法是log-rank，这个P值是Cox回归的P值，两者并不完全一样。

如果你觉得这些你听不懂，那么直接运行下面的代码即可，原理就是用使用**ggplot2**的`annotate()`函数拼接数值。最后结果见图\@ref(fig:fit4)所示。


```r
library(ggplot2)
p1$plot+ #直接已经构建好的图,要有$plot
  annotate("text",x=100,y=0.45,label=paste("HR = ",round(res$conf.int[1],2)))+
  annotate("text",x=100,y=0.4,label=paste("95%CI = ",round(res$conf.int[3],2),"-",
                                          round(res$conf.int[4],2)))+
  annotate("text",x=100,y=0.35,label=paste("Cox P = ",round(res$coefficients[5],4)))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-12-kmplot/index/fit4-1.png" alt="添加了HR和95%CI的生存曲线"  />
<p class="caption">添加了HR和95%CI的生存曲线</p>
</div>

当然用annotate手动拼接确实太累了，有这个时间我不如直接用AI添加。。。。

------------------------------------------------------------------------

当然，如果你实在不想敲代码，想傻瓜式作图，那你可以去仙桃学术，<https://www.xiantao.love/products>
