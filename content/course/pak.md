---
title: pak包：一条指令解决包的安装问题，支持CRAN，Bioconductor和Github，还支持自动更新
author: 欧阳松
date: '2022-11-28'
slug: pak
categories:
  - pak
  - 安装
tags:
  - pak
---

R语言有很多强大的包，随着各种包的开发，许多包之间相互依赖，R包的主要来源CRAN、Bioconductor和Github，但是不同来源的安装指令却不一样，再比如Github上的包，有时候随着R包的更新，每次还要重新下载安装，这些都是困扰我们安装的问题。  

> 我们知道，一般有三种R包的来源，不同来源的安装方案不一样：

- CRAN：官网的包，可以再Rstudio中直接搜索安装，也可以使用`install.packages("rstatix")` 安装

- Bioconductor：主要是生物信息学的包，需要使用`BiocManager::install("limma")` 安装

- Github：需要使用devtools或者remotes安装，如`devtools::install_github('lchiffon/REmap')`

三个来源的包，就需要三种不同的代码安装，而且还不能自动更新，就算更新了也不是只更新有变化的部分，而有了**pak**这个包，只要一条指令就可以完成所有的工作。

## 首先安装pak

    # 普通版
    install.packages("pak")
    #或者开发版
    install.packages("pak", repos = "https://r-lib.github.io/p/pak/dev/")

## 一条指令安装

可以用`pak::pkg_install("tibble")`进行安装，实际上只需要`pak("tibble")`一条指令即可。引号里面的就是需要的包名，**不需要区分上CRAN还是Bioconductor**，只需要写包名即可，它是自动搜索的。  
比如我们安装著名的**clusterProfiler**，只需要一句代码，剩下的直接等待就好了，所有的依赖包都会自动下载安装，而且安装界面很有感觉。

- 为了速度更好，建议提前设置镜像和Git环境。

<!-- -->

    options(BioC_mirror="https://mirrors.tuna.tsinghua.edu.cn/bioconductor")

    pak::pak('clusterProfiler') 

    # 也可以使用 
    pak::pkg_install("clusterProfiler")

而对于Github的包，也只需要一条指令，引号里只需要将用户名和仓库名用斜线分开即可，比如`ggstatplot`的开发版

    pak::pak("IndrajeetPatil/ggstatsplot")

而且Github支持自动更新，也不需要重新下载安装，只安装更新的部分。  
另外pak还支持本地安装，更多功能等你发现。
