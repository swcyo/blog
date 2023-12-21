---
title: 关于TCGA数据下载的一些知识
Slug: aboutTCGAdownload
Date: 2021-09-29
---

TCGA是最常用的肿瘤学生信数据库，包含了30多种肿瘤近万名患者的转录组测序数据和临床信息，是一个很全面的数据挖掘库。

按照特定的编号我们可以找到我们所需要的资料，一般临床信息包含了患者的基本情况、病理学特征和生存资料等，而每一个样本的RNA-seq数据就包含了将近6万个基因的测序数据，当然这里包括了mRNA、lnc RNA、miRNA和一些假基因等。

然而，就是因为包含了大量的数据，所以有源源不断的数据可以挖掘，同时对于新手而言，也存在非常大的困难，关于TCGA的介绍，网上有大量的教程，我这里不做过多的介绍，我只说一些我遇到的一些情况，大致介绍我常用的一些资源。

## 什么是TCGA？

TCGA的全称是The Cancer Genome Atlas，翻译过来就是癌症基因组图谱，这是一个具有里程碑意义的癌症基因组学项目，它对20000多个原发性癌症进行了分子表型，并匹配了33种癌症类型的正常样本。这项联合项目始于2006年，目前还是缓慢的更新中。。。（其实现在基本也不更新了）

TCGA的官网是<https://www.cancer.gov/about-nci/organization/ccg/research/structural-genomics/tcga>，但其实我们常选择的是GDC（<https://portal.gdc.cancer.gov/>）实时更新下载。

## TCGA数据库里都储存了什么信息？

1.  临床样本信息：Biospecimen、Clinical

2.  测序数据：RNA sequencing、 MicroRNA sequencing、 DNA sequencing、SNP-based platforms、Array-based DNA methylation sequencing、Reverse-phase array(RPPA)等。可以说包括了基因组，转录组，表观遗传，蛋白组等各个组学数据。

## 如何下载TCGA实时数据？

TCGA的数据虽然目前更新很少了，但并不表示他不更新，以前有个很强的包叫`RTCGA`的包，包含了TCGA的数据，功能也比较齐全，但是它也很久没有更新了，所以不是很推荐，我们最好用实时的数据，当然如果你要写文章的话，一定要备注好是在哪一天下载的数据。

目前有很多很多方法可以实时下载TCGA的数据，其中R语言是最常用的办法，原理都是通过API方法去爬取官网数据，但其实你并不一定需要下载所有的数据，甚至你也可以不用R语言也可以很快捷的得到你需要的数据。

### 官网获得实时数据

通过访问[GDC (cancer.gov)](https://portal.gdc.cancer.gov/)，可以获得最权威的数据，但是很多时候国内的网并不是很稳定，有时候你还需要下载一个专门的工具，因此，一般我不推荐用官网下载。

## TCGAbiolinks获得实时数据

**TCGAbiolinks**是一个可以爬取TCGA实时数据的R包，同时还可以爬取健康人群GTEx的数据。

`TCGAbiolinks` 包的功能主要可以分为三大块：

-   数据查询和下载

-   数据的常规分析

-   可视化

该包可以从 `Bioconductor` 上安装稳定版本

    if (!requireNamespace("BiocManager", quietly=TRUE))
        install.packages("BiocManager")
    BiocManager::install("TCGAbiolinks")

或者从 `GitHub` 上安装开发版本

    if (!requireNamespace("BiocManager", quietly = TRUE))
        install.packages("BiocManager")
    BiocManager::install("BioinformaticsFMRP/TCGAbiolinksGUI.data")
    BiocManager::install("BioinformaticsFMRP/TCGAbiolinks")

当然，具体如何下载数据，简书或知乎上教程有很多。

## **GDCRNAtools**获得实时数据

**GDCRNAtools也**是一个可以爬取TCGA实时数据的R包，同时还可以爬取小儿肿瘤TARGET的数据。不过它的主要功能其实是寻找lncRNAs相关的ceRNAs。为方便用户使用，也加入了TCGA数据下载、整理和常规分析工具。

该包提供了常规的火山图，柱状图，散点图，富集分析气泡图，生存曲线等等。同时还可以使用GDCRNATools中三个简单的shiny工具在网页上选取感兴趣的基因，通路来观察他们的生存曲线，表达相关性，及富集的KEGG通路map。目前已经收录在Bioconductor上了。

该包可以从 `Bioconductor` 上安装稳定版本

    if (!requireNamespace("BiocManager", quietly=TRUE))
        install.packages("BiocManager")
    BiocManager::install("GDCRNATools")

官网有个教程见[GDCRNATools: integrative analysis of protein coding genes, long non-coding genes, and microRNAs in GDC (bioconductor.org)](http://bioconductor.org/packages/devel/bioc/vignettes/GDCRNATools/inst/doc/GDCRNATools.html)，里面有一些示例，大家可以自己试一下。

## XENA网站获得数据

[Xena](https://xena.ucsc.edu/)是UCSC开发的一款网页浏览器数据库，几乎包含了目前生物信息学领域所有的权威数据库，数据相当齐全，也非常强大。不过严格来说，Xena网站只是一个超大型的数据集合平台，通过它获得的数据并不是实时的数据，但是这个数据库也时常更新，而且也很受权威专家们的认可，所以并不会有多少影响。

xena官网是在国外，所以有时候也还会有网络不稳定的情况，然而，国内总是有无私奉献的一群人为人民服务中，目前国内的镜像站已经授权给了hiplot平台。所以通过访问[xena.hiplot.com.cn](https://xena.hiplot.com.cn/)可以很快的下载到你所需要的数据。

## UCSCxenashiny获得数据

UCSCxenashiny其实是对xena数据的爬取，所以xena上有的数据，它都能下载到，而且shiny本身就是交互式平台，我们也可以通过点鼠标就可以获得数据和可视化结果。

``` r
## 目前UCSCXenaShiny已经收录在CRAN上，所以你可以直接安装稳定版
install.packages("UCSCXenaShiny")
## 也可以通过GitHub安装开发版
remotes::install_github("openbiox/UCSCXenaShiny")
## 对于GitHub访问困难的用户，也可以通过Gitee来安装开发版
remotes::install_git("https://gitee.com/XenaShiny/UCSCXenaShiny")
```

而且UCSCxenashiny这个包也是hiplot平台所研发的，直接通过R语言运行的话，会提示你安装非常多的依赖包，如果你想更进一步偷懒的话，其实你只需要访问他的shiny网站（<https://hiplot.com.cn/advance/ucsc-xena-shiny>）就可以了,功能从普通的肿瘤与正常组织的差异、生存分析到复杂的免疫分析都有，数据也能下载。

## DrBioRight人工智能获得数据

[DrBioRight](https://drbioright.org/landing/about/)是一款给予交互式文字录入获得数据和可视化结果的平台，收录了TCGA、CCLE等数据库。说是人工智能，其实目前也只是通过输入关键词得到结果而已，还没有到深度算法学习的那种地步，我个人觉得其实就是一个噱头，但奈何人家这个数据库还发了*Cancer Cell* ，影响因子高达31分。。。。

## 其他可视化工具

目前可以获得TCGA数据的还有很多很多工具，比如北京大学开发的[GEPIA](http://gepia.cancer-pku.cn/)和[GEPIA2](http://gepia2.cancer-pku.cn/)，用来做变异分析的有[cBioPortal](http://www.cbioportal.org/)，还有很多衍生的数据库平台，有做免疫分析的[TIMER](http://timer.cistrome.org/)，有做生存分析的[KMplotter](https://kmplot.com/analysis/)，还有做蛋白的、做甲基化的、做m6A、LncRNA。。。还有国内外很多大学自己开发的基于TCGA的工具。

目前纯生信的文章已经很难发文章了，但是还可以开发一些数据平台或者算法，其实这么多工具基本都是国人研发的，以后是不是专门搞个国人生信平台呢。
