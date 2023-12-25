---
title: "使用multipleROC快速绘制ROC曲线"
author: "欧阳松"
date: "2022-10-29"
slug: multipleROC
categories:
- ROC
- multipleROC
- 教程
tags:
- ROC
- multipleROC
from_Rmd: yes
---
之前写过一篇[使用pROC包画好看的ROC曲线](/course/2021-11-16-proc/proc/)的教程，那篇教程中使用的是**pROC**，这个包可以快速拟合ROC曲线，然后这个包需要提取进行运算结果，并且不能直接显示AUC值，今天推荐一个另一个绘制ROC的包**multipleROC**，顾名思义，这个包是可以一次性绘制多条ROC曲线的，并且也是基于ggplot2。

目前这个包作者没有上传CRAN或BiocManager，只能通过Github安装，地址为<https://github.com/cardiomoon/multipleROC>

## 安装multipleROC

    remotes::install_github("cardiomoon/multipleROC")

如果无法访问GitHub，也可以导入到Gitee后进行安装

    remotes::install_git("https://gitee.com/swcyo/multipleROC/")

## 数据演示

我们继续使用上次的数据进行演示。

以[仙桃学术](https://www.xiantaozi.com/)上的一个诊断性ROC示例数据为例进行演示（下载请点击[xlxs链接](https://xt-biotools.oss-cn-hangzhou.aliyuncs.com/biotools-v3/data/public/demo/%E8%AF%8A%E6%96%ADROC.xlsx)）。

```
library(readxl)
ROC <- read_excel("~/诊断ROC.xlsx")
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

## 探索性分析

我们可以事先看一下group1和group2两组在a变量中的差别，使用webr函数，先看看结果如何


```r
library(webr)
library(ggplot2)
library(dplyr)
library(tidyr)
ROC %>%
  group_by(outcome) %>%
  numSummaryTable(a)
```

<div class="tabwid"><style>.cl-1c7f784e{}.cl-1c7329d6{font-family:'Helvetica';font-size:13pt;font-weight:bold;font-style:normal;text-decoration:none;color:rgba(255, 255, 255, 1.00);background-color:transparent;}.cl-1c7329ea{font-family:'Helvetica';font-size:12pt;font-weight:normal;font-style:normal;text-decoration:none;color:rgba(0, 0, 0, 1.00);background-color:transparent;}.cl-1c788ef8{margin:0;text-align:center;border-bottom: 0 solid rgba(0, 0, 0, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);padding-bottom:2pt;padding-top:2pt;padding-left:5pt;padding-right:5pt;line-height: 1;background-color:transparent;}.cl-1c788f02{margin:0;text-align:right;border-bottom: 0 solid rgba(0, 0, 0, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 0 solid rgba(0, 0, 0, 1.00);border-right: 0 solid rgba(0, 0, 0, 1.00);padding-bottom:2pt;padding-top:2pt;padding-left:5pt;padding-right:5pt;line-height: 1;background-color:transparent;}.cl-1c78bc70{width:0.75in;background-color:rgba(91, 119, 120, 1.00);vertical-align: middle;border-bottom: 1pt solid rgba(237, 189, 62, 1.00);border-top: 1pt solid rgba(0, 0, 0, 1.00);border-left: 1pt solid rgba(0, 0, 0, 1.00);border-right: 1pt solid rgba(0, 0, 0, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-1c78bc84{width:0.75in;background-color:transparent;vertical-align: middle;border-bottom: 1pt solid rgba(237, 189, 62, 1.00);border-top: 0 solid rgba(0, 0, 0, 1.00);border-left: 1pt solid rgba(237, 189, 62, 1.00);border-right: 1pt solid rgba(237, 189, 62, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.cl-1c78bc85{width:0.75in;background-color:rgba(239, 239, 239, 1.00);vertical-align: middle;border-bottom: 1pt solid rgba(237, 189, 62, 1.00);border-top: 1pt solid rgba(237, 189, 62, 1.00);border-left: 1pt solid rgba(237, 189, 62, 1.00);border-right: 1pt solid rgba(237, 189, 62, 1.00);margin-bottom:0;margin-top:0;margin-left:0;margin-right:0;}.tabwid {
  font-size: initial;
  padding-bottom: 1em;
}

.tabwid table{
  border-spacing:0px !important;
  border-collapse:collapse;
  line-height:1;
  margin-left:auto;
  margin-right:auto;
  border-width: 0;
  border-color: transparent;
  caption-side: top;
}
.tabwid-caption-bottom table{
  caption-side: bottom;
}
.tabwid_left table{
  margin-left:0;
}
.tabwid_right table{
  margin-right:0;
}
.tabwid td, .tabwid th {
    padding: 0;
}
.tabwid a {
  text-decoration: none;
}
.tabwid thead {
    background-color: transparent;
}
.tabwid tfoot {
    background-color: transparent;
}
.tabwid table tr {
background-color: transparent;
}
.katex-display {
    margin: 0 0 !important;
}</style><table data-quarto-disable-processing='true' class='cl-1c7f784e'><thead><tr style="overflow-wrap:break-word;"><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">outcome</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">n</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">mean</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">sd</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">median</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">trimmed</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">mad</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">min</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">max</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">range</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">skew</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">kurtosis</span></p></th><th class="cl-1c78bc70"><p class="cl-1c788ef8"><span class="cl-1c7329d6">se</span></p></th></tr></thead><tbody><tr style="overflow-wrap:break-word;"><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">group1</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">40.00</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">1.51</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">0.55</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">1.45</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">1.51</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">0.63</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">0.59</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">2.44</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">1.86</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">0.06</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">-1.12</span></p></td><td class="cl-1c78bc84"><p class="cl-1c788f02"><span class="cl-1c7329ea">0.09</span></p></td></tr><tr style="overflow-wrap:break-word;"><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">group2</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">32.00</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">1.00</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">0.55</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">1.00</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">0.99</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">0.63</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">0.13</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">1.99</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">1.86</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">0.08</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">-1.25</span></p></td><td class="cl-1c78bc85"><p class="cl-1c788f02"><span class="cl-1c7329ea">0.10</span></p></td></tr></tbody></table></div>

也可以使用箱示图和密度图进行展示，见fig1所示。


```r
p1 <- ggplot(data = ROC) + geom_density(aes(x = a, fill = outcome), alpha = 0.5)
p2 <- ggplot(data = ROC) + geom_boxplot(aes(x = outcome, y = a, fill = outcome),
  alpha = 0.5)
cowplot::plot_grid(p1, p2)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-10-29-multipleroc/multipleROC/fig1-1.png" alt="group1和group2两组在a变量中的差别"  />
<p class="caption">group1和group2两组在a变量中的差别</p>
</div>

同法可以显示b和c变量的结果，我们暂时以boxplot展示


```r
p3 <- ggplot(data = ROC) + geom_boxplot(aes(x = outcome, y = b, fill = outcome),
  alpha = 0.5)
p4 <- ggplot(data = ROC) + geom_boxplot(aes(x = outcome, y = c, fill = outcome),
  alpha = 0.5)
cowplot::plot_grid(p2, p3, p4, labels = "AUTO", nrow = 1)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-10-29-multipleroc/multipleROC/fig2-1.png" alt="group1和group2两组在三变量中的差别"  />
<p class="caption">group1和group2两组在三变量中的差别</p>
</div>

虽然探索性分析可以判断两组的差异，但是无法确定最佳截断值，也无妨评估预测效能。

## ROC曲线的绘制

绘制ROC曲线是确定最佳截断值的有用方法之一。您可以使用以下R命令执行ROC分析。下面的R命令使一个类为multipleROC的对象，并进行绘图。

由于默认的函数中分组需为0和1，因此需要将group1和group2进行赋值，我们将group1定义为0，group2定义为1，我们绘制a变量在两组中的ROC图片，我们可以使用`multipleROC()`语句一步计算，可以看到最佳截断值，AUC值，另外敏感度、特异度都是可以直接显示的，见fig3所示。。


```r
ROC$group <- ifelse(ROC$outcome == "group1", 0, 1)  # 将group1定义为0，否则为1
library(multipleROC)
a = multipleROC(group ~ a, data = ROC)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-10-29-multipleroc/multipleROC/fig3-1.png" alt="a变量在两组的ROC曲线"  />
<p class="caption">a变量在两组的ROC曲线</p>
</div>

如果不想显示那么多结果的话，也可以`plot_ROC()`函数一个个设置是否显示

    plot_ROC(
      x,
      show.points = TRUE,
      show.eta = TRUE,
      show.sens = TRUE,
      show.AUC = TRUE,
      facet = FALSE
    )

## AUC和p值

在fig3的右下角，您可以看到曲线下面积（AUC）和Wilcoxon秩和检验的p值。p值来自以下计算结果。


```r
wilcox.test(ROC$a, ROC$group)
```

```
## 
## 	Wilcoxon rank sum test with continuity correction
## 
## data:  ROC$a and ROC$group
## W = 4416, p-value = 1e-13
## alternative hypothesis: true location shift is not equal to 0
```

AUC值则通过**multipleROC**包的`simpleAUC()`函数进行运算，函数如下:

    simpleAUC <- function(df){
         df=df[order(df$x,decreasing=TRUE),]
         TPR=df$sens
         FPR=df$fpr

         dFPR <- c(diff(FPR), 0)
         dTPR <- c(diff(TPR), 0)

         sum(TPR * dFPR) + sum(dTPR * dFPR)/2
    }

那么，我们直接直接只有simpleAUC(a\$df) 进行提取，或者简单的的a\$auc直接看到完整的AUC值


```r
simpleAUC(a$df)  ## 函数法
```

```
## [1] 0.7328
```

```r
a$auc  # 直接提取法
```

```
## [1] 0.7328
```

同样的，我们直接提取截断点(cutpoint)和最佳截断值（Optimal Cutoff value）


```r
a$cutpoint
```

```
## [1] 0.5137
```

```r
a$cutoff
```

```
##        a
## 54 1.083
```

## 将结果转换为**pROC对象**

如果你更习惯**pROC**的结果，使用`multipleROC2roc()`函数，可以直接将结果转换为 **pROC**的roc 对象


```r
a2 <- multipleROC2roc(a)
```

```
## Setting levels: control = 0, case = 1
```

```
## Setting direction: controls < cases
```

```r
class(a)  ##a的类型为multipleROC
```

```
## [1] "multipleROC"
```

```r
class(a2)  ##a2已经转换为roc的类型了
```

```
## [1] "roc"
```

```r
pROC::auc(a2)  ## 我们用pROC看auc的结果
```

```
## Area under the curve: 0.733
```

我们可以使用pROC的绘图函数对a2进行绘图了，我们比较以下两种结果吧，见fig4所示。


```r
library(pROC)
```

```
## Type 'citation("pROC")' for a citation.
```

```
## 
## Attaching package: 'pROC'
```

```
## The following objects are masked from 'package:stats':
## 
##     cov, smooth, var
```

```r
p5 <- ggroc(a2, legacy.axes = TRUE) + geom_segment(aes(x = 0, xend = 1, y = 0, yend = 1),
  color = "darkgrey", linetype = 4) + theme_bw() + ggtitle("pROC")
p6 <- plot(a) + ggtitle("multipleROC")
cowplot::plot_grid(p5, p6)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-10-29-multipleroc/multipleROC/fig4-1.png" alt="pROC和multipleROC的结果对比"  />
<p class="caption">pROC和multipleROC的结果对比</p>
</div>

## 多个ROC曲线的绘制

可以用多个函数进行多个ROC的曲线，可以使用`plot_ROC(list())`一个个绘制曲线，见fig5所示。


```r
a=multipleROC(group~a,data=ROC,plot=FALSE)
b=multipleROC(group~b,data=ROC,plot=FALSE)
c=multipleROC(group~c,data=ROC,plot=FALSE)
plot_ROC(list(a,b,c),
         show.eta=FALSE,#不显示截点
         show.sens=FALSE #不显示各种率
         )
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-10-29-multipleroc/multipleROC/fig5-1.png" alt="三条曲线同时绘制"  />
<p class="caption">三条曲线同时绘制</p>
</div>

当然，如果你不想写那么多代码的话，也可以直接使用`plot_ROC2()`函数直接绘制，是不是简单的多。


```r
plot_ROC2(yvar = "group", xvars = c("a", "b", "c"), dataname = "ROC")
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-10-29-multipleroc/multipleROC/fig6-1.png" alt="三条曲线同时绘制简单函数"  />
<p class="caption">三条曲线同时绘制简单函数</p>
</div>

### 分面显示

将三张图放在一起，可以看到数值重叠，影响了颜值，因此我们可以用facet进行分面绘制，见fig7所示。。


```r
plot_ROC(list(a, b, c), facet = TRUE)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-10-29-multipleroc/multipleROC/fig7-1.png" alt="三条曲线的分面显示"  />
<p class="caption">三条曲线的分面显示</p>
</div>

可以发现分面的标签默认是1，2，3，我们可以使用Y叔团队开发的**ggfun**这个包的`facet_set()`函数进行快速的修改，见fig8所示。


```r
library(ggfun)
plot_ROC(list(a, b, c), facet = TRUE) + facet_set(label = c(`1` = "a", `2` = "b",
  `3` = "c"))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-10-29-multipleroc/multipleROC/fig8-1.png" alt="三条曲线的分面显示，改标签"  />
<p class="caption">三条曲线的分面显示，改标签</p>
</div>

### 换一种分面显示

使用**ggplot2**包的`facet_grid`可以换一个分面显示方式，见fig9所示。


```r
plot_ROC(list(a, b, c)) + facet_grid(no ~ .) + facet_set(label = c(`1` = "a", `2` = "b",
  `3` = "c"))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-10-29-multipleroc/multipleROC/fig9-1.png" alt="三条曲线的分面显示，改标签"  />
<p class="caption">三条曲线的分面显示，改标签</p>
</div>

由于是基于ggplot2语句，所以我们可以使用`ggtitle`添加标题，还可以更换主题等等，有兴趣的可以自去摸索以下。。。
