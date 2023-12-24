---
title: 批量生存分析并导出pdf
author: 欧阳松
date: '2022-02-24'
slug: multi-km2
categories:
  - R
tags:
  - 生存分析
from_Rmd: yes
---

之前写了个[多个基因的生存分析曲线一步绘制法](/course/multi-km-facet/)的教程，然而还是有不少问题，比如P值都是一样的，之前我不会批量的函数，不过最近看了些批量计算的函数，又学到了一些，分享一下代码

之前的教程是用`cbind()`函数计算高低组然后再合并，然而用循环函数可以一步计算好，我们依然使用示例数据[data.tsv](/course/multi-km-facet/data.tsv)，数据经过预处理后见Table \@ref(tab:data)所示。

    data<-read.csv("/data.tsv",sep="\t",row.names = 1,header = T)
    ### 修改行名
    colnames(data)[c(2:3,6:14)]<-c('status','time',
                                   'RFC1','RFC2','RCF3','RFC4','RFC5',
                                   'BEST1','BEST2','BEST3','BEST4')
    ### 去掉NA值，否则计算均值后会是NA值
    data<-na.omit(data)
    ### 查看一下数据
    data[1:10,c(2:3,6:14)]


Table: 显示多基因前10数据

|                | status| time|  RFC1|  RFC2|  RCF3|  RFC4|  RFC5|   BEST1|  BEST2|   BEST3|   BEST4|
|:---------------|------:|----:|-----:|-----:|-----:|-----:|-----:|-------:|------:|-------:|-------:|
|TCGA-2F-A9KR-01 |      1| 3183| 4.750| 4.990| 3.268| 4.973| 3.803| -1.4310| -5.574| -3.1710| -0.1993|
|TCGA-XF-A8HF-01 |      1| 2954| 2.554| 4.852| 3.018| 4.241| 3.353| -0.8599| -9.966| -9.9660|  0.1776|
|TCGA-XF-AAME-01 |      1| 2828| 4.083| 4.691| 2.895| 4.838| 3.368|  1.6790| -5.012| -9.9660| -1.2480|
|TCGA-UY-A78N-01 |      1| 2641| 4.889| 6.278| 1.696| 5.480| 4.733| -0.0725| -9.966| -9.9660| -4.0350|
|TCGA-XF-A9SL-01 |      1| 2020| 3.964| 5.241| 2.452| 4.418| 3.025|  0.9268| -9.966| -9.9660| -2.2450|
|TCGA-XF-A9SH-01 |      1| 1971| 3.483| 3.470| 0.679| 2.710| 4.248| -1.0260| -3.626| -9.9660| -1.1810|
|TCGA-XF-AAN2-01 |      1| 1869| 4.843| 5.537| 4.593| 4.959| 4.342| -0.3566| -9.966| -0.8863| -1.8840|
|TCGA-G2-A2EO-01 |      1| 1804| 3.725| 5.303| 2.768| 5.265| 4.104|  0.6969| -9.966| -6.5060| -2.4660|
|TCGA-XF-AAN0-01 |      1| 1718| 4.174| 5.440| 3.139| 4.936| 3.277| -0.1345| -4.608| -9.9660| -2.7270|
|TCGA-XF-AAMJ-01 |      1| 1670| 3.847| 4.388| 2.108| 3.972| 4.648|  0.6425| -9.966| -9.9660|  0.5763|

## 批量一：一步计算出基因表达的高低组并导出图片

直接按照基因表达量的中位数分为高低组的批量函数，见Table @ref(tab:data2)所示，是不是比之前的函数更快更简单？


```r
### 计算6列到16列的基因表达量均值并分组
a <- apply(data[, 6:14], 2, median)
a = data.frame(a)

for (i in 6:14) {
  n = colnames(data)[i]
  data[, n] = ifelse(data[, n] >= a[n, ], "High", "Low")
  data[, n] <- factor(data[, n], levels = c("Low", "High"))
}

### 再次查看一下数据
data[1:10, c(2:3, 6:14)]
```

```
##                 status time RFC1 RFC2 RCF3 RFC4 RFC5 BEST1 BEST2 BEST3 BEST4
## TCGA-2F-A9KR-01      1 3183 High  Low High High  Low   Low  High  High  High
## TCGA-XF-A8HF-01      1 2954  Low  Low High  Low  Low   Low   Low   Low  High
## TCGA-XF-AAME-01      1 2828  Low  Low  Low  Low  Low  High  High   Low  High
## TCGA-UY-A78N-01      1 2641 High High  Low High High   Low   Low   Low   Low
## TCGA-XF-A9SL-01      1 2020  Low High  Low  Low  Low  High   Low   Low   Low
## TCGA-XF-A9SH-01      1 1971  Low  Low  Low  Low High   Low  High   Low  High
## TCGA-XF-AAN2-01      1 1869 High High High High High   Low   Low  High   Low
## TCGA-G2-A2EO-01      1 1804  Low High  Low High High  High   Low  High   Low
## TCGA-XF-AAN0-01      1 1718 High High High High  Low   Low  High   Low   Low
## TCGA-XF-AAMJ-01      1 1670  Low  Low  Low  Low High  High   Low   Low  High
```


Table: 多基因批量处理后的前10数据

|                | status| time|RFC1 |RFC2 |RCF3 |RFC4 |RFC5 |BEST1 |BEST2 |BEST3 |BEST4 |
|:---------------|------:|----:|:----|:----|:----|:----|:----|:-----|:-----|:-----|:-----|
|TCGA-2F-A9KR-01 |      1| 3183|High |Low  |High |High |Low  |Low   |High  |High  |High  |
|TCGA-XF-A8HF-01 |      1| 2954|Low  |Low  |High |Low  |Low  |Low   |Low   |Low   |High  |
|TCGA-XF-AAME-01 |      1| 2828|Low  |Low  |Low  |Low  |Low  |High  |High  |Low   |High  |
|TCGA-UY-A78N-01 |      1| 2641|High |High |Low  |High |High |Low   |Low   |Low   |Low   |
|TCGA-XF-A9SL-01 |      1| 2020|Low  |High |Low  |Low  |Low  |High  |Low   |Low   |Low   |
|TCGA-XF-A9SH-01 |      1| 1971|Low  |Low  |Low  |Low  |High |Low   |High  |Low   |High  |
|TCGA-XF-AAN2-01 |      1| 1869|High |High |High |High |High |Low   |Low   |High  |Low   |
|TCGA-G2-A2EO-01 |      1| 1804|Low  |High |Low  |High |High |High  |Low   |High  |Low   |
|TCGA-XF-AAN0-01 |      1| 1718|High |High |High |High |Low  |Low   |High  |Low   |Low   |
|TCGA-XF-AAMJ-01 |      1| 1670|Low  |Low  |Low  |Low  |High |High  |Low   |Low   |High  |

批量函数

``` {.R}
library(survminer)
library(survival)

vars= colnames(data[,6:14])
for (i in vars) {
    splots<-list()
    km_fit<-surv_fit(Surv(time,status)~data[,i],data = data)
    splots[[1]]<-ggsurvplot(km_fit,
                            xlab="Time_days",
                            pval = T,pval.method = T,
                            legend=c(0.8,0.8),
                            title=i,
                            legend.title=i,legend.labs=levels(data[[i]]),
                            conf.int = T,conf.int.style='step',
                            surv.median.line = 'hv',
                            palette = 'lancet',
                            risk.table = T,
                            risk.table.pos=c('in'),
                            ggtheme = theme_bw(base_size = 12))
    
    res<-arrange_ggsurvplots(splots,print = F,ncol = 1,nrow = 1)
    ggsave(paste(i,'all.pdf',sep = "_"), res,width=6,height = 6)
}
```

这时候你会发现目标文件夹会多出很多图，直接计算好了

## 批量二：直接批量函数

    for (gene in colnames(data[6:14])) {
    group<-ifelse(data[[gene]]>median(data[[gene]]),
                  'High','Low')
    group<-factor(group,levels = c('Low',"High"))
    splots <- list()
    fit<-survfit(Surv(time,status)~group,data = data)
    splots[[1]]<-ggsurvplot(fit,
                        data = data,
                        legend.title= gene,
                        title= gene,
                        legend=c(0.85,0.85),
                        legend.labs=c('Low','High'),
                        pval = T,
                        conf.int = T,conf.int.style='step',
                        xlab="Time_days",
                        surv.median.line = 'hv',
                        palette = "lancet",
                        ggtheme = theme_bw(base_size = 12))
    res<-arrange_ggsurvplots(splots,print = F,ncol = 1,nrow = 1)
    ggsave(paste(gene,'os.pdf',sep = '_'),res,width = 6,height = 4.5)
    }
