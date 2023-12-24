---
title: 把多个生存分析进行合并
author: 欧阳松
date: '2021-09-12'
slug: multisurv
categories:
  - 博客
  - R
tags:
  - 生存分析
from_Rmd: yes
---

**survminer**包的`ggsurvplot()`函数可以画好看的生存分析曲线，也可以用分面的方法分面绘制多组图片（教程可以见我以前给生信助手上的一个小建议[「凌云34」Multiple
Overall Survival
Plots(2)](https://mp.weixin.qq.com/s/3GPQF75-SaUzMSfa1dFX9Q)，但是它的图却不是标准的ggplot2格式，所以对于多个生存曲线图，你并不能使用~~cowplot~~，~~ggarrange~~和~~patchwork~~进行拼接，甚至于Y叔开发的~~**ggplotify**~~ 也都不能转换为ggplot图形（大家如有好的方案，也可以下方评论），后面在生信技能术的[Github](https://github.com/jmzeng1314/GEO/tree/master/GSE11121_survival)上发现解决方案。

测试数据在这里，[phe.csv](/course/multisurv/phe.csv)，我们导入数字，看看前几行的数据，如表\@ref(tab:phe)所示。

    phe<-read.csv('~/phe.csv/)
    phe[1:10,]


Table: phe示例数据前10行

| event | grade | size | time |
|:-----:|:-----:|:----:|:----:|
|   1   |   2   | 1.8  |  92  |
|   0   |   3   | 2.5  |  71  |
|   1   |   3   | 1.5  |  58  |
|   0   |   2   | 1.2  |  68  |
|   0   |   2   | 2.4  | 103  |
|   0   |   2   | 1.8  |  93  |
|   0   |   2   | 1.4  | 111  |
|   0   |   3   | 1.3  |  85  |
|   0   |   2   | 0.8  |  98  |
|   0   |   1   | 1.6  |  81  |

这里可以看到size是一些数字，我们想一个办法，按照中位数，把它分为big和small两组（同理一些基因表达量也可以这样分组）,我们还可以转换成因子形式，这样后面的顺序不会因为字母排序而编号


```r
phe$size <- ifelse(phe$size > median(phe$size), "big", "small")
phe$size <- factor(phe$size, levels = c("small", "big"))
```

现在size就转换成字符的因子形式了，见表\@ref(tab:phe2)所示。


Table: phe示例数据前10行，size改变了形式

| event | grade | size  | time |
|:-----:|:-----:|:-----:|:----:|
|   1   |   2   | small |  92  |
|   0   |   3   |  big  |  71  |
|   1   |   3   | small |  58  |
|   0   |   2   | small |  68  |
|   0   |   2   |  big  | 103  |
|   0   |   2   | small |  93  |
|   0   |   2   | small | 111  |
|   0   |   3   | small |  85  |
|   0   |   2   | small |  98  |
|   0   |   1   | small |  81  |

接下来，我们可以参照之前的教程，画一个不同size在生存状态上的生存曲线，如图\@ref(fig:km)所示

    library(survival)
    library(survminer)
    sfit <- survfit(Surv(time, event)~size, data=phe)
    p1<-ggsurvplot(sfit,data = phe,
               palette = 'jco', 
               conf.int = T,conf.int.style='step', 
               pval = T,pval.method = T,
               risk.table = T,risk.table.pos='in',
               legend=c(0.85,0.85),
               legend.title="Size",
               legend.labs=c("small","big"),
               title="Survival curve for size", 
               ggtheme = theme_bw(base_size = 12))
    p1

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-12-multisurv/index/km-1.png" alt="不同Size分组的生存曲线"  />
<p class="caption">不同Size分组的生存曲线</p>
</div>

同样的我们可以画grade分组的生存曲线，见图\@ref(fig:km2)所示


```r
gfit = survfit(Surv(time, event) ~ grade, data = phe)
p2 <- ggsurvplot(gfit, data = phe, palette = "lancet", conf.int = T, conf.int.style = "step",
  pval = T, pval.method = T, risk.table = T, risk.table.pos = "in", legend = c(0.85,
    0.85), legend.title = "Grade", title = "Survival curve for grade", ggtheme = theme_bw(base_size = 12))
p2
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-12-multisurv/index/km2-1.png" alt="不同Grade分组的生存曲线"  />
<p class="caption">不同Grade分组的生存曲线</p>
</div>

然而，我们并不能拼接图形，使用下面的经典拼图代码全都会报错，连转换为ggplot都做不到

    cowplot::plot_grid(p1,p2) # 这个函数不行
    patchwork::p1+p1 # 这个函数也不行
    p1<-ggplotify::as.ggplot(p1) # 转成ggplot也不行

那么，除了导出图片，用AI等工具意外，是不是就没有在R里直接的解决方案了呢？

不然，我们可以用**survminer的**`arrange_ggsurvplots`函数进行解决，如下面代码，见图\@ref(fig:km3)所示。


```r
sfit1 = survfit(Surv(time, event) ~ size, data = phe)
sfit2 = survfit(Surv(time, event) ~ grade, data = phe)
splots <- list()
splots[[1]] <- p1
splots[[2]] <- p2

# 将多个图合并一起

arrange_ggsurvplots(splots, print = TRUE, ncol = 2, nrow = 1)  #定义行数和列数
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-12-multisurv/index/km3-1.png" alt="多个生存曲线的合并"  />
<p class="caption">多个生存曲线的合并</p>
</div>
