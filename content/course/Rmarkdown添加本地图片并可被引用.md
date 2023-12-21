---
title: Rmarkdown添加本地图片并可被引用
author: 欧阳松
date: '2021-10-11'
slug: [localfigure]
categories: [rmarkdown]
tags: [R Markdown]
---

在Rmarkdown添加本地图片并被引用需要使用的是**knitr**，然后使用	`include_graphics`函数。当然，我们还需要产生一个引用效果，那就需要定义图片的名称和位置等等。

比如实现见图？所示，可以输入见Figure \@ref(fig:figname)所示。

````R
{r figname, echo=FALSE, fig.cap="A caption", out.width = '100%'}
knitr::include_graphics("~/temp.png")
````

解释：

figname是定义的图片名称，这个是链接引用的名字

echo=F，表示不显示运行代码，只显示结果

fig.cap=""，表示图片的标签（位于图片下方）

fig.align='center'，表示图片标签居中

out.width = '100%'，代表图片宽度为100%

记住，fig.cap一定要有。
