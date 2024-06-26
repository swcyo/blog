---
title: Github的包要怎么装
author: 欧阳松
date: '2021-09-03'
slug: githubpackage
categories:
  - 教程
  - R
tags:
  - Github
from_Rmd: yes
---

有很多好用的R包第一时间都不是发布在CRAN或者BiocManager，而是放在Github上，尤其是很多开发版的包，可以说是第一手资源。可是，很多时候由于网络限制，可能我们并不能在不科学上网的情况下成功安装，那么就有这几种情况

## 可以访问Github

这种情况下很好办，可以用devtools或者remotes安装，先安装这两个包，然后直接`install_github(用户名/仓库名)`

    # install.packages('devtools') #没有安装的，把第一个#去掉安装
    # install.packages('remotes')#没有安装的，把第一个#去掉安装
    # devtools::install_github('xjsun1221/tinyarray) # 两种方法任选其一
    remotes::install_github('xjsun1221/tinyarray)

## 不可以访问Github

### 镜像安装

如果打开不了github.com，可以用镜像源替代，比如<https://hub.fastgit.org/>代替<https://github.com>然后安装`install_git()`

    remotes::install_git('https://hub.fastgit.org/xjsun1221/tinyarray')

### 本地安装

如果下载了源码，以master.zip结尾的，那么可以这样安装

    remotes::install_local("tinyarray-master.zip",upgrade = F,dependencies = T)

也可以解压成文件夹了，然后这样安装，把`type="source"`

    install.packages("tinyarray-master",repos=NULL,type="source")

### 码云安装

码云（Gitee.com）可以直接forkGithub的包，导入到自己的码云，一定要选公开，然后可以这样：

    remotes::install_git('https://gitee.com/swcyo/tinyarray')

---

最后祝你成功。。。。
