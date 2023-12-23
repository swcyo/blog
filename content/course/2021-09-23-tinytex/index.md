---
title: Tinytex如何本地安装简易latex
author: 欧阳松
date: '2021-09-23'
slug: tinytex
categories:
  - tinytex
tags:
  - tinytex
enableToc: yes
---

**tinytex**是[**yihui**](https://github.com/yihui)开发出来的一款简易Latex工具，不需要安装额外的Latex和包，主要用于rmarkdown的后期渲染效果，比如说你用**bookdown**写了一本电子书，直接knitr后是一本Gitbook的电子书，但是如果你想渲染成pdf或word，就需要使用到Latex，安装**bookdown**是顺带就安装了**tinytex**这个R包，但是并不是说你就已经安装了Tiny Tex了，也就是说**tinytex** ≠ *TinyTeX*，**tinytex**只是个R包，你还需要安装*TinyTeX*框架。

可是就国内这个限制而言，一般很难直接就安装成功，即使有国内镜像，也会经常掉线，所以我们可以选择折中的办法进行本地安装，即第一步就下载需要的文件，然后进行本地安装。

## 安装**tinytex** R包

``` R
install.packages('tinytex')
```

## 安装 TinyTeX框架

### 在线安装办法

``` R
tinytex::install_tinytex()
## trying URL 'https://yihui.org/tinytex/TinyTeX-1.zip'
```

一般这种情况下这种情况经常掉线，或者网速很慢，我们从下面的解释可以看出其实是下载https://yihui.org/tinytex/TinyTeX-1.zip 这个压缩包，所以我们可以先下载好压缩包，然后本地安装。

### 本地安装办法

这里有个官方的列表

|                               | **Windows**                                                      | **macOS**                                                         | **Linux**                                                               |
|-------------------------------|------------------------------------------------------------------|-------------------------------------------------------------------|-------------------------------------------------------------------------|
| TinyTeX-0                     | [TinyTeX-0.zip](https://yihui.org/tinytex/TinyTeX-0.zip) (27 MB) | [TinyTeX-0.tgz](https://yihui.org/tinytex/TinyTeX-0.tgz) (1.4 MB) | [TinyTeX-0.tar.gz](https://yihui.org/tinytex/TinyTeX-0.tar.gz) (670 KB) |
| TinyTeX-1                     | [TinyTeX-1.zip](https://yihui.org/tinytex/TinyTeX-1.zip) (99 MB) | [TinyTeX-1.tgz](https://yihui.org/tinytex/TinyTeX-1.tgz) (83 MB)  | [TinyTeX-1.tar.gz](https://yihui.org/tinytex/TinyTeX-1.tar.gz) (64 MB)  |
| TinyTeX                       | [TinyTeX.zip](https://yihui.org/tinytex/TinyTeX.zip) (200+ MB)   | [TinyTeX.tgz](https://yihui.org/tinytex/TinyTeX.tgz) (188+ MB)    | [TinyTeX.tar.gz](https://yihui.org/tinytex/TinyTeX.tar.gz) (160+ MB)    |
| tinitex binary (experimental) | [tinitex.zip](https://yihui.org/tinytex/tinitex.zip) (24 MB)     | [tinitex.tgz](https://yihui.org/tinytex/tinitex.tgz) (28 MB)      | [tinitex.tar.gz](https://yihui.org/tinytex/tinitex.tar.gz) (31 MB)      |

我们可以看到有TinyTeX-0、TinyTeX-1、TinyTeX和tinitex binary 四种文件，简单解释就是TinyTeX-0是最基础框架，没有包，TinyTeX-1包括了大约90种包，而**TinyTeX**则包括了更多的包（看容量大小也知道）。一般默认的是TinyTeX-1这个框架，但是我倒是建议你安装TinyTeX这个更全的框架。

如果想本地安装，我们先将下载的zip文件保存好，然后使用下列代码即可

``` R
 tinytex:::install_prebuilt('D:\\我的下载\\TinyTeX.zip') ##记得这里是三个:
## 复制了 3 个文件
## 复制了 1 个文件
## Running fc-cache -v -r
```

## 检测是否安装成功

``` R
tinytex::is_tinytex()
```

如果出现\[1\] TRUE，那么恭喜你，安装成功了，这个时候你就可以使用Latex去渲染pdf了
