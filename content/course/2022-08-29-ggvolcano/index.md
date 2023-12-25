---
title: ggVolcano：一行代码绘制火山图
author: 欧阳松
date: '2022-08-29'
slug: ggVolcano
categories:
  - 博客
  - 火山图
  - ggplot2
  - ggVolcano
tags:
  - 火山图
description: ~
hideToc: no
enableToc: no
enableTocContent: no
tocFolding: no
tocPosition: inner
tocLevels:
  - h2
  - h3
  - h4
series: ~
image: ~
from_Rmd: yes
---

之前我介绍了用[ggplot2](/course/ggplot2-volcano/)和[ggpubr](/course/ggpubr-volcano/)绘制火山图的教程，其实生信发展到现在，当然有更多更快捷的方法了，包括我之前说的[AnnoProbe](/course/annoprobe-%E4%B8%80%E4%B8%AA%E5%8F%AF%E4%BB%A5%E5%BF%AB%E9%80%9F%E4%B8%8B%E8%BD%BD%E5%B9%B6%E5%A4%84%E7%90%86geo%E6%95%B0%E6%8D%AE%E7%9A%84%E5%A5%BD%E5%8C%85/)和[tinyarray](/course/tinyarray-geo/)都是可以一步绘制火山图的。当然还有很多shiny网站也可以绘制火山图，比如[ggVolcanoR (monash.edu)](https://ggvolcanor.erc.monash.edu/)等，还有国内众多的在线工具，比如[sangerbox](http://vip.sangerbox.com/home.html)、[hiplot](https://hiplot-academic.com/basic/volcano)、[联川生物云](https://www.omicstudio.cn/tool)、[微生信](http://www.bioinformatics.com.cn/plot_enhanced_volcano_plot_138)等也是很好的。

今天介绍一个一行代码绘制火山图的包：[**ggVolcano**](https://github.com/BioSenior/ggVolcano)，目前这个还只在GitHub上托管，不能登陆Github的可以看我之前的教程。

## 安装和加载包


```r
# install.packages('devtools') devtools::install_github('BioSenior/ggVolcano')
# ## 需要安装的把前面的#去掉
library(ggVolcano)
```

这个安装过程中可能会附加很多别的包，甚至需要更新，一般其实建议不更新，比方我第一次安装后就提示我的**tidyverse**和r**lang这**两个包就有问题，不过卸载重装以后又可以了。。。

## 如何绘图

这里，我们可以直接按照Github上的教程去跑一遍（访问不了的可以看[再肝一个R包！一行代码绘制精美火山图！](https://www.jianshu.com/p/aad46b3e1b35)这个简书教程），当然也可以用自己的数据去跑。

### 需要的数据

画火山图至少需要三组数据：

-   基因名

-   差异倍数

-   p值或校正p值

当然如果需要标记那些基因是显著上调、显著下调或者不显著的画，这个包可以直接使用`add_regulate()`进行添加，无需另外定义一行，见Table \@ref(tab:table)所示。


```r
### 加载演示数据
data(deg_data)
### 新增标记行，定义差异倍数为2倍（log2FC=1），p<0.05
data <- add_regulate(deg_data, log2FC_name = "log2FoldChange", fdr_name = "padj",
  log2FC = 1, fdr = 0.05)
```


Table: 新建data数据前十行

|          |    row    | baseMean | log2FoldChange | lfcSE  |  stat   | pvalue | padj | regulate |
|:---------|:---------:|:--------:|:--------------:|:------:|:-------:|:------:|:----:|:--------:|
|GCR1      |   GCR1    |  7201.6  |     2.244      | 0.2005 | 11.193  |   0    |  0   |    Up    |
|OPI10     |   OPI10   |  1009.4  |     -2.257     | 0.2096 | -10.768 |   0    |  0   |   Down   |
|AGA2      |   AGA2    |  249.1   |     3.829      | 0.3623 | 10.569  |   0    |  0   |    Up    |
|FIM1_1376 | FIM1_1376 |  5237.5  |     2.550      | 0.2560 |  9.961  |   0    |  0   |    Up    |
|HMG1      |   HMG1    | 10838.1  |     2.214      | 0.2229 |  9.934  |   0    |  0   |    Up    |
|FIM1_3918 | FIM1_3918 |  2456.8  |     2.288      | 0.2356 |  9.711  |   0    |  0   |    Up    |
|HSP26     |   HSP26   | 270620.2 |     -2.411     | 0.2492 | -9.677  |   0    |  0   |   Down   |
|FIM1_3823 | FIM1_3823 |  373.3   |     2.587      | 0.2816 |  9.184  |   0    |  0   |    Up    |
|ARO10     |   ARO10   | 19263.2  |     -2.293     | 0.2641 | -8.682  |   0    |  0   |   Down   |
|cyp524A1  | cyp524A1  | 28952.6  |     1.797      | 0.2081 |  8.637  |   0    |  0   |    Up    |

### 使用ggvolcano()函数绘制通用火山图

### 基本用法

确保有一个包含差异表达基因信息的DEG结果数据（包括GeneName、Log2FC、pValue、FDR）,如果数据没有名为"regulate"的列，则可以使用`add_regulate()`函数进行添加。使用函数`ggvolcano()`绘制通用火山图。

可以看基础教程参数如下：

    ggvolcano(
      data,
      x = "log2FoldChange",
      y = "padj",
      pointSize = 1,
      pointShape = 21,
      fills = c("#00AFBB", "#999999", "#FC4E07"),
      colors = c("#00AFBB", "#999999", "#FC4E07"),
      x_lab = NULL,
      y_lab = NULL,
      legend_title = NULL,
      legend_position = "UL",
      log2FC_cut = 1,
      FDR_cut = 0.05,
      add_line = TRUE,
      add_label = TRUE,
      label = "row",
      label_number = 10,
      custom_label = NULL,
      output = TRUE,
      filename = "volcano_plot"
    )

先使用最基本的`ggvolcano()`函数绘制火山图，见Figure \@ref(fig:fig1)所示，可以看到自动设置了XY轴的名称，标记了差异倍数和p值虚线，还标记了显著的基因名。


```r
ggvolcano(data, x = "log2FoldChange", 
          y = "padj",
          label = "row", ## 标记基因所在的行名
          legend_position = 'UR', ## 设置标签位于右上
          label_number = 20, ## 显示前20的基因
          output = FALSE ##不导出图，默认设置是TURE，这样在根目录会生成一个pdf的图
          )
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-29-ggvolcano/index/fig1-1.png" alt="基础火山图"  />
<p class="caption">基础火山图</p>
</div>

### **更改填充色和配色**

我们可以手动修改相关参数来更改配色，也可以调用ggsci的配色，比较图见Figure \@ref(fig:fig2)所示。


```r
p1 <- ggvolcano(data, x = "log2FoldChange", y = "padj",
          fills = c("brown","gray","steelblue"), ## 手动设置配色，如棕色、灰色和金属蓝
          colors = c("brown","gray","steelblue"),## 手动设置配色，如棕色、灰色和金属蓝
          label = "row", legend_position = 'UR',label_number = 20, output = FALSE)

p2 <- ggvolcano(data, x = "log2FoldChange", y = "padj",
          label = "row", legend_position = 'UR',label_number = 20, output = FALSE)+
  ggsci::scale_color_lancet()+ ## 调用ggsci的lancet配色
  ggsci::scale_fill_lancet() ## 调用ggsci的lancet配色


### 拼图进行比较
library(patchwork)
p1|p2
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-29-ggvolcano/index/fig2-1.png" alt="手动修改火山图配色"  />
<p class="caption">手动修改火山图配色</p>
</div>

## **‎使用gradual_volcano()函数制作渐变彩色火山图‎**

#### 基本用法

确保有一个包含差异表达基因信息的DEG结果数据（包括GeneName、Log2FC、pValue、FDR）,如果数据没有名为"regulate"的列，则可以使用`add_regulate()`函数进行添加。使用`ggvolcano()`函数绘制通用火山图，见Figure \@ref(fig:fig3)所示。


```r
gradual_volcano(deg_data, x = "log2FoldChange", y = "padj", label = "row", label_number = 20,
  legend_position = "UR", output = FALSE)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-29-ggvolcano/index/fig3-1.png" alt="渐变色火山图"  />
<p class="caption">渐变色火山图</p>
</div>

### **更改填充色和配色**

我们可以手动修改相关参数来更改配色，也可以调用ggsci的配色，比较图见Figure \@ref(fig:fig4)所示。


```r
library(RColorBrewer)
p3 <- gradual_volcano(data, x = "log2FoldChange", y = "padj", fills = brewer.pal(5,
  "RdYlBu"), colors = brewer.pal(8, "RdYlBu"), label = "row", label_number = 20,
  legend_position = "UR", output = FALSE)

p4 <- gradual_volcano(data, x = "log2FoldChange", y = "padj", label = "row", label_number = 20,
  legend_position = "UR", output = FALSE) + ggsci::scale_color_gsea() + ggsci::scale_fill_gsea()


### 拼图进行比较
library(patchwork)
p3 | p4
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-29-ggvolcano/index/fig4-1.png" alt="手动修改渐变色火山图配色"  />
<p class="caption">手动修改渐变色火山图配色</p>
</div>

> 如果要调整点的大小范围，可以使用pointSizeRange=c（min_size，max_size）

## **使用term_volcano()函数制作GO term火山图‎**

### 基本用法

确保有一个包含差异表达基因信息的DEG结果数据（包括GeneName、Log2FC、pValue、FDR）。除了DEG结果数据外，您还需要一个**术语数据**，它是一个两列数据框架，包含一些基因的GO术语信息。

如果数据没有名为"regulate"的列，则可以使用add_regulate函数进行添加。

使用函数term_Volcano()绘制GO term火山图。见Figure \@ref(fig:fig5)所示


```r
term_volcano(deg_data, term_data, x = "log2FoldChange", y = "padj", label = "row",
  label_number = 15, output = FALSE)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-29-ggvolcano/index/fig5-1.png" alt="GO term火山图"  />
<p class="caption">GO term火山图</p>
</div>

### **更改填充色和配色**

我们可以手动修改相关参数来更改配色，见Figure \@ref(fig:fig6)所示。


```r
library(RColorBrewer)

# Change the fill and color manually:
deg_point_fill <- brewer.pal(5, "RdYlBu")
names(deg_point_fill) <- unique(term_data$term)

term_volcano(data, term_data, x = "log2FoldChange", y = "padj", normal_point_color = "#75aadb",
  deg_point_fill = deg_point_fill, deg_point_color = "grey", legend_background_fill = "#deeffc",
  label = "row", label_number = 10, output = FALSE) + theme_bw()
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-29-ggvolcano/index/fig6-1.png" alt="手动修改Go term火山图配色"  />
<p class="caption">手动修改Go term火山图配色</p>
</div>
