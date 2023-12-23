---
title: 如何设置CRAN和Bioconductor的镜像源
author: 欧阳松
date: '2021-09-03'
slug: how_to_set_mirror
categories:
  - 教程
  - R
tags:
  - 镜像源
---

我们在安装R包的时候，如果不是本地安装的话，需要调用网络，而目前的服务器基本都是在国外，所以经常会出现网络不稳定的情况，这个时候我们就需要设置镜像源，目前基本都有国内的镜像，所以每次安装包的时候最好先设置一下镜像源。

安装的过程中，会提示安装依赖的包，基本都是无脑安装，但是安装前还是建议先设置一下镜像，CRAN的可以在RStudio直接设置，而BiocManager的话，建议先输入以下代码选择一个镜像。

    options(BioC_mirror="https://mirrors.tuna.tsinghua.edu.cn/bioconductor")
    # options(BioC_mirror="https://mirrors.nju.edu.cn/bioconductor/") #南京镜像

或者下面的代码一次性设置两个

    local({
        r <- getOption("repos")
        r["CRAN"] <- "http://mirror.lzu.edu.cn/CRAN" #兰州大学镜像
        options(repos = r, BioC_mirror="https://mirrors.tuna.tsinghua.edu.cn/bioconductor")
    })

------------------------------------------------------------------------

CRAN镜像源：

China

:   |                                              |                     |
    |----------------------------------------------|---------------------|
    | 地址                                         | 学校                |
    | <https://mirrors.tuna.tsinghua.edu.cn/CRAN/> | 清华大学            |
    | <https://mirrors.bfsu.edu.cn/CRAN/>          | 北京外国语学院      |
    | <https://mirrors.ustc.edu.cn/CRAN/>          | 中国科学技术大学    |
    | <https://mirror-hk.koddos.net/CRAN/>         | KoDDoS in Hong Kong |
    | <https://mirrors.e-ducation.cn/CRAN/>        | Elite Education     |
    | <https://mirror.lzu.edu.cn/CRAN/>            | 兰州大学            |
    | <https://mirrors.nju.edu.cn/CRAN/>           | 南京大学            |
    | <https://mirrors.tongji.edu.cn/CRAN/>        | 同济大学            |
    | <https://mirrors.sjtug.sjtu.edu.cn/cran/>    | 上海交通大学        |
    | <https://mirrors.sustech.edu.cn/CRAN/>       | 南方科技大学        |

------------------------------------------------------------------------

bioconductor镜像源

| City           | URL                                                 |
|----------------|-----------------------------------------------------|
| 清华大学       | <http://mirrors.tuna.tsinghua.edu.cn/bioconductor/> |
| 南京大学       | <https://mirrors.nju.edu.cn/bioconductor/>          |
| 北京外国语学院 | <https://mirrors.bfsu.edu.cn/bioconductor>          |
