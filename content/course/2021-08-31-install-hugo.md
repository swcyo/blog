---
title: 如何安装hugo？
author: 欧阳松
date: '2021-08-31'
slug: install-hugo
categories:
  - 教程
  - R
  - hugo
tags:
  - 教程
  - hugo
---

如果在RStudio中使用**blogdown**搭建个人博客，除了安装**blogdown**以外，还有个重要的步骤就是安装[Hugo](https://github.com/gohugoio/hugo)（仅在RStudio中安装，不需要额外安装），如果你能连接Github，一切都好说。如果你安装不上去，一切都白搭。。。

## 可正常访问GitHub

```
blogdown:install_hugo(version = "latest")
```

## 无法访问GitHub

无法访问GitHub的时候，只能安装镜像版或者本地版，这个时候我们需要知道Hugo的版本和安装

### 镜像安装
```
blogdown:install_hugo(version = "https://mirror.ghproxy.com/https://github.com/gohugoio/hugo/releases/download/v0.121.1/hugo_0.121.1_darwin-universal.tar.gz")
```

### 本地安装
如果你是windows系统，可以首先下载zip或tar.gz文件，然后解压，比如D盘中，输入代码
```
blogdown:::install_hugo_bin("D:/hugo.exe")
```


