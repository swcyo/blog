---
title: GSEA的二次作图
author: 欧阳松
date: '2022-09-29'
slug: replotGSEA
categories:
  - GSEA
tags:
  - GSEA
from_Rmd: yes
---

在[使用排序值的基因集进行GSEA之GSEA软件分析](/course/2022-09-28-gsea1/pre-ranked-gsea1/)这篇教程中，我最后留了个悬念，也就是用GSEA java软件作图后的结果全都是分辨率只有72的png图片，比如下面这张图Figure \@ref(fig:Fig1)所示，这是远远不能满足需求的，那么对于这些结果是否还可以再次画图呢？是否有参数可以自己画图呢？

![](/course/2022-09-29-replotgsea/enplot_KEGG_PPAR_SIGNALING_PATHWAY_HSA03320_47.png)

答案是肯定的，不仅有，而且还有很多方法，不知道你们有没有发现本地文件夹里其实还有一个**edb**文件夹，而这个文件夹只有三个文件，一个rnk，一个gmt，另外就是`results.edb`，实际上所有的图片信息都在`results.edb`里，在这里展示一些我收集的二次绘图办法。

## Rtoolbox二次绘图

[**Rtoolbox**](https://github.com/PeeperLab/Rtoolbox)这个包其实没有发布，只能在**Github**上安装，里面也只有两个函数`replotGSEA()`和`OverviewPlot()`，关于Github如何安装R包这里不做介绍了，为了方便更好的访问，我很早就已经导入到我的Gitee上了，安装起来也很简单。

```         
remotes::install_git('https://gitee.com/swcyo/Rtoolbox')
```

而关于这个包的`replotGSEA()`使用也很简单

```         
replotGSEA(path, gene.set, class.name, metric.range, enrichment.score.range)
```

比如我的本地结果都在\
`/Users/mac/gsea_home/output/sep28/my_analysis.GseaPreranked.1664378586466` 这个文件夹里，里面有很多结果，我们只需要提取相应的通路名称，设置一些简单的函数就可以出一个pdf的图了，比如我要提取PPAR通路的结果，只需要一个函数即可

```         
library(Rtoolbox) ##加载R包
replotGSEA(path = '/Users/mac/gsea_home/output/sep28/my_analysis.GseaPreranked.1664378586466', ##设置本地文件夹路径
           gene.set = "KEGG_PPAR_SIGNALING_PATHWAY%HSA03320", ## 提取PPAR通路
           class.name = "PPAR_SIGNALING_PATHWAY", ##定义图中间的名称
           ## enrichment.score.range= c(-1, 1) ###设置富集分数范围，一般默认即可
           )
```

这时候会再弹出一个R的窗口（Mac系统可能提示要安装Quartz），这时候会显示一个图，显示了一个比自带更好看的图，还能显示p、FDR和NES的值，我们适当的拉伸图片的长宽，然后点Save可以保存为pdf，之后再自己编辑结果，见Figure \@ref(fig:Fig2)所示。与Figure \@ref(fig:Fig1)比较简直就是天壤之别吧。

![](/course/2022-09-29-replotgsea/replotGSEA1.png)


> 然而，这个方案有两个缺陷
>
> 1.  不能在一张图片上设置多条通路
>
> 2.  不能使用代码自由保存图片格式和大小

## gseaplot_modified函数二次绘图

使用这个函数其实纯属于不讲武德的方法，完完全全就是调用[**Rtoolbox**](https://github.com/PeeperLab/Rtoolbox)这个包的`replotGSEA()`绘图，唯一的区别就是这个函数不需要安装**Rtoolbox**这个包，而是直接定义函数，要说区别吧，我仔细对比了一些源代码[R/ReplotGSEA.R](https://gitee.com/swcyo/Rtoolbox/blob/master/R/ReplotGSEA.R)，也就是在图片的设置上有非常非常非常细微的差距而已。。。

因为两个函数没有本质差异，所以我也就不放结果了，需要的还不如直接复制[R/ReplotGSEA.R](https://gitee.com/swcyo/Rtoolbox/blob/master/R/ReplotGSEA.R)这个链接里的函数，这没有什么好说的了。。。

## 基于ggplot2的绘图

这个教程来自于[GSEA自定义做图 - 简书 (jianshu.com)](https://www.jianshu.com/p/7c171bed7c6f)，当然最好看的肯定是使用**clusterProfiler**计算好的结果，然后使用**enrichplo**t包的`gseaplot2()`函数来绘图，当然我们也是可以借鉴Y叔的画图思路要成图，但这个要求太高。这个教程其实还是在**Rtoolbox**的基础上进行二次修改，将`replotGSEA()`函数的作图取消，改成单独提取rank和ES的值，然后使用ggplot2拼图。原理无非就是在结果文件夹中有个edb文件夹，里面又有一个.edb 和 .rank文件，这个文件就是做图的原始文件，如果你动手能力强，可以封装成一个函数，也可以自己开发一个包。

使用修改的函数直接提取数据作图。然而对于单个GSEA而已，GSEA的文件夹里还有png图和tsv的表格（很久以前是xls），网上当然也有一些教程，我们可以先看看tsv的结果，比如我们继续使用PPAR通路的表格，可以看到表格里有SYMBOL，RANK.IN.GENE.LIST，RANK.METRIC.SCORE，RUNNING.ES等信息。


```r
data <- read.delim("~/gsea_home/output/sep28/my_analysis.GseaPreranked.1664378586466/KEGG_PPAR_SIGNALING_PATHWAY%HSA03320.tsv")
## 看看数据分布
head(data)
```

```
##    NAME SYMBOL RANK.IN.GENE.LIST RANK.METRIC.SCORE RUNNING.ES CORE.ENRICHMENT
## 1 row_0   MMP1               265            0.4224    0.03072              No
## 2 row_1    ME1               368            0.3964    0.06376              No
## 3 row_2   OLR1               567            0.3653    0.09122              No
## 4 row_3   SCD5              1024            0.3145    0.10665              No
## 5 row_4    UBC              1589            0.2741    0.11530              No
## 6 row_5  FABP5              2359            0.2329    0.11430              No
##    X
## 1 NA
## 2 NA
## 3 NA
## 4 NA
## 5 NA
## 6 NA
```

二次绘图，无非三部分，第一部分是曲线，第二部分是网格线，第三部分是底下的曲线，可以使用的办法很多，重点是知道图的x轴和y轴是什么，推荐使用ggplot2画图，当然如果你想省事，用ggpubr更简单

我们先画最上面的图，可以使用geom_line画出，见Figure \@ref(fig:fig3)所示。


```r
library(ggplot2)
p1<-ggplot(data) +
  aes(x = RANK.IN.GENE.LIST, y = RUNNING.ES) + #x轴是rank值，y轴是ES值
  geom_line(size = 1, colour = "#f87669") +
  labs( y = "Enrichment score (ES)",  title = "PPAR SIGNALING PATHWAY",x=NULL) +
  theme_bw(base_size = 12)+ #设置主题和字体大小
  theme(axis.title.x=element_blank(),axis.text.x=element_blank(), axis.ticks.x=element_blank(),## 将x轴文字清空
        plot.title=element_text(hjust=0.5))+ #设置标题居中
  scale_x_continuous(expand = c(0, 0)) + #取消x轴左右的空白
  geom_hline(yintercept = 0, linetype = "dashed") #添加ES为0的基准线
```

```
## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
## ℹ Please use `linewidth` instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

```r
p1
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-09-29-replotgsea/replotgsea/fig3-1.png" alt="GSEA上部分的曲线图"  />
<p class="caption">GSEA上部分的曲线图</p>
</div>

接着我们画中间的部分，见Figure \@ref(fig:fig4)所示。


```r
p2 <- ggplot(data, aes(x = RANK.IN.GENE.LIST)) + geom_linerange(aes(ymin = -min(RANK.IN.GENE.LIST),
  ymax = max(RANK.IN.GENE.LIST))) + xlab(NULL) + ylab(NULL) + theme_bw() + theme(legend.position = "none",
  plot.margin = margin(t = -0.1, b = 0, unit = "cm"), axis.ticks = element_blank(),
  axis.text = element_blank(), axis.line.x = element_blank()) + scale_x_continuous(expand = c(0,
  0)) + scale_y_continuous(expand = c(0, 0))
p2
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-09-29-replotgsea/replotgsea/fig4-1.png" alt="GSEA中间部分的线图"  />
<p class="caption">GSEA中间部分的线图</p>
</div>

最后下面的rank部分，见Figure \@ref(fig:fig5)所示。


```r
p3 <- ggplot(data) + aes(x = RANK.IN.GENE.LIST, y = RANK.METRIC.SCORE) + geom_area(size = 1.5,
  fill = "gray30") + theme_bw(base_size = 12) + ylab("Ranked List Metric") + xlab("Rank in Ordered Dataset") +
  scale_x_continuous(expand = c(0, 0))
p3
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-09-29-replotgsea/replotgsea/fig5-1.png" alt="GSEA下面部分的图"  />
<p class="caption">GSEA下面部分的图</p>
</div>

最后，将三张图拼成一张图即可，见Figure \@ref(fig:fig6)所示。


```r
library(patchwork)

p1/p2/p3 + plot_layout(heights = c(0.5, 0.2, 0.3))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-09-29-replotgsea/replotgsea/fig6-1.png" alt="GSEA图"  />
<p class="caption">GSEA图</p>
</div>
