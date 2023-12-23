---
title: AnnoProbe：一个可以快速下载并处理GEO数据的好包
author: 欧阳松
date: '2021-09-05'
slug: annoprobe-一个可以快速下载并处理geo数据的好包
categories:
  - 教程
  - R
tags:
  - GEO
  - 教程
---

之前我在简书上写过一个"[GEO数据国内镜像下载、基因注释、差异分析、火山图、热图及后续处理](https://www.jianshu.com/p/b51892e9ad15)"的教程，现在看来，这个教程还是有一些误区的，留了一些坑。现在AnnoProbe已经上市，可以重新再演示一下。

首先我想说一下关于这个包的历史，**AnnoProbe**是[曾建明](https://github.com/jmzeng1314)老师2020年开发的一款用于下载GEO数据集并注释的R包，最初开发的是一个叫GEOmirror的包，而更早之前是建明老师开发了四个用于注释GEO各种ID的包，大家都知道GEO数据里有很多都是基因芯片，而注释平台也很多很杂，所以经常需要下载各种平台注释文件去注释基因ID，这大大的浪费了大家的时间。而建明老师应该全国第一批在生物信息学领域孜孜不倦的大神，因此开发了很多造福大家的R包。

在他开发完这4个ID注释包以后，又给我们解决了下载GEO数据的最大困扰，那就是下载的网速，我们用过GEO数据库的人都知道，下载他家文件的网速啊。。。。极其的不稳定。经常绝大多数时间都浪费在加载网速上，而且还老是下载一半就掉线，最终导致下载的数据不全，所以建明老师又自购服务器，把**绝大多数**的GEO数据进行了备份，同时开发了一个叫GEOmirror的包，以前托管在[Github](https://github.com/jmzeng1314/GEOmirror)上，但那也是个网速不稳定的坑，所以建明老师又同步到了[Gitee](https://gitee.com/jmzeng/GEOmirror)上（备注里写着：备份了GEO数据库里面的几乎全部的表达量芯片数据集的表达量矩阵和临床信息矩阵）。而这里大家也一定要注意，因为GEO是不断更新的，而这个包却不是，比较是个人的服务器，没有那么大的空间，我们要饮水思源，但是也不能过分强求。

之后建明老师把这些包全部都整合了，也就是把GEO下载和基因ID注释全部整合起来，然后还加入了火山图、热图这两个基本功能，所以对于GEO数据的挖掘是很方便的。

另外，多说一句，**tinyarray**这个包其实也已经整合了**AnnoProbe**包。

------------------------------------------------------------------------

## 安装AnnoProbe

目前AnnoProbe已经被CRAN收录了，所以安装起来非常方便，只需要一句话即可

    install.package('AnnoProbe')

或者也可以用Gitee安装，代码是：

    remotes::install_git('https://gitee.com/jmzeng/AnnoProbe')

安装的中途会提示你安装一些依赖的包，比如ggplot2, DT, ggpubr, pheatmap, utils, methods, Biobase, stats。

同时建议你先提前把limma和GEOquery这两个包装上，一个包用于差异分析，一个包用于提取GEO的数据。这两个包在Bioconductor上，安装的话需要用下面的代码安装。

    BiocManager::install(c(‘limma’，‘GEOquery’))

------------------------------------------------------------------------

## 下载GEO数据

比如我们在网上搜索了**GSE13507**这个数据集，我们现在需要下载数据，只需要一句代码即可，由于是国内的服务器，所以十几M的文件，下载的数据很快

    gset=AnnoProbe::geoChina('GSE13507')



现在，我们大致看一下这个数据，直接输入gset即可，可以看到一些基本信息，比如256个样本，可以看到引用文献的pubmedID，还有注释平台是GPL6102.


```r
gset
```

```
## $GSE13507_series_matrix.txt.gz
```

```
## 载入需要的程辑包：Biobase
```

```
## 载入需要的程辑包：BiocGenerics
```

```
## 载入需要的程辑包：parallel
```

```
## 
## 载入程辑包：'BiocGenerics'
```

```
## The following objects are masked from 'package:parallel':
## 
##     clusterApply, clusterApplyLB, clusterCall, clusterEvalQ,
##     clusterExport, clusterMap, parApply, parCapply, parLapply,
##     parLapplyLB, parRapply, parSapply, parSapplyLB
```

```
## The following objects are masked from 'package:stats':
## 
##     IQR, mad, sd, var, xtabs
```

```
## The following objects are masked from 'package:base':
## 
##     anyDuplicated, append, as.data.frame, basename, cbind, colnames,
##     dirname, do.call, duplicated, eval, evalq, Filter, Find, get, grep,
##     grepl, intersect, is.unsorted, lapply, Map, mapply, match, mget,
##     order, paste, pmax, pmax.int, pmin, pmin.int, Position, rank,
##     rbind, Reduce, rownames, sapply, setdiff, sort, table, tapply,
##     union, unique, unsplit, which.max, which.min
```

```
## Welcome to Bioconductor
## 
##     Vignettes contain introductory material; view with
##     'browseVignettes()'. To cite Bioconductor, see
##     'citation("Biobase")', and for packages 'citation("pkgname")'.
```

```
## ExpressionSet (storageMode: lockedEnvironment)
## assayData: 43148 features, 256 samples 
##   element names: exprs 
## protocolData: none
## phenoData
##   sampleNames: GSM340537 GSM340538 ... GSM340792 (256 total)
##   varLabels: title geo_accession ... systemic chemo:ch1 (66 total)
##   varMetadata: labelDescription
## featureData: none
## experimentData: use 'experimentData(object)'
##   pubMedIds: 20059769
## 20421545
## 23060996
## 28781684
## 20096140 
## Annotation: GPL6102
```

### 提取表型数据或者临床信息数据

这个需要先加载GEOquery，然后需要先提取gset\[\[1\]\]的数据，通过`pData()`函数获取分组信息，这里包含了所有的临床数据。


```r
suppressMessages(library(GEOquery))
## 获取ExpressionSet对象，包括的表达矩阵和分组信息
gset=gset[[1]]
phenoDat <- pData(gset) 
```


Table: Table 1: 部分临床表型信息

|          |title                     |geo_accession |status                |submission_date |last_update_date |type |
|:---------|:-------------------------|:-------------|:---------------------|:---------------|:----------------|:----|
|GSM340537 |Control C010              |GSM340537     |Public on May 06 2010 |Nov 07 2008     |May 06 2010      |RNA  |
|GSM340538 |Control C017              |GSM340538     |Public on May 06 2010 |Nov 07 2008     |May 06 2010      |RNA  |
|GSM340539 |Control C029              |GSM340539     |Public on May 06 2010 |Nov 07 2008     |May 06 2010      |RNA  |
|GSM340540 |Control C030              |GSM340540     |Public on May 06 2010 |Nov 07 2008     |May 06 2010      |RNA  |
|GSM340541 |Control C040              |GSM340541     |Public on May 06 2010 |Nov 07 2008     |May 06 2010      |RNA  |
|GSM340542 |Vav-Control MPP RNA-seq 1 |GSM340542     |Public on May 06 2010 |Nov 07 2008     |Sep 27 2018      |RNA  |
|GSM340543 |Control C044              |GSM340543     |Public on May 06 2010 |Nov 07 2008     |May 06 2010      |RNA  |
|GSM340544 |Control C045              |GSM340544     |Public on May 06 2010 |Nov 07 2008     |May 06 2010      |RNA  |
|GSM340545 |Control C051              |GSM340545     |Public on May 06 2010 |Nov 07 2008     |May 06 2010      |RNA  |
|GSM340546 |Control C072              |GSM340546     |Public on May 06 2010 |Nov 07 2008     |May 06 2010      |RNA  |

### **获取分组信息**

分组信息是要人为定义的，比如你这个数据里面，前68个是正常组，后188个是肿瘤组织，我们就可以直接按出现的顺序分组：

    group_list=factor(c(rep('Normal',68),rep('Cancer',188)))

但是，更多的时候，有些数据不是按顺序分组的话，而有些大样本的话直接数数字也很麻烦。这时候我们就可以用**stringr**这个包，按照关键词进行分组，比如在title列里，虽然是control正常组织，但是关键词是Surrounding的也是正常组织，而有bladder的都是肿瘤组织，那么我们就可以定义title列里出现bladder的都定义为癌症组织。

要注意，分组列表信息主要转换为**因子**形式，这样可以固定顺序。


```r
##按title自定义，如包含‘bladder’定义为‘cancer’
library(stringr)
group_list=ifelse(str_detect(phenoDat$title,"bladder"),"cancer","normal")
#设置参考水平，对照在前，处理在后
group_list = factor(group_list,
                    levels = c("normal","cancer"))
table(group_list)
```

```
## group_list
## normal cancer 
##     68    188
```

### **获取基因表达矩阵并校正**

可以同通过`exprs()`函数获取表达矩阵


```r
exprSet=exprs(gset)
dim(exprSet) #看一下基因数量和样本数量
```

```
## [1] 43148   256
```


Table: Table 2: 部分基因表达矩阵信息

|             | GSM340537| GSM340538| GSM340539| GSM340540| GSM340541| GSM340542|
|:------------|---------:|---------:|---------:|---------:|---------:|---------:|
|ILMN_1651199 |      7.12|      6.90|      6.98|      6.93|      7.10|      6.97|
|ILMN_1651209 |      7.54|      7.53|      7.75|      7.69|      7.45|      7.52|
|ILMN_1651210 |     10.29|     10.34|     10.06|     10.30|     10.02|     10.90|
|ILMN_1651217 |      7.06|      7.10|      7.03|      7.03|      6.89|      7.46|
|ILMN_1651221 |      7.32|      7.06|      7.00|      7.07|      7.08|      7.26|
|ILMN_1651228 |     15.51|     15.34|     15.16|     15.34|     15.26|     15.33|

但我们不能满足这个表达矩阵，我们还需要知道这个矩阵是否需要校正，是否需要经过log2的处理等等。

#### 是否需要校正基因表达矩阵

拿到基因表达矩阵后，首先要大致看一下分布是否整齐，由于前68个正常组织，我们可以去掉这些数据，然后使用最基本的boxplot函数看一下整体分布是否在一条线上，见图 <a href="#fig:box">1</a>所示。


```r
boxplot(exprSet[,-c(1:68)],outline=FALSE, notch=T,col=group_list, las=2)
```

<div class="figure" style="text-align: center">
<img src="{{< blogdown/postref >}}index_files/figure-html/box-1.png" alt="未校正前的基因分布" width="672" />
<p class="caption">Figure 1: 未校正前的基因分布</p>
</div>

这里可以看到数据是整齐的，不需要校正了。但是有时候，有些数据是不齐的，那么就需要校正，校正的方法有很多，我们可以用limma包内置的一个函数normalizeBetweenArrays()，然后就可以全部校正到一条线上，见图 <a href="#fig:box2">2</a>


```r
library(limma) 
```

```
## 
## 载入程辑包：'limma'
```

```
## The following object is masked from 'package:BiocGenerics':
## 
##     plotMA
```

```r
exprSet2=normalizeBetweenArrays(exprSet)
boxplot(exprSet2,outline=FALSE, notch=T,col=group_list, las=2)
```

<div class="figure" style="text-align: center">
<img src="{{< blogdown/postref >}}index_files/figure-html/box2-1.png" alt="校正后的基因分布" width="672" />
<p class="caption">Figure 2: 校正后的基因分布</p>
</div>

由于这个数据本来就是整齐的，所以看不出差异，你也可以试试别的数据，比如GSE32575。

#### 是否需要进行数据log2转换

很多GEO的数据，由于最大值和最小值差异特别大，需要进行log2的归一化处理，这样做的目的是为了在一个理想的范围内缩小差距，不至于看起来差异太大。在GEO2R上，官网其实提供了一个可以自动判断是否需要log转换的脚步，最下面的一句话是如果没有转换的话，自动转换成功，如果不需要转换，就提示log2转换不需要。我们可以借鉴一下。


```r
ex <- exprSet
qx <- as.numeric(quantile(ex, c(0., 0.25, 0.5, 0.75, 0.99, 1.0), na.rm=T))
LogC <- (qx[5] > 100) ||
  (qx[6]-qx[1] > 50 && qx[2] > 0) ||
  (qx[2] > 0 && qx[2] < 1 && qx[4] > 1 && qx[4] < 2)

if (LogC) { ex[which(ex <= 0)] <- NaN
exprSet <- log2(ex)
print("log2 transform finished")}else{print("log2 transform not needed")}
```

```
## [1] "log2 transform not needed"
```

可以看到，GSE13507这个数据集非常好，不需要校正，也不需要归一化，是一个已经处理好的标识GEO数据，以后需要这个数据集的话，不需要对它进行前置处理。

---

## 基因ID注释

然后，这里我们看到基因的ID名不是我们需要的symbol啊，这个基因的开头是ILMN_xxx，这个我们就没办法进行后续分析，这时候需要进行ID的注释转换，而**Annoprobe**是可以很快的转换的。获取注释信息用的是`idmap()`函数，而转换ID则需要`filterEM()`函数，而且有时候我们会遇到注释后symbol重复的情况，而`filterEM()`默认删除重复的基因符号。原理是只保留最大值。（关于对重复基因是选择最小值、最大值还是平均值，这个有争议，但是有一个无脑的工具，就不要考虑那么多了，不嫌麻烦又纠结的人可以自己看别的教程或者修改源码参数）



```r
library(AnnoProbe)
```

```
## AnnoProbe v 0.1.6  welcome to use AnnoProbe!
## If you use AnnoProbe in published research, please acknowledgements:
## We thank Dr.Jianming Zeng(University of Macau), and all the members of his bioinformatics team, biotrainee, for generously sharing their experience and codes.
```

```r
(gpl=gset@annotation) #提取注释平台
```

```
## [1] "GPL6102"
```

```r
ids<-idmap(gpl ,type = 'soft') #这里有三种type，默认是bioc，我们可以选择soft
```

```
## file downloaded in /Users/mac/Documents/Gitee/Rblog/content/course/2021-09-05-annoprobe-一个可以快速下载并处理geo数据的好包
```

这时候就自动提取了GPL的注释信息，有两列数据，一列是原始ID，一列是symbol，见表 <a href="#tab:ids">3</a>所示。


Table: Table 3: 平台注释信息

|ID           |symbol    |
|:------------|:---------|
|ILMN_1725881 |LOC23117  |
|ILMN_1910180 |          |
|ILMN_1804174 |FCGR2B    |
|ILMN_1810835 |SPRR3     |
|ILMN_1758197 |LOC653895 |
|ILMN_1668162 |DGAT2L3   |
|ILMN_1715600 |LOC387701 |
|ILMN_1912287 |          |
|ILMN_1889125 |          |
|ILMN_1682799 |STAMBPL1  |

使用`filterEM()`函数转换基本表达矩阵，自动删除重复基因，最终结果见表 <a href="#tab:exprSet2">4</a>所示。


```r
exprSet<-filterEM(exprSet,ids)
```

```
## input expression matrix is 43148 rows(genes or probes) and 256 columns(samples).
```

```
## input probe2gene is 48702 rows(genes or probes)
```

```
## after remove NA or useless probes for probe2gene, 43148 rows(genes or probes) left
```

```
## There are 43148 of 43148 probes can be annotated.
```

```
## output expression matrix is 24358 rows(genes or probes) and 256 columns(samples).
```


Table: Table 4: 部分基因表达矩阵信息

|       | GSM340537| GSM340538| GSM340539| GSM340540| GSM340541| GSM340542|
|:------|---------:|---------:|---------:|---------:|---------:|---------:|
|ZZZ3   |     10.28|     10.10|     10.64|      9.90|      9.89|      9.61|
|ZZEF1  |     10.50|     10.47|     10.42|     10.41|     10.46|     10.21|
|ZYX    |     11.49|     11.11|     11.43|     11.59|     10.65|     12.78|
|ZYG11B |     12.23|     11.63|     11.99|     12.13|     11.88|     11.21|
|ZYG11A |      6.89|      7.14|      6.92|      7.08|      6.99|      7.02|
|ZXDC   |      8.90|      9.76|      9.03|      9.53|      9.67|      9.10|

---

## 绘制火山图

火山图的绘制需要先计算差异表达基因，这个包选择的是limma法，所以首先进行差异表达分析，速度很快，结果见表 <a href="#tab:DEG">5</a>所示。


```r
# library(limma) #需要加载limma包，没有加载的把前面的#去掉
design=model.matrix(~factor(group_list))
fit=lmFit(exprSet,design)
fit=eBayes(fit)
DEG=topTable(fit,coef=2,n=Inf)
DEG=na.omit(DEG)
```


Table: Table 5: 差异表达分析结果

|        |     logFC|  AveExpr|         t| P.Value| adj.P.Val|        B|
|:-------|---------:|--------:|---------:|-------:|---------:|--------:|
|SRPX    | -2.700876| 9.136250| -14.26641|       0|         0| 67.27607|
|FAM107A | -2.277193| 8.657539| -14.16281|       0|         0| 66.46376|
|ABCA8   | -1.994565| 8.693477| -14.15038|       0|         0| 66.36636|
|MOXD1   | -1.944784| 8.486211| -14.01322|       0|         0| 65.29196|
|C4orf31 | -1.801411| 8.117383| -14.01213|       0|         0| 65.28343|
|LPPR4   | -1.430961| 7.687227| -13.89391|       0|         0| 64.35845|

这个包需要提取一些数据才能画火山图，处理后的表见表 <a href="#tab:DEG2">6</a>所示。


```r
need_deg=data.frame(symbols=rownames(DEG), logFC=DEG$logFC, p=DEG$P.Value)
```


Table: Table 6: 需要的差异表达分析结果

|symbols |     logFC|  p|
|:-------|---------:|--:|
|SRPX    | -2.700876|  0|
|FAM107A | -2.277193|  0|
|ABCA8   | -1.994565|  0|
|MOXD1   | -1.944784|  0|
|C4orf31 | -1.801411|  0|
|LPPR4   | -1.430961|  0|

火山图用的是deg_volcano()函数，有两种风格，第一种风格是ggplot2，第二种风格是ggpubr，而且可以现在上调和下调的基因数量。默认的logFC是1，如果设置logFC是0的话，那么计算的是动态差异倍数，也就是均数+2倍标准差的形式，而且p值也是可调的。

    deg_volcano(need_deg, style = 1, p_thred = 0.05, logFC_thred = 1)

我们先看默认的图，见图 <a href="#fig:volcano">3</a>所示。


```r
deg_volcano(need_deg) 
```

<div class="figure" style="text-align: center">
<img src="{{< blogdown/postref >}}index_files/figure-html/volcano-1.png" alt="火山图" width="672" />
<p class="caption">Figure 3: 火山图</p>
</div>

再看动态的图，见图 <a href="#fig:volcano2">4</a>所示。


```r
deg_volcano(need_deg,1,logFC_thred = 0) 
```

<div class="figure" style="text-align: center">
<img src="{{< blogdown/postref >}}index_files/figure-html/volcano2-1.png" alt="火山图" width="672" />
<p class="caption">Figure 4: 火山图</p>
</div>

最后再看第二种风格的图，见图 <a href="#fig:volcano3">5</a>所示。


```r
deg_volcano(need_deg,2) 
```

<div class="figure" style="text-align: center">
<img src="{{< blogdown/postref >}}index_files/figure-html/volcano3-1.png" alt="火山图" width="672" />
<p class="caption">Figure 5: 火山图</p>
</div>

---

## 画热图

热图也是很简单的，用deg_heatmap()函数，这个可以自己选择前多少对显著的差异基因，很方便，默认的是显示前20对，见图 <a href="#fig:heatmap">6</a>所示。


```r
deg_heatmap(DEG,exprSet,group_list)
```

<div class="figure" style="text-align: center">
<img src="{{< blogdown/postref >}}index_files/figure-html/heatmap-1.png" alt="热图" width="672" />
<p class="caption">Figure 6: 热图</p>
</div>

## 单个差异基因分布

用check_diff_genes()函数可以看单个基因在不同组的分布情况，比如随便选个基因，如TP53，看两组的分布，见图 <a href="#fig:diff">7</a>所示。


```r
 check_diff_genes('TP53',exprSet,group_list)
```

<div class="figure" style="text-align: center">
<img src="{{< blogdown/postref >}}index_files/figure-html/diff-1.png" alt="热图" width="672" />
<p class="caption">Figure 7: 热图</p>
</div>
