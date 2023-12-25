---
title: 利用tinyarry批量画生存曲线第二波
author: 欧阳松
date: '2022-08-17'
slug: [tinyarray-exp_surv]
categories:
  - tinyarray
tags:
  - 生存分析
from_Rmd: yes
---

之前写了篇[利用tinyarray批量画生存曲线，支持最佳截点,也可以按均值分组](/course/2022-02-25-利用tinyarray批量画生存曲线-支持最佳截点/tinyarray-km-cutoff/)的博文，当时`tinyarry`刚提交到**GitHub**上，还没有提交到CRAN，最初默认的函数只支持最佳截点的生存分析，并不支持均值分组，而且默认的函数出图也是较丑的红白配色，当时我的解决方案是修改底层函数，具体在我的博文中有教程，这样修改的办法很低效，于是我早在2021年10月27日就在Github上提了[issue](https://hub.fastgit.xyz/xjsun1221/tinyarray/issues/6)，经过半年多的等待，终于在2022年5月13日这天小洁老师终于给了我回复，并更新到了**2.2.8**版。

![](/course/2022-08-17-利用tinyarry批量画生存曲线第二波/Githubreply.png)

> **Re: [xjsun1221/tinyarray] exp_surv()画生存曲线能否设置不支持最佳截点 (Issue #6)**
>
> ------------------------------------------------------------------------
>
> 更新好啦。FALSE就是按照中位数分组了。

我们继续使用[利用tinyarray批量画生存曲线，支持最佳截点,也可以按均值分组](/course/2022-02-25-利用tinyarray批量画生存曲线-支持最佳截点/tinyarray-km-cutoff/)的博文里的数据[data.tsv](/course/multi-km-facet/data.tsv)这个数据，先导入数据，改一下列名，记得生存状态和生存时间的表头改成event和time

```         
data<-read.csv("/data.tsv",sep="\t",row.names = 1,header = T)
### 修改行名
colnames(data)[c(2:3,6:14)]<-c('event','time',
                               'RFC1','RFC2','RCF3','RFC4','RFC5',
                               'BEST1','BEST2','BEST3','BEST4')
### 去掉NA值，否则计算均值后会是NA值
data<-na.omit(data)
### 显示前10行和前10列
data[1:10,1:10]
```


Table: data

|                |samples         | event| time|cancer.type.abbreviation |sample_type   |  RFC1|  RFC2|  RCF3|  RFC4|  RFC5|
|:---------------|:---------------|-----:|----:|:------------------------|:-------------|-----:|-----:|-----:|-----:|-----:|
|TCGA-2F-A9KR-01 |TCGA-2F-A9KR-01 |     1| 3183|BLCA                     |Primary Tumor | 4.750| 4.990| 3.268| 4.973| 3.803|
|TCGA-XF-A8HF-01 |TCGA-XF-A8HF-01 |     1| 2954|BLCA                     |Primary Tumor | 2.554| 4.852| 3.018| 4.241| 3.353|
|TCGA-XF-AAME-01 |TCGA-XF-AAME-01 |     1| 2828|BLCA                     |Primary Tumor | 4.083| 4.691| 2.895| 4.838| 3.368|
|TCGA-UY-A78N-01 |TCGA-UY-A78N-01 |     1| 2641|BLCA                     |Primary Tumor | 4.889| 6.278| 1.696| 5.480| 4.733|
|TCGA-XF-A9SL-01 |TCGA-XF-A9SL-01 |     1| 2020|BLCA                     |Primary Tumor | 3.964| 5.241| 2.452| 4.418| 3.025|
|TCGA-XF-A9SH-01 |TCGA-XF-A9SH-01 |     1| 1971|BLCA                     |Primary Tumor | 3.483| 3.470| 0.679| 2.710| 4.248|
|TCGA-XF-AAN2-01 |TCGA-XF-AAN2-01 |     1| 1869|BLCA                     |Primary Tumor | 4.843| 5.537| 4.593| 4.959| 4.342|
|TCGA-G2-A2EO-01 |TCGA-G2-A2EO-01 |     1| 1804|BLCA                     |Primary Tumor | 3.725| 5.303| 2.768| 5.265| 4.104|
|TCGA-XF-AAN0-01 |TCGA-XF-AAN0-01 |     1| 1718|BLCA                     |Primary Tumor | 4.174| 5.440| 3.139| 4.936| 3.277|
|TCGA-XF-AAMJ-01 |TCGA-XF-AAMJ-01 |     1| 1670|BLCA                     |Primary Tumor | 3.847| 4.388| 2.108| 3.972| 4.648|

有了数据，我们就可以画批量的生存分析曲线图了，在**tinyarray2.28**版中，批量生存曲线的函数是`exp_surv()`，目前函数已经重新设置，指导参数如下：

> **Usage**
>
> exp_surv(exprSet_hub, meta, cut.point = FALSE, color = c("#2874C5", "#f87669"))
>
> **Arguments**
>
> exprSet_hub: a tumor expression set for hubgenes
>
> meta: meta data corresponds to expression set
>
> cut.point: logical , use cut_point or not, if FALSE,use median by defult
>
> color: color for boxplot

可以从参数中看到增加了`cut.point()`函数 的设定，并且默认是FALSE，也就是按median均值分组，而不是之前的最佳截点了，并且也修改了默认配色，这样我演示如下:

## 按均值分组批量生存分析

对于多基因的生存曲线图，直接使用exp_surv()函数绘图，并且使用patchwork的lot_layout函数添加了统一标题的设定，见Figure \@ref(fig:fig1)所示


```r
exp <- t(data[, 6:14])  ## 记得这里是转置表格
meta <- data[, 1:3]  #也可以不改
tmp = tinyarray::exp_surv(exp, meta)
```

```
## 
```

```r
patchwork::wrap_plots(tmp) + patchwork::plot_layout(guides = "collect")  #调用patchwork包拼图
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-17-利用tinyarry批量画生存曲线第二波/tinyarray-exp_surv/fig1-1.png" alt="按均值分组的批量生存分析"  />
<p class="caption">按均值分组的批量生存分析</p>
</div>

## 按最佳截点进行批量生存分析

需要最佳截点，至于设置cut.point=TRUE即可，见Figure \@ref(fig:fig2)


```r
exp <- t(data[, 6:14])  ## 记得这里是转置表格
meta <- data[, 1:3]  #也可以不改
tmp2 = tinyarray::exp_surv(exp, meta, cut.point = T)
```

```
## 9 rows with less than 5 values were ignored in cut.point calculations
```

```r
patchwork::wrap_plots(tmp2) + patchwork::plot_layout(guides = "collect")  #调用patchwork包拼图
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-08-17-利用tinyarry批量画生存曲线第二波/tinyarray-exp_surv/fig2-1.png" alt="按最佳截点分组的批量生存分析"  />
<p class="caption">按最佳截点分组的批量生存分析</p>
</div>

跟我之前的教程比较，结果是一模一样的。。。
