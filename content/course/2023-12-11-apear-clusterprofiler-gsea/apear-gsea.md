---
title: 使用aPEAR来增强clusterProfiler的GSEA分析结果
author: 生信技能树
date: '2023-12-11'
slug: apear-gsea
categories:
  - aPEAR
  - GSEA
  - 富集分析
  - 教程
tags:
  - GSEA
  - aPEAR
  - 富集分析
  - 画图
  - 教程
from_Rmd: yes
---

最近在**生信技能树**的公众号发现一篇推文，[使用aPEAR来增强clusterProfiler的GSEA分析结果](https://mp.weixin.qq.com/s/q5KHfQv3LoJupAX4TCiZfQ)，今天复现一下。

## 转录组差异分析

首先我们获取**airway**这个包里面的`airway`表达量矩阵，它有8个样品，分成了两组，我们命名为'control'，'case' ，如下所示代码：


```r
library(data.table)
library(airway, quietly = T)
data(airway)
mat <- assay(airway)
keep_feature <- rowSums(mat > 1) > 1
ensembl_matrix <- mat[keep_feature, ]
library(AnnoProbe)
ids = annoGene(rownames(ensembl_matrix), "ENSEMBL", "human")
ids = ids[!duplicated(ids$SYMBOL), ]
ids = ids[!duplicated(ids$ENSEMBL), ]
symbol_matrix = ensembl_matrix[match(ids$ENSEMBL, rownames(ensembl_matrix)), ]
rownames(symbol_matrix) = ids$SYMBOL
group_list = as.character(airway@colData$dex)
group_list = ifelse(group_list == "untrt", "control", "case")
group_list = factor(group_list, levels = c("control", "case"))
```

然后针对上面的airway这个包里面的airway表达量矩阵，是一个简单的转录组测序后的count矩阵，可以走**DESeq2**进行转录组差异分析，代码如下所示：


```r
library(DESeq2)
colData <- data.frame(row.names = colnames(symbol_matrix), group_list = group_list)
dds <- DESeqDataSetFromMatrix(countData = symbol_matrix, colData = colData, design = ~group_list)
dds <- DESeq(dds)
res <- results(dds, contrast = c("group_list", levels(group_list)[2], levels(group_list)[1]))
resOrdered <- res[order(res$padj), ]
DEG = as.data.frame(resOrdered)
DEG_deseq2 = na.omit(DEG)  ##去掉NA值
```

查看一下差异分析的结果矩阵
```
head(DEG_deseq2) 
```

|        | baseMean| log2FoldChange|  lfcSE|  stat| pvalue| padj|
|:-------|--------:|--------------:|------:|-----:|------:|----:|
|SPARCL1 |    997.3|          4.602| 0.2117| 21.73|      0|    0|
|STOM    |  11193.6|          1.451| 0.0846| 17.15|      0|    0|
|PER1    |    776.5|          3.183| 0.2013| 15.81|      0|    0|
|PHC2    |   2738.0|          1.386| 0.0916| 15.13|      0|    0|
|MT2A    |   3656.0|          2.203| 0.1472| 14.97|      0|    0|
|DUSP1   |   3408.8|          2.948| 0.2017| 14.61|      0|    0|

## 使用clusterProfiler的GSEA方法针对GO数据库进行注释

前面的**DESeq2**进行转录组差异分析后的表格里面有两万多个基因，需要对它们根据里面的log2FoldChange对基因排序后的全部的基因的列表。
代码如下所示：

```r
nrDEG=DEG_deseq2[,c("log2FoldChange",   "padj")] 
colnames(nrDEG)=c('logFC','P.Value')
library(org.Hs.eg.db)
library(clusterProfiler)
gene <- bitr(rownames(nrDEG), fromType = "SYMBOL",
             toType =  "ENTREZID", ## z转换成更为稳定的ENTREZID基因格式
             OrgDb = org.Hs.eg.db) 
nrDEG = nrDEG[rownames(nrDEG) %in% gene$SYMBOL,]
nrDEG$ENTREZID = gene$ENTREZID[match(rownames(nrDEG) , gene$SYMBOL)]
# https://www.ncbi.nlm.nih.gov/gene/?term=SPARCL1
geneList=nrDEG$logFC
names(geneList)=nrDEG$ENTREZID
geneList=sort(geneList,decreasing = T)
# 运行GSEA的GO
## go_BP_enrich <- gseGO(geneList, OrgDb = org.Hs.eg.db, ont = 'BP')
## head(go_BP_enrich@result)
```
如果网络不好，也可以试试离线gson的方式,参考[一次性运行多组GSEA富集分析](/course/2022-11-20-multi-gsea/multi-gsea/)的教程
```
library(gson)
GO<-rread.gson("GO_BP_human.gson")
go_BP_enrich <- GSEA(geneList,gson = GO )
head(go_BP_enrich@result)
```


```
##                    ID                     Description setSize enrichmentScore
## GO:0046323 GO:0046323                  glucose import      57          0.6629
## GO:0046324 GO:0046324    regulation of glucose import      45          0.6763
## GO:0097501 GO:0097501    stress response to metal ion      11          0.9220
## GO:0071294 GO:0071294   cellular response to zinc ion      12          0.8985
## GO:1904659 GO:1904659 glucose transmembrane transport      82          0.5877
## GO:0008645 GO:0008645  hexose transmembrane transport      84          0.5743
##              NES    pvalue p.adjust  qvalue rank                   leading_edge
## GO:0046323 2.096 5.158e-06  0.01371 0.01266 2503 tags=42%, list=16%, signal=35%
## GO:0046324 2.065 1.021e-05  0.01371 0.01266 2503 tags=42%, list=16%, signal=35%
## GO:0097501 2.028 3.922e-06  0.01371 0.01266  119  tags=45%, list=1%, signal=45%
## GO:0071294 2.005 2.211e-05  0.01371 0.01266  119  tags=42%, list=1%, signal=41%
## GO:1904659 1.980 1.962e-05  0.01371 0.01266 2518 tags=38%, list=16%, signal=32%
## GO:0008645 1.954 2.135e-05  0.01371 0.01266 2518 tags=37%, list=16%, signal=31%
##                                                                                                                                                                       core_enrichment
## GO:0046323                                          28999/3952/6272/8660/10580/5295/51422/5167/23596/3667/55829/55022/53916/8218/3643/4205/6443/5781/3099/10999/6513/151176/6253/2887
## GO:0046324                                                                     28999/3952/8660/10580/5295/51422/5167/23596/3667/55829/55022/8218/3643/4205/5781/3099/151176/6253/2887
## GO:0097501                                                                                                                                                   4501/4495/4499/4502/4493
## GO:0071294                                                                                                                                                   4501/4495/4499/4502/4493
## GO:1904659 28999/3952/6272/8660/8013/10580/5295/51422/5167/50651/252983/23596/3667/55829/55022/53916/8218/3643/4205/51763/6443/5781/81031/3099/10999/6513/151176/6253/56606/2887/2171
## GO:0008645 28999/3952/6272/8660/8013/10580/5295/51422/5167/50651/252983/23596/3667/55829/55022/53916/8218/3643/4205/51763/6443/5781/81031/3099/10999/6513/151176/6253/56606/2887/2171
```

## 默认的可视化
使用enrichplot进行可视化，或者利用我修改过的myenrichplot包美化

```r
p1 <- enrichplot::gseaplot2(go_BP_enrich, geneSetID = go_BP_enrich@result[1, 1],
  title = go_BP_enrich@result$Description[1], pvalue_table = T)
p1
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2023-12-11-apear-clusterprofiler-gsea/apear-gsea/gseaplot-1.png" alt="plot of chunk gseaplot"  />
<p class="caption">plot of chunk gseaplot</p>
</div>
或者利用我修改过的myenrichplot包美化

```r
p2 <- myenrichplot::gseaplot2(go_BP_enrich, geneSetID = go_BP_enrich@result[1, 1],
  title = go_BP_enrich@result$Description[1], pvalue_table = T)
```

```
## Registered S3 methods overwritten by 'myenrichplot':
##   method                             from      
##   as.data.frame.compareClusterResult enrichplot
##   barplot.enrichResult               enrichplot
##   fortify.compareClusterResult       enrichplot
##   fortify.enrichResult               enrichplot
##   fortify.gseaResult                 enrichplot
##   ggplot_add.autofacet               enrichplot
```

```r
p2
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2023-12-11-apear-clusterprofiler-gsea/apear-gsea/gseaplot2-1.png" alt="plot of chunk gseaplot2"  />
<p class="caption">plot of chunk gseaplot2</p>
</div>
也可以是汇总这个GO数据库进行注释结果表格后展现其上下调的最显著的通路，代码如下所示

```r
# ～～～取前6个上调通路和6个下调通路～～～
kk = go_BP_enrich
up_k <- kk[head(order(kk$enrichmentScore, decreasing = T)), ]
up_k$group = 1
down_k <- kk[tail(order(kk$enrichmentScore, decreasing = T)), ]
down_k$group = -1

dat = rbind(up_k, down_k)
colnames(dat)
```

```
##  [1] "ID"              "Description"     "setSize"         "enrichmentScore"
##  [5] "NES"             "pvalue"          "p.adjust"        "qvalue"         
##  [9] "rank"            "leading_edge"    "core_enrichment" "group"
```

```r
dat$pvalue = -log10(dat$pvalue)
dat$pvalue = dat$pvalue * dat$group
dat = dat[order(dat$pvalue, decreasing = F), ]

library(ggplot2)
p3 <- ggplot(dat, aes(x = reorder(Description, order(pvalue, decreasing = F)), y = pvalue,
  fill = group)) + geom_bar(stat = "identity") + scale_fill_gradient(low = "#34bfb5",
  high = "#ff6633", guide = FALSE) + scale_x_discrete(name = "Pathway names") +
  scale_y_continuous(name = "log10P-value") + coord_flip() + theme_bw() + theme(plot.title = element_text(size = 15,
  hjust = 0.5), axis.text = element_text(size = 12, face = "bold"), panel.grid = element_blank()) +
  ggtitle("Pathway Enrichment")
p3
```

```
## Warning: The `guide` argument in `scale_*()` cannot be `FALSE`. This was deprecated in
## ggplot2 3.3.4.
## ℹ Please use "none" instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2023-12-11-apear-clusterprofiler-gsea/apear-gsea/top6-1.png" alt="前6个上调通路和6个下调通路"  />
<p class="caption">前6个上调通路和6个下调通路</p>
</div>
## 使用aPEAR来增强
一行代码可视化，可以看到，确实是效果不一样了，这也就是为什么网络可视化非常受大家欢迎。

```r
# install.packages('aPEAR')
library(aPEAR)
enrichmentNetwork(go_BP_enrich@result)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2023-12-11-apear-clusterprofiler-gsea/apear-gsea/enrichmentNetwork-1.png" alt="enrichmentNetwork的效果"  />
<p class="caption">enrichmentNetwork的效果</p>
</div>
当然了，这个包（aPEAR）肯定是不会就这一个 enrichmentNetwork 函数，更多好玩的用法和参数，可以看官方文档：

- https://github.com/kerseviciute/aPEAR
- aPEAR: an R package for enrichment network visualisation
- aPEAR: an R package for autonomous visualisation of pathway enrichment networks. BioRxiv. 2023 Mar 29; doi: 10.1101/2023.03.28.534514
