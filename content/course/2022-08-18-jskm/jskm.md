---
title: jskm绘制生存曲线
author: 欧阳松
date: '2022-08-18'
slug: jskm
categories:
  - 教程
  - ggplot2
  - jskm
tags:
  - 生存分析

from_Rmd: yes
---

上个月看了[jskm包，可以使用ggplot2语法绘制生存曲线的R包](https://mp.weixin.qq.com/s/MMXT_Bvf1W0HpGTbFQdACA)这篇文章，介绍了**jskm**这个包绘制生存曲线，其实明眼人都可以看出来只是调用了**survminer**这个包绘制生存曲线，不过减少了语句的书写，对于爱偷懒的人来说，这也是不错的，今天按照官方提供的教程复现一次。

## jskm包的安装和加载

目前**jskm**包的版本上0.4.3版，已经发布到CRAN上，所以可以直接安装，当然也可以使用GitHub安装

    install.packages("jskm")  ## 直接从CRAN上安装

    ## 从github上安装
    install.packages("remotes")
    remotes::install_github("jinseob2kim/jskm")


```r
library(jskm)  ### 加载jskm
```

## jskm包的帮助文档

从包的说明文档里看到有两个函数

-   `jskm()` Creates a Kaplan-Meier plot for survfit object.

-   `suvjskm()` Creates a **Weighted** Kaplan-Meier plot - svykm.object in survey package

可以看出来，jskm函数是创建KM曲线图，而suvjskm是创建加权KM图，具体的语句可以点进去看示例

    jskm(
      sfit,
      table = FALSE,
      xlabs = "Time-to-event",
      ylabs = "Survival probability",
      xlims = c(0, max(sfit$time)),
      ylims = c(0, 1),
      surv.scale = c("default", "percent"),
      ystratalabs = names(sfit$strata),
      ystrataname = "Strata",
      timeby = signif(max(sfit$time)/7, 1),
      main = "",
      pval = FALSE,
      pval.size = 5,
      pval.coord = c(NULL, NULL),
      pval.testname = F,
      marks = TRUE,
      shape = 3,
      legend = TRUE,
      legendposition = c(0.85, 0.8),
      ci = FALSE,
      subs = NULL,
      label.nrisk = "Numbers at risk",
      size.label.nrisk = 10,
      linecols = "Set1",
      dashed = FALSE,
      cumhaz = F,
      cluster.option = "None",
      cluster.var = NULL,
      data = NULL,
      cut.landmark = NULL,
      showpercent = F,
      ...
    )

------------------------------------------------------------------------

    svyjskm(
      sfit,
      xlabs = "Time-to-event",
      ylabs = "Survival probability",
      xlims = NULL,
      ylims = c(0, 1),
      ystratalabs = NULL,
      ystrataname = NULL,
      surv.scale = c("default", "percent"),
      timeby = NULL,
      main = "",
      pval = FALSE,
      pval.size = 5,
      pval.coord = c(NULL, NULL),
      pval.testname = F,
      legend = TRUE,
      legendposition = c(0.85, 0.8),
      ci = NULL,
      linecols = "Set1",
      dashed = FALSE,
      cumhaz = F,
      design = NULL,
      subs = NULL,
      table = F,
      label.nrisk = "Numbers at risk",
      size.label.nrisk = 10,
      cut.landmark = NULL,
      showpercent = F,
      ...
    )

##jskm绘图

## **绘制简单曲线**

我们先调用survival的lung数据集，按照性别分组拟合数据，使用最简单的jskm函数绘图，见Figure \@ref(fig:fig1)所示。


```r
## 加载数据
library(survival)
fit <- survfit(Surv(time,status)~sex, ##创建生存对象
               data=lung)

#Plot the data
jskm(fit)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-18-jskm/jskm/fig1-1.png" alt="简单曲线"  />
<p class="caption">简单曲线</p>
</div>

## 自定义**曲线图形参数**

根据帮助文档语句可以看到可以调节的参数很多，比如risk table（风险表）、xy轴标签，标题、p值、置信区间等

### **添加风险表**

可以使用table参数添加风险表，直接设置table=TRUE（简写为T即可），见Figure \@ref(fig:fig2)所示


```r
jskm(fit, table = T)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-18-jskm/jskm/fig2-1.png" alt="添加风险表"  />
<p class="caption">添加风险表</p>
</div>

### **修改标题、坐标轴名称和范围**

使用main参数调整图形名称；使用xlabs和ylabs修改x轴和y轴的坐标轴名称；使用ystrataname修改分组标签；使用ystratalabs修改组别的名称；使用xlims和ylims来调整x轴和y轴的范围，使用timeby修改时间切割，surv.scale可以修改显示为百分比还是数值。

我们试着修改X轴为Time (day)，设置分组标签为Sex，分组为male和female，同时Y轴显示为百分比，见Figure \@ref(fig:fig3)所示。


```r
jskm(fit, table = T, xlabs = "Time(day)", surv.scale = "percent", ystratalabs = c("male",
  "female"), ystrataname = "Sex", main = "KM-plot", )
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-18-jskm/jskm/fig3-1.png" alt="添加风险表，修改标签标题等"  />
<p class="caption">添加风险表，修改标签标题等</p>
</div>

###添加P值

使用pval设置P值是否在图形上显示；使用pval.size设置P值的文本大小；使用pval.coord设置P值在图上的位置；使用pval.testname参数在图形上显示检验方法名称 。

我们在Figure \@ref(fig:fig3)基础上，设置显示p值和检验方法，见Figure \@ref(fig:fig4)所示。


```r
jskm(fit,table = T,
     xlabs = "Time(day)",
     surv.scale = 'percent',
     ystratalabs = c('male','female'),
     ystrataname = "Sex",
     main = "KM-plot",
     pval = T,
     pval.testname = T,
     pval.size = 5,  ## 设置p值字体大小用这个
     pval.coord = c(200, 0.20),  ## 设置p值的位置
     )
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-18-jskm/jskm/fig4-1.png" alt="添加风险表，修改标签标题、添加P值"  />
<p class="caption">添加风险表，修改标签标题、添加P值</p>
</div>

### 添加置信区间

可以使用ci参数是否显示生存曲线的置信区间，使用linecols参数设置配色等，见Figure \@ref(fig:fig5)所示


```r
jskm(fit,table = T,
     xlabs = "Time(day)",
     surv.scale = 'percent',
     ystratalabs = c('male','female'),
     ystrataname = "Sex",
     main = "KM-plot",
     pval = T,
     ci=T,
     linecols = "Set1", ## 可以设置Set2，Set3，和black等
     )
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-18-jskm/jskm/fig5-1.png" alt="添加风险表，修改标签标题、添加P值，添加置信区间"  />
<p class="caption">添加风险表，修改标签标题、添加P值，添加置信区间</p>
</div>

### Landmark分析

关于landmark分析，我的理解是将生存曲线劈成两段，分别做生存分析，可以用于交叉的情况，

使用cut.landmark参数，设置显示百分比的话再用showpercent参数

比如，我们在第365天的时候分开，然后做分析，见Figure \@ref(fig:fig6)所示


```r
jskm(fit, table = T, xlabs = "Time(day)", surv.scale = "percent", ystratalabs = c("male",
  "female"), ystrataname = "Sex", main = "KM-plot", pval = T, cut.landmark = 365,
  showpercent = T)
```

```
## Warning in Surv(time, status): Invalid status value, converted to NA

## Warning in Surv(time, status): Invalid status value, converted to NA
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-18-jskm/jskm/fig6-1.png" alt="添加风险表，修改标签标题、添加P值，landmark分析"  />
<p class="caption">添加风险表，修改标签标题、添加P值，landmark分析</p>
</div>

### 设置ggplot函数

可以适当使用ggplot2语句进行再次绘图，不过需要设置table=F，见Figure \@ref(fig:fig7)所示


```r
library(ggplot2)
jskm(fit, table = F, xlabs = "Time(day)", surv.scale = "percent", ystratalabs = c("male",
  "female"), ystrataname = "Sex", main = "KM-plot", pval = T, cut.landmark = 365,
  showpercent = T) + theme_bw(base_size = 12)
```

```
## Warning in Surv(time, status): Invalid status value, converted to NA

## Warning in Surv(time, status): Invalid status value, converted to NA
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-18-jskm/jskm/fig7-1.png" alt="ggplot2"  />
<p class="caption">ggplot2</p>
</div>

---

其他的函数可以自己摸索
