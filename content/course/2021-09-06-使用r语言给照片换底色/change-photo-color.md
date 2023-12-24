---
title: 使用R语言给照片换底色
author: 欧阳松
date: '2021-09-06'
slug: change-photo-color
categories:
  - 教程
  - R
tags:
  - magick
from_Rmd: yes
---

这个教程来自Y叔的[听说你用R把证件照给一键换底了](https://mp.weixin.qq.com/s/ZX4iHCHeGvtfCBl5-v5GVw)

\
首先准备一张证件照（打码照）,比如蓝底图 \@ref(fig:fig)，png或jpg格式的都可以，用**magick**这个包的`image_read()`函数把它读进来，用ggplotify转换成ggplot图片

    library(magick)
    x<-image_read('你的照片.jpg') ## 或者png
    ggplotify::as.ggplot(x)

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-06-使用r语言给照片换底色/change-photo-color/fig-1.png" alt="蓝底图"  />
<p class="caption">蓝底图</p>
</div>

用image_fill()函数直接就可以处理图片，选一个自己想要换的颜色，比如白色，代码一输，`as.ggplot`一转，就成了白底图 \@ref(fig:fig2)。


```r
y <- image_fill(x, "white", fuzz = 20)
ggplotify::as.ggplot(y)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-06-使用r语言给照片换底色/change-photo-color/fig2-1.png" alt="白底图"  />
<p class="caption">白底图</p>
</div>

再试试别的颜色，拼一下图，见图 \@ref(fig:combine)所示。


```r
p1 <- ggplotify::as.ggplot(image_fill(x, "red", fuzz = 20))
p2 <- ggplotify::as.ggplot(image_fill(x, "brown", fuzz = 20))
p3 <- ggplotify::as.ggplot(image_fill(x, "navyblue", fuzz = 20))
p4 <- ggplotify::as.ggplot(image_fill(x, "steelblue", fuzz = 20))
p5 <- ggplotify::as.ggplot(image_fill(x, "green", fuzz = 20))
p6 <- ggplotify::as.ggplot(image_fill(x, "white", fuzz = 20))
p7 <- ggplotify::as.ggplot(x)
p8 <- ggplotify::as.ggplot(image_fill(x, "yellow", fuzz = 20))
p9 <- ggplotify::as.ggplot(image_fill(x, "black", fuzz = 20))
cowplot::plot_grid(p1, p2, p3, p4, p5, p6, p7, p8, p9, ncol = 3, labels = "AUTO")
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-06-使用r语言给照片换底色/change-photo-color/combine-1.png" alt="组合图"  />
<p class="caption">组合图</p>
</div>

---

最后用ggsave保存一下，还可以自己设置照片的长度和宽度，以及dpi

比如，保存为宽2.5cm，高3.5cm、分辨率为150的jpg照片

    ggsave("pic.jpg",width=2.5,heigh=3.5,unit=c('cm'),dpi=150)

---

想试一下吗？
