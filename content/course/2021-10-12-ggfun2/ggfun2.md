---
title: ggfun的其他功能
author: 欧阳松
date: '2021-10-12'
slug: ggfun2
categories:
  - ggplot2
  - ggfun
tags:
  - ggplot2
enableToc: no
from_Rmd: yes
---

前面写了[使用ggfun给ggplot2添加一个圆润的方框](/course/ggfun/)，不过**ggfun**这个包除了添加圆润的外框外，还有一些别的功能，而且在`User guides`里是有教程示例的，

要注意的是Github上其实也有个ggfun，但是不是Y叔开发的ggfun，Y叔的ggfun已经收录在CRAN上，所以是可以直接安装的，截止本文时间，最新的版本是0.0.4

    install.packages("ggfun")

教程上一共提供了四个方面的功能：

-   element_roundrect
-   gglegend
-   set_font
-   facet_set

element_roundrect我们已经试过了，我们可以把剩下三个按照教程跑一跑

## gglegend


```r
library(grid)
library(ggplot2)
library(ggfun)
p <- ggplot(mtcars, aes(mpg, disp)) + geom_point()
data <- data.frame(colour = c("red", "blue"), VALUE = c("A", "B"))
gglegend(aes(colour = VALUE, label = VALUE), data, geom_text, p)
```

![plot of chunk unnamed-chunk-1](/figures/course/2021-10-12-ggfun2/ggfun2/unnamed-chunk-1-1.png)

## set_font


```r
d <- data.frame(x = rnorm(10), y = rnorm(10), lab = LETTERS[1:10])
p <- ggplot(d, aes(x, y)) + geom_text(aes(label = lab), size = 5)
set_font(p, family = "Times", fontface = "italic", color = "firebrick")
```

![plot of chunk unnamed-chunk-2](/figures/course/2021-10-12-ggfun2/ggfun2/unnamed-chunk-2-1.png)

## facet_set

手动修改分面标签


```r
library(ggplot2)
library(ggfun)
p <- ggplot(mtcars, aes(disp, drat)) + geom_point() + facet_grid(cols = vars(am),
  rows = vars(cyl))
p + facet_set(label = c(`0` = "Zero", `6` = "Six"))
```

![plot of chunk unnamed-chunk-3](/figures/course/2021-10-12-ggfun2/ggfun2/unnamed-chunk-3-1.png)

支持贴标签


```r
p + facet_set(label = label_both)
```

![plot of chunk unnamed-chunk-4](/figures/course/2021-10-12-ggfun2/ggfun2/unnamed-chunk-4-1.png)

给图片添加一个分面标签


```r
p + facet_set(label = "TEST")
```

![plot of chunk unnamed-chunk-5](/figures/course/2021-10-12-ggfun2/ggfun2/unnamed-chunk-5-1.png)

通过联合ggplotify，我们可以使用`facet_set`函数向R中的几乎任何绘图添加facet标签。


```r
ggplotify::as.ggplot(~barplot(1:10, col = rainbow(10))) + facet_set("a barplot in base")
```

![plot of chunk unnamed-chunk-6](/figures/course/2021-10-12-ggfun2/ggfun2/unnamed-chunk-6-1.png)
