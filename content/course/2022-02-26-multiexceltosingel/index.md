---
title: 使用R将多个excel合并成一个excel
author: 欧阳松
date: '2022-02-26'
slug: multiexceltosingel
categories:
  - R
tags:
  - excel
---

在一个文件夹下里面有很多excel文件，它们字段都一样，比如每个月的报表之类，这时候需要把他们合并为一个excel表格。Excel2016版本以上可以通过新建查询实现，但是也很曲折，那么如何通过R将多个Excel进行合并呢？

将多个Excel合并成一个并导出，一般有两种方法：

1.  写循环用`rbind()`函数实现

2.  用现成的包（如**purrr**）实现

------------------------------------------------------------------------

## 写rbind()函数实现合并

### 类型1：R语言合并同一个文件夹下的多个csv文件

``` R
rm(list = ls())  ## 清空数据

#########自定义函数################
readCSV <- function(dir_dta){
  file_list <- list.files(path=dir_dta,full.names=T)
  varSave_func <- function(x){
    table_x <- read.csv(file=x,sep=",",header = T)
  }
  a<-invisible(lapply(file_list,FUN=varSave_func))
  b<-as.data.frame(a[[1]])
  for (i in 2:length(a)){
    c<-rbind(b,a[[i]])
    b <- c
  }
  return(b)
}

##########调用函数
dir_dta <- "C:/Users/Administrator/Desktop/data"

result <-readCSV(dir_dta)
```

### 类型2：R语言合并同一个文件夹下的多个.xlsx文件

#### 对于文件名没有规律的情况

``` R
# 设置工作空间
setwd('D:\\data file\\data2')
# 读取该工作空间下的所有文件名
filenames <- dir()
# 初始化数据框，用于后面的数据合并
data2 <- data.frame()
#通过循环完成数据合并
for (i in filenames){
  # 构造数据路径
  path <- paste0(getwd(),'\\',i)
  #res <- c(res,path)
  # 读取并合并数据
  data2 <- rbind(data2,read_excel(path = path))
}
```

#### 对于文件名没有规律的情况，并且只读取某后缀的文件

(xls,csv,xlsx 后缀都存在，只读取xlsx)

``` R
# 设置工作空间
setwd('D:\\data file\\data3')
# 读取该工作空间下的所有文件名
filenames <- dir()
# 通过正则，获取所有xlsx结尾的文件名
filenames2 <- grep('\\.xlsx', filenames, value = TRUE)
# 初始化数据框，用于后面的数据合并
data3 <- data.frame()
#通过循环完成数据合并
for (i in filenames2){
# 构造数据路径
  path <- paste0(getwd(),'\\',i)
  #res <- c(res,path)
# 读取并合并数据
  data3 <- rbind(data3,read_excel(path = path))
}
```

## 通过R软件包实现合并

### 通过purr包实现

当然首先需要安装**purr**包，导出xlsx的则包是**writexl**

``` R
library(purrr)
library(readxl)
files = list.files("文件夹的路径", pattern = "xlsx", full.names = TRUE)
df = map_dfr(files, read_xlsx)  
writexl::write_xlsx(df,"命名.xlsx")
```

如果还需要插入文件来源的话，则可以这样修改一下

``` r
library(purrr)
library(readxl)
files = list.files("文件夹的路径", pattern = "xlsx", full.names = TRUE)
df = map_dfr(set_names(files), read_xlsx,.id="文件来源")  
writexl::write_xlsx(df,"命名.xlsx")
```

### 使用tidyverse包实现

参考教程[【R语言】批量读取Excel数据并合并（升级版） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/157420835)

载入R包：

``` R
library(tidyverse)
library(readxl)
library(writexl)
```

以xlsx文件为例，如果是xls文件，改用`read_xls()`函数即可，或者不区分，直接用`read_excel()`也行。

将合并后的数据写出到 Excel ：

``` R
write_xlsx(df, "新工作簿.xlsx")          # 写出
```

#### 多个数据表列名相同

这是最常见的情形，读取+按行堆叠合并，即可得到总的数据表。再分两种情况讨论：

##### 数据在多个工作簿（即多个xlsx文件）

最常见，一个excel只有一个sheet

``` R
files = list.files("你的文件夹路径/", pattern = "xlsx", full.names = TRUE)
df = map_dfr(files, read_xlsx)   # 批量读取+按行堆叠合并
```
注1：数据文件名可以随便，不需要规律命名。

注2：数据文件甚至可以嵌套多层文件夹，只需要设置参数 recursive = TRUE.

##### 数据在一个工作簿的多个工作表（1个xlsx文件，包含多个sheet）

``` R
path = "文件.xlsx"
df <- map_dfr(excel_sheets(path), 
              ~ read_xlsx(path, sheet = .x))  # 批量读取+按行堆叠合并  
```
注：同样，各个sheet可以随便起名，不需要规律命名。

#### 多个数据表列名不同

都是同样的批量读取，但合并时不能只是简单的按行堆叠，而是需要做数据连接（join），得到总的数据表。也分两种情况讨论：

##### 数据在多个工作簿（即多个xlsx文件）

比如，datas 文件夹下有3个xlsx文件，3个数据表中的人名是有重复的，所以不能简单按行堆叠

实际上，这是将所有信息都合并到一起，即做全连接。又因为是多表依次做连接，再结合 ``reduce() `函数就能实现。

``` R
files = list.files("datas/", pattern = "xlsx", full.names = TRUE)

df = map(files, read_xlsx) %>% 
  reduce(full_join, by = "人名")                  # 读入并依次做全连接
```

##### 数据在一个工作簿的多个工作表（1个xlsx文件，包含多个sheet）

``` R
  path = "exercise/3-5月业绩.xlsx"
df <- map(excel_sheets(path), 
          ~ read_xlsx(path, sheet = .)) %>% 
  reduce(full_join, by = "人名")              # 读入并依次做全连接
```
