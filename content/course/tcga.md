---
title: 如何只下载TCGA肿瘤或正常标本的count
author: 欧阳松
date: '2021-07-01'
slug: 如何只下载tcga肿瘤或正常标本的count
categories:
  - 教程
  - R
  - TCGA
tags:
  - R
  - Tutorial
  - TCGA
---

其实有很多教程指导怎么下载count，最简单当然还有`xena`，但是我们知道TCGA的肿瘤样本中包括了肿瘤了正常样本，而且有些样本还是重复的，所以需要去重，还需要把肿瘤和正常过滤，现在推荐一下我自己摸索的一个办法\
使用的是`GDCRNATools`，这个包可以下载RNA、miRNA和lncRNA，数据也是count，有人说能不能下载FPKM或者TPM，其实count才是最原始的，自己找一个包转换成TPM不就好了吗

#### **第一步，下载所有的count**

    ### GDCRNATools是Bioconductor的包,所以先安装
    if (!requireNamespace("BiocManager", quietly = TRUE))
        install.packages("BiocManager")
    BiocManager::install("GDCRNATools")
    #### 
    #加载GDCRNATools
    library(GDCRNATools)
    #比如我要下载膀胱癌的数据，项目是TCGA-BLCA
    gdcRNADownload(project.id     = 'TCGA-BLCA', #参数项目id
                   data.type      = 'RNAseq', #也可改为miRNAs
                   directory      = 'GDCRNATOOLS/TCGA-BLCA/RNAseq') #自己要保存的路径，当然也可以自己提前设置号

这一步完成了以后，自己保存的路径会生成几个包括`gdc-client`的文件，其实就是调用这个去下载，最终生成一个`Data`的文件夹，里面可能有几百个子文件夹，每一个文件夹里面就是一个样本，下载完全取决于你的网速。

#### **第二步，数据预处理**

    ### 首先用gdcParseMetadata（）函数构建一个meta的表格
    meta<- gdcParseMetadata(project.id = 'TCGA-BLCA',
                                       data.type  = 'RNAseq', 
                                       write.meta = F)
    ### 可以看到有433个样本
    ### 然后就是过滤样本，gdcFilterDuplicate函数过滤重复的样本、gdcFilterSampleType函数过滤非肿瘤非正常组织
    meta<- gdcFilterDuplicate(meta)  # Removed 6 samples，去掉了6个
    meta <- gdcFilterSampleType(meta)

然后可以发现gdcFilterDuplicate函数可以过滤掉重复的样本，但是gdcFilterSampleType函数却没有过滤，所以关键就是这一步了，因为后续融合数据其实就是要调用这个meta，然后我就想到了一个最简单的办法\
可以看到meta里面有个sample_type，下面只有`PrimaryTumor`和`SolidTissueNormal`，这样我们可以使用subset(函数)来过滤了

    tumor<-subset(meta,sample_type!="SolidTissueNormal")
    normal<-subset(meta,sample_type!="PrimaryTumor")

#### **第三步，融合数据，gdcRNAMerge函数**

    #### 合并 RNAseq数据
    ### 所有的样本count
    count <- gdcRNAMerge(metadata  = meta, 
                             path      = 'TCGA-BLCA/RNAseq/', 
                             data.type = 'RNAseq')
    ### 肿瘤样本count
    tumor_count <- gdcRNAMerge(metadata  = tumor, 
                             path      = 'TCGA-BLCA/RNAseq/', 
                             data.type = 'RNAseq')
    ### 正常样本count
    normal_count <- gdcRNAMerge(metadata  = normal, 
                             path      = 'TCGA-BLCA/RNAseq/', 
                             data.type = 'RNAseq')

然后write.csv()保存就OK了
