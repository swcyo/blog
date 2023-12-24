---
title: 利用tinyarray批量画生存曲线，支持最佳截点,也可以按均值分组
author: 欧阳松
date: '2022-02-25'
slug: tinyarray-km-cutoff
categories:
  - tinyarray
tags:
  - tinyarray
from_Rmd: yes
---

- **tinyarray**是个好包，功能强大，今天介绍**批量生存分析**，支持最佳截点（cutoff），函数是`exp_surv()` ，当然还可以支持箱式图和生存曲线同步显示，函数是`box_surv()`。 > 

> 具体的可以见[太好用了！批量生存分析，一步到位，还支持最佳截点](https://www.jianshu.com/p/5b623561eb29)

   
## 示例数据可视化
我们可以直接先运行示例文件看看效果，见下图所示

```
library(tinyarray)
library(ggplot2)
tmp = exp_surv(exprSet_hub1,meta1)
patchwork::wrap_plots(tmp)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-02-25-利用tinyarray批量画生存曲线-支持最佳截点/tinyarray-km-cutoff/fig1-1.png" alt="示例批量生存"  />
<p class="caption">示例批量生存</p>
</div>

## 数据的分析
现在，我们开始分析数据，我们可以看到生存结果的**tpm**其实是一系列的list的图，使用**pathchwork**进行封装拼图，那么如果我们知道了函数是否可以再次定制呢？

首先看示例数据，一个是`exprSet_hub1`，一个是`meta1`，可以看到`meta1`是生存状态和时间，而`exprSet_hub1`是一个基因表达矩阵，其中行是患者，列是基因，我们看看数据的形式。
```
exprSet_hub1<-exprSet_hub1
meta1<-meta1
exprSet_hub1[1:5,1:5]
meta1[1:5,]
```


|       | TCGA-3A-A9IO-01A| TCGA-US-A774-01A| TCGA-HZ-A49H-01A| TCGA-FB-A4P5-01A| TCGA-FB-AAPS-01A|
|:------|----------------:|----------------:|----------------:|----------------:|----------------:|
|CXCL8  |            8.031|            10.75|            13.33|            11.20|            8.104|
|FN1    |           19.128|            18.06|            16.07|            17.82|           17.930|
|COL3A1 |           17.631|            18.49|            19.08|            18.58|           18.439|
|ISG15  |           12.065|            11.48|            12.70|            13.78|           11.726|
|COL1A2 |           17.640|            18.41|            18.42|            18.41|           18.567|


|    |sample           | event|X_PATIENT    | time|
|:---|:----------------|-----:|:------------|----:|
|216 |TCGA-3A-A9IO-01A |     0|TCGA-3A-A9IO | 1942|
|172 |TCGA-US-A774-01A |     1|TCGA-US-A774 |  695|
|128 |TCGA-HZ-A49H-01A |     0|TCGA-HZ-A49H |  491|
|37  |TCGA-FB-A4P5-01A |     1|TCGA-FB-A4P5 |  179|
|45  |TCGA-FB-AAPS-01A |     0|TCGA-FB-AAPS |  228|

我们使用[data.tsv](/course/multi-km-facet/data.tsv)这个数据，先导入数据，改一下列名，记得列名一定是`event`和`time`

    data<-read.csv("/data.tsv",sep="\t",row.names = 1,header = T)
    ### 修改行名
    colnames(data)[c(2:3,6:14)]<-c('status','time',
                                   'RFC1','RFC2','RCF3','RFC4','RFC5',
                                   'BEST1','BEST2','BEST3','BEST4')
    ### 去掉NA值，否则计算均值后会是NA值
    data<-na.omit(data)



接着把数据稍微处理一下，变成`exp`和`meta`两个数据，然后直接运算出结果，见下图所示


```r
exp <- t(data[, 6:14])
meta <- data[, 1:3]  #也可以不改
tmp2 = exp_surv(exp, meta, color = c("#2874C5", "#f87669"))
patchwork::wrap_plots(tmp2)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-02-25-利用tinyarray批量画生存曲线-支持最佳截点/tinyarray-km-cutoff/fig2-1.png" alt="data多基因批量计算" width="95%" />
<p class="caption">data多基因批量计算</p>
</div>

## 数据的再次处理

然而这样仍有一个问题就是，**最佳截点并不是按均值分组**，能否可以再次变换呢?

这时候我们就需要去GitHub查找源代码，
<https://github.com/xjsun1221/tinyarray/blob/master/R/11_surv_box_plot.R>

找到函数以后我们适度的修改，美化什么的


```r
exp_surv <- function(exprSet_hub, meta, color = c("#2874C5", "#f87669")) {
  splots <- lapply(rownames(exprSet_hub), function(g) {
    i = which(rownames(exprSet_hub) == g)
    meta$gene = ifelse(as.numeric(exprSet_hub[g, ]) > median(as.numeric(exprSet_hub[g,
      ])), "high", "low")
    if (length(unique(meta$gene)) == 1)
      stop(paste0("gene", g, "with too low expression"))
    sfit1 = survival::survfit(survival::Surv(time, event) ~ gene, data = meta)
    p = survminer::ggsurvplot(sfit1, pval = TRUE, palette = rev(color), data = meta,
      legend = c(0.8, 0.8), title = rownames(exprSet_hub)[[i]], legend.title = "Expression",
      xlab = "Time_days", surv.median.line = "hv", risk.table = T, risk.table.pos = c("in"),
      ggtheme = theme_bw(base_size = 12))
    p2 = p$plot + theme(plot.title = element_text(hjust = 0.5))
    return(p2)
  })
  return(splots)
}
```

运行一下修改的代码，最终结果见下图所示


```r
t <- exp_surv(exp, meta)
patchwork::wrap_plots(t)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2022-02-25-利用tinyarray批量画生存曲线-支持最佳截点/tinyarray-km-cutoff/fig3-1.png" alt="按均值分组的data多基因批量计算" width="95%" />
<p class="caption">按均值分组的data多基因批量计算</p>
</div>

当然，更多可以自己修改函数显示更多的效果。
