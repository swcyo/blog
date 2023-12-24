---
title: 使用pROC包画好看的ROC曲线
author: 欧阳松
date: '2021-11-16'
slug: pROC
categories:
  - ROC
  - 教程
tags:
  - ROC
  - 画图
  - 教程
from_Rmd: yes
---

**pROC**是一个专门用来计算和绘制ROC曲线的R包，目前已被CRAN收录，因此安装也非常简单，同时该包也兼容`ggplot2`函数绘图，因此本文试做一教程。

ROC曲线主要是用于X对Y的预测准确率情况，在医学领域主要用于判断某种因素对于某种疾病的诊断是否有诊断价值。而关于什么是ROC曲线和AUC，以及如何去看ROC曲线的结果，本文不做科普，简单的理解就是看AUC的值，AUC取值范围一般在0.5和1之间，使用AUC值作为评价标准是因为很多时候ROC曲线并不能清晰的说明哪个分类器的效果更好，而作为一个数值，对应AUC更大的分类器效果更好。。。

本文以[仙桃学术](https://www.xiantaozi.com)上的一个诊断性ROC示例数据,单机版测试数据地址[诊断ROC.xlsx](/course/2021-11-16-proc/诊断ROC.xlsx)。


```r
library(readxl)
ROC <- read_excel("/Users/mac/Downloads/仙桃学术示例数据及代码/诊断ROC.xlsx")
```


|outcome |      a|      b|      c|
|:-------|------:|------:|------:|
|group1  | 1.5859| 1.1743| 2.6748|
|group1  | 2.2053| 0.8619| 2.0031|
|group1  | 2.1996| 2.3159| 1.2816|
|group1  | 1.2411| 1.5746| 1.8664|
|group1  | 2.0170| 1.9533| 1.8472|
|group2  | 1.9882| 0.3276| 0.6753|
|group2  | 0.5604| 1.0118| 1.3762|
|group2  | 0.8033| 0.6715| 0.2956|
|group2  | 0.6511| 1.1817| 0.3139|
|group2  | 0.1287| 0.4656| 0.3263|

-   我们可以看到该数据的组成，结局为`group1`和`group2`，变量为a、b和c，也就是说评估a、b和c在预测group1和group2上的结局，哪个的准确性更高。

-   如果在肿瘤领域的话，我们可以这样假设，比如group1是正常组织，group2是肿瘤组织，而a、b、c是三种基因，或者是三种数据集。那么，常规情况下，我们除了比较a、b、c三个变量在两组的差异外，我们还可以检测在两组中的预测性能。

-   比如已知a数据在组1和组2中有显著性差异，那么他的诊断性能高不高呢，我们就可以使用ROC曲线来预测下诊断效能好不好。

------------------------------------------------------------------------

在进行ROC曲线绘制前，我们可以先看看三个变量数据在两组的差异，这里我们可以使用**ggpubr**包进行快速的绘制，结果见Figure \@ref(fig:box)所示，可见在a、b、c三个变量中，group1均显著高于group2。


```r
library(ggpubr)
p1 <- ggboxplot(ROC, "outcome", "a", color = "outcome", palette = "lancet", add = "jitter",
  legend = "none", ggtheme = theme_bw()) + stat_compare_means(comparisons = list(c("group1",
  "group2")), method = "t.test")
p2 <- ggboxplot(ROC, "outcome", "b", color = "outcome", palette = "lancet", add = "jitter",
  legend = "none", ggtheme = theme_bw()) + stat_compare_means(comparisons = list(c("group1",
  "group2")), method = "t.test")
p3 <- ggboxplot(ROC, "outcome", "c", color = "outcome", palette = "lancet", add = "jitter",
  legend = "none", ggtheme = theme_bw()) + stat_compare_means(comparisons = list(c("group1",
  "group2")), method = "t.test")
ggarrange(p1, p2, p3, nrow = 1, labels = "AUTO")
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-11-16-proc/proc/box-1.png" alt="三个变量在两组中的差异"  />
<p class="caption">三个变量在两组中的差异</p>
</div>

然而，为了进一步评估三个变量的诊断性能，这时候需要绘制ROC曲线，这里使用**pROC**包的roc函数进行计算，而在进行计算之前，我们需要知道几个注意点：

1.  确定分组的顺序

    进行ROC曲线的绘制，必须要明确分组方向，默认的是对照组\>参考组，就本例子而已即group1\>group2，因此需设置`levels=c('group1','group2'),direction=">"`

2.  AUC和CI的计算

    默认AUC为T，如需现在95%CI，则应设置`ci=TRUE`

3.  是否需要拟合平滑曲线

    默认不拟合平滑曲线，如果需要拟合平滑曲线，需设置`smooth=TRUE`，但需要注意的是，如果设置了平滑曲线，那么计算的结果与非平滑曲线**有一定的差异**。


```r
library(pROC)
## roc的计算，可以一次性批量计算a、b、c三组数据
res<-roc(outcome~a+b+c,data=ROC,aur=TRUE,
         ci=TRUE, # 显示95%CI
         # percent=TRUE, ##是否需要以百分比显示
         levels=c('group1','group2'),direction=">" #设置分组方向
         )
## 平滑曲线的ROC结果
smooth<-roc(outcome~a+b+c,data=ROC,aur=TRUE,
         ci=TRUE, # 显示95%CI
         # percent=TRUE, ##是否需要以百分比显示
         smooth=TRUE,
         levels=c('group1','group2'),direction=">" #设置分组方向
         )
### 显示三组在平滑和非平滑ROC曲线的结果
res
```

```
## $a
## 
## Call:
## roc.formula(formula = outcome ~ a, data = ROC, aur = TRUE, ci = TRUE,     levels = c("group1", "group2"), direction = ">")
## 
## Data: a in 40 controls (outcome group1) > 32 cases (outcome group2).
## Area under the curve: 0.733
## 95% CI: 0.617-0.849 (DeLong)
## 
## $b
## 
## Call:
## roc.formula(formula = outcome ~ b, data = ROC, aur = TRUE, ci = TRUE,     levels = c("group1", "group2"), direction = ">")
## 
## Data: b in 40 controls (outcome group1) > 32 cases (outcome group2).
## Area under the curve: 0.823
## 95% CI: 0.73-0.917 (DeLong)
## 
## $c
## 
## Call:
## roc.formula(formula = outcome ~ c, data = ROC, aur = TRUE, ci = TRUE,     levels = c("group1", "group2"), direction = ">")
## 
## Data: c in 40 controls (outcome group1) > 32 cases (outcome group2).
## Area under the curve: 0.924
## 95% CI: 0.868-0.981 (DeLong)
```

```r
smooth
```

```
## $a
## 
## Call:
## roc.formula(formula = outcome ~ a, data = ROC, aur = TRUE, ci = TRUE,     smooth = TRUE, levels = c("group1", "group2"), direction = ">")
## 
## Data: m.data[[predictor]] in 40 controls (response group1) > 32 cases (response group2).
## Smoothing: binormal 
## Area under the curve: 0.73
## 95% CI: 0.609-0.833 (2000 stratified bootstrap replicates)
## 
## $b
## 
## Call:
## roc.formula(formula = outcome ~ b, data = ROC, aur = TRUE, ci = TRUE,     smooth = TRUE, levels = c("group1", "group2"), direction = ">")
## 
## Data: m.data[[predictor]] in 40 controls (response group1) > 32 cases (response group2).
## Smoothing: binormal 
## Area under the curve: 0.817
## 95% CI: 0.705-0.893 (2000 stratified bootstrap replicates)
## 
## $c
## 
## Call:
## roc.formula(formula = outcome ~ c, data = ROC, aur = TRUE, ci = TRUE,     smooth = TRUE, levels = c("group1", "group2"), direction = ">")
## 
## Data: m.data[[predictor]] in 40 controls (response group1) > 32 cases (response group2).
## Smoothing: binormal 
## Area under the curve: 0.91
## 95% CI: 0.83-0.964 (2000 stratified bootstrap replicates)
```

紧接着就是对结果的可视化，默认可用`plot()`函数直接绘制可视化结果，并且细节非常之多。但是本文仅介绍自带`ggroc()`函数的可视化方法，为了图像的美观，本文以平滑曲线来绘制，可视化的教程很多，可以参照[R语言pROC包绘制ROC曲线](https://www.jianshu.com/p/8d3716bf2e9b)这个教程。

------------------------------------------------------------------------

1.  单独曲线的绘制,见Figure \@ref(fig:roc)所示

    
    ```r
    library(ggplot2)
    pa<- ggroc(smooth$a, 
           legacy.axes = TRUE # 将X轴改为0-1，（默认是1-0）
           )+
       geom_segment(aes(x = 0, xend = 1, y = 0, yend = 1), 
                    color="darkgrey", linetype=4)+
     theme_bw() +# 设置背景
     ggtitle('a-ROC')
    pb<- ggroc(smooth$b, legacy.axes = TRUE)+geom_segment(aes(x = 0, xend = 1, y = 0, yend = 1), color="darkgrey", linetype=4)+theme_bw() +ggtitle('b-ROC')
    pc<- ggroc(smooth$c, legacy.axes = TRUE)+geom_segment(aes(x = 0, xend = 1, y = 0, yend = 1), color="darkgrey", linetype=4)+theme_bw() +ggtitle('c-ROC')
    cowplot::plot_grid(pa,pb,pc,labels = "AUTO",nrow = 1)
    ```
    
    <div class="figure" style="text-align: center">
    <img src="/figures/course/2021-11-16-proc/proc/roc-1.png" alt="三个变量在两组中的ROC曲线"  />
    <p class="caption">三个变量在两组中的ROC曲线</p>
    </div>

    当然，我们也可以把AUC附上，使用**ggplot2**的`annotate()`函数拼接数值，见Figure \@ref(fig:roc2)所示。

    
    ```r
    pa <- pa + annotate("text", x = 0.75, y = 0.25, label = paste("AUC = ", round(res$a$auc,
      3)))
    pb <- pb + annotate("text", x = 0.75, y = 0.25, label = paste("AUC = ", round(res$b$auc,
      3)))
    pc <- pc + annotate("text", x = 0.75, y = 0.25, label = paste("AUC = ", round(res$c$auc,
      3)))
    cowplot::plot_grid(pa, pb, pc, labels = "AUTO", nrow = 1)
    ```
    
    <div class="figure" style="text-align: center">
    <img src="/figures/course/2021-11-16-proc/proc/roc2-1.png" alt="三个变量在两组中的ROC曲线"  />
    <p class="caption">三个变量在两组中的ROC曲线</p>
    </div>

2.  汇总曲线的绘制,美化后见Figure \@ref(fig:roc3)所示。

    
    ```r
    ggroc(smooth, legacy.axes = TRUE) + geom_segment(aes(x = 0, xend = 1, y = 0, yend = 1),
      color = "darkgrey", linetype = 4) + theme_bw() + ggtitle("ROC") + ggsci::scale_color_lancet() +
      annotate("text", x = 0.75, y = 0.125, label = paste("a-AUC = ", round(res$a$auc,
        3))) + annotate("text", x = 0.75, y = 0.25, label = paste("b-AUC = ", round(res$b$auc,
      3))) + annotate("text", x = 0.75, y = 0.375, label = paste("c-AUC = ", round(res$c$auc,
      3)))
    ```
    
    <div class="figure" style="text-align: center">
    <img src="/figures/course/2021-11-16-proc/proc/roc3-1.png" alt="三个变量汇总的ROC曲线"  />
    <p class="caption">三个变量汇总的ROC曲线</p>
    </div>

3.  分面曲线的绘制,美化后见Figure \@ref(fig:roc4)所示。

    
    ```r
    ggroc(smooth, legacy.axes = TRUE) + facet_grid(. ~ name) + theme(legend.position = "none") +
      theme_bw() + ggsci::scale_color_lancet() + geom_segment(aes(x = 0, xend = 1,
      y = 0, yend = 1), color = "darkgrey", linetype = 4)
    ```
    
    <div class="figure" style="text-align: center">
    <img src="/figures/course/2021-11-16-proc/proc/roc4-1.png" alt="三个变量分面的ROC曲线"  />
    <p class="caption">三个变量分面的ROC曲线</p>
    </div>

------------------------------------------------------------------------

简单的把boxplot和ROC曲线拼起来对比一下


```r
cowplot::plot_grid(p1, p2, p3, pa, pb, pc, labels = "AUTO")
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-11-16-proc/proc/cow-1.png" alt="boxplot和ROC"  />
<p class="caption">boxplot和ROC</p>
</div>
