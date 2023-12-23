---
title: R语言复现我的硕士论文
author: 欧阳松
date: '2021-08-23'
slug: mymasterthesis
categories:
  - R
  - 教程
  - bookdown
tags:
  - R
  - R Markdown
  - bookdown
---
最近在学习bookdown，顺便复现了我的硕士论文，网址是<https://swcyo.github.io/masterthesis/>

这个完全是用R生成的，安装好`bookdown`以后，新建一个Project，类型选择Book project using bookdown

写书的原理是一个一个的Rmd文件，首页是index，然后按00-，01-，02-，03-这样新建或重命名Rmd文档，默认按数字的顺序拼接文章。最终文章目录的生成取决与标题级数。

目前1.4版的RStudio合成了markdown可视化，可以直接点击选择用几级的标题，一般Heading1就是章数，然后逐级设置，注意的是完全不用自己写序号，序号都是自动生成的，写完以后点击**Knit**就可以渲染，这里要注意如果只是预览效果，点击一次Knit生成的只是你写的那一章，除非你写完每一章都重新Knit，否则不是完整的文章，有一个Bulid的选项可以生成电子书，也有很简单的代码实现

> bookdown::render_book("你的项目.Rproj")
