---
title: 使用TCGAbiolinks下载转录组数据，含tpm
author: 欧阳松
date: '2023-12-26'
slug: tcgabiolinks-tpm
categories:
  - TCGAbiolinks
tags:
  - TCGAbiolinks
---

最近临床事情多，代码临时写的，比较粗糙，适合有一定基础的同学！
## 安装和加载包
```
## 设置镜像
options(BioC_mirror="https://mirrors.tuna.tsinghua.edu.cn/bioconductor")
if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install("remotes",force = TRUE)
BiocManager::install("BioinformaticsFMRP/TCGAbiolinksGUI.data",force = T)
BiocManager::install("BioinformaticsFMRP/TCGAbiolinks",force = T)
BiocManager::install("SummarizedExperiment",force = TRUE)
## 加载包
library(SummarizedExperiment)
library(TCGAbiolinks)
## 设置工作目录
setwd('F:\\TCGA\\BLCA') 
```
## TCGAbiolink的说明
 使用TCGAbiolink这个包下载，分三个步骤：查询，下载，整理
### 查询

比如我们查询**膀胱癌**的数据
```
query <- GDCquery(project = "TCGA-BLCA",
                  data.category = "Transcriptome Profiling",
                  data.type = "Gene Expression Quantification",
                  workflow.type = "STAR - Counts",
                  legacy = FALSE)
```
### 获取TCGA中各种癌症的project
```
projects <- TCGAbiolinks::getGDCprojects()$project_id
projects <- projects[grepl('^TCGA', projects, perl=TRUE)]
```

### 下载数据
将文件下载到工作目录中，并在目录下创建GDCdata的文件夹，下次使用时，不再需要运行该句代码，可重复下载
```
GDCdownload(query = query)
```

### 整理数据
将下载好的文件整理成SE格式
```
SE=GDCprepare(query = query)
```
保存该数据，以后可以直接加载使用
```
save(SE,file = 'TCGA_BLCA_SE.Rdata')
load('TCGA_BLCA_SE.Rdata')
## 相应的数据信息
names(SE@assays)    ###assays包含的是打包的各种数据类型的基因表达量的信息
names(SE@rowRanges) ###rowRanges包含基因注释信息
```
## 相关数据的提取

### 获取我们所需要的基因注释信息
```
mydf <- as.data.frame(rowRanges(SE))
```
### 查看基因类型
```
table(mydf$gene_type)
```

### 获取所需的基因表达量的数据类型

获取TPM，FPKM和COUNT结果，分别对应tpm_unstrand，fpkm_unstrand，unstranded（counts）
```
gene_expr <- as.data.frame(assay(SE,i='tpm_unstrand'))
count<- as.data.frame(assay(SE,i='unstranded'))
```

### Ensemble ID转换
```
gene_expr <- cbind(type=mydf$gene_type,ID=mydf$gene_name,gene_expr)
mydata <- gene_expr[,-1]
```
#### 不对基因进行去重

保存全部基因的文件,大样本数据建议保存rdata格式
```
write.table(mydata,'TCGA_BLCA_tpm.txt',sep = '\t',row.names = F,quote = F)
save(mydata,file = 'TCGA_BLCA_tpm.Rdata')
load('TCGA_BLCA_tpm.Rdata')
```

#### 对基因去重复
保留全部基因(只保留基因表达量最大的基因)
```
library(dplyr)
all <- mydata %>%
  dplyr::mutate(newcolum=rowMeans(.[,-1])) %>%
  dplyr::arrange(desc(newcolum)) %>%
  dplyr::distinct(ID,.keep_all = T) %>%
  dplyr::select(-newcolum)
## 数据的保存
write.table(all,'all_TCGA_BLCA_tpm.txt',sep = '\t',row.names = F,quote = F)
save(all,file = 'all_TCGA_BLCA_tpm.Rdata')
```

### 获取mRNA或者lncRNA
```
mRNA <- gene_expr[gene_expr$type=='protein_coding',] ###获取lncRNA只需要把'protein_coding'换成'lncRNA'
mRNA <- mRNA[,-1]
```

#### 不对基因去重
```
write.table(mRNA,'mRNA_TCGA_BLCA_tpm.txt',sep = '\t',row.names = F,quote = F)
save(mRNA,file = 'mRNA_TCGA_BLCA_tpm.Rdata')
```

#### 对基因去重
```
d_mRNA <- mRNA %>%
  dplyr::mutate(newcolum=rowMeans(.[,-1])) %>%
  dplyr::arrange(desc(newcolum)) %>%
  dplyr::distinct(ID,.keep_all = T) %>%
  dplyr::select(-newcolum)
write.table(d_mRNA,'d_mRNA_TCGA_BLCA_tpm.txt',sep = '\t',row.names = F,quote = F)
save(d_mRNA,file = 'd_mRNA_TCGA_BLCA_tpm.Rdata')
```

### 对正常样本和肿瘤样本进行排序

方便后面分析
#### 以去重的d_mRNA为列子
```
rownames(d_mRNA) <- d_mRNA[,1]
Tumor <- substr(colnames(d_mRNA),14,16)=='01A'
Tumor_mRNA <- d_mRNA[,Tumor]

Normal <- substr(colnames(d_mRNA),14,16)=='11A'
Normal_mRNA <- d_mRNA[,Normal]

ncol(Tumor_mRNA )  ##查看肿瘤样本
ncol(Normal_mRNA ) ##查看正常样本

symbol <- cbind(id=d_mRNA$ID,Normal_mRNA ,Tumor_mRNA )
write.table(symbol,'symbol.txt',sep = '\t',row.names = F,quote = F)
```
