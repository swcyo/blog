---
title: 可视化矩阵的相关性分析之corrplot
author: 欧阳松
date: '2021-10-17'
slug: corrplot
categories:
  - corrplot
tags:
  - 相关性分析
hideToc: yes
from_Rmd: yes
---

目前有**corrplot**、**ggcorrplot**和**GGally**这三个包可以用来可视化矩阵的相关性分析，而这三个包都已经被CRAN收录，安装都很简单，所以可以先直接输入安装好。

    install.packages("corrplot")
    install.packages("ggcorrplot")
    install.packages("GGally")

我准备在别人的基础上复现一下这三个包的相关参数，使用Rmarkdown进行渲染，数据都是使用自带的`mtcars`进行分析。

本节首先介绍最基础的**corrplot**进行操作，首先一键清空，导入数据，加载**corrplot**

    rm(list = ls())
    library(corrplot)
    data(mtcars)
    mtcars


|                    |  mpg| cyl|  disp|  hp| drat|    wt|  qsec| vs| am| gear| carb|
|:-------------------|----:|---:|-----:|---:|----:|-----:|-----:|--:|--:|----:|----:|
|Mazda RX4           | 21.0|   6| 160.0| 110| 3.90| 2.620| 16.46|  0|  1|    4|    4|
|Mazda RX4 Wag       | 21.0|   6| 160.0| 110| 3.90| 2.875| 17.02|  0|  1|    4|    4|
|Datsun 710          | 22.8|   4| 108.0|  93| 3.85| 2.320| 18.61|  1|  1|    4|    1|
|Hornet 4 Drive      | 21.4|   6| 258.0| 110| 3.08| 3.215| 19.44|  1|  0|    3|    1|
|Hornet Sportabout   | 18.7|   8| 360.0| 175| 3.15| 3.440| 17.02|  0|  0|    3|    2|
|Valiant             | 18.1|   6| 225.0| 105| 2.76| 3.460| 20.22|  1|  0|    3|    1|
|Duster 360          | 14.3|   8| 360.0| 245| 3.21| 3.570| 15.84|  0|  0|    3|    4|
|Merc 240D           | 24.4|   4| 146.7|  62| 3.69| 3.190| 20.00|  1|  0|    4|    2|
|Merc 230            | 22.8|   4| 140.8|  95| 3.92| 3.150| 22.90|  1|  0|    4|    2|
|Merc 280            | 19.2|   6| 167.6| 123| 3.92| 3.440| 18.30|  1|  0|    4|    4|
|Merc 280C           | 17.8|   6| 167.6| 123| 3.92| 3.440| 18.90|  1|  0|    4|    4|
|Merc 450SE          | 16.4|   8| 275.8| 180| 3.07| 4.070| 17.40|  0|  0|    3|    3|
|Merc 450SL          | 17.3|   8| 275.8| 180| 3.07| 3.730| 17.60|  0|  0|    3|    3|
|Merc 450SLC         | 15.2|   8| 275.8| 180| 3.07| 3.780| 18.00|  0|  0|    3|    3|
|Cadillac Fleetwood  | 10.4|   8| 472.0| 205| 2.93| 5.250| 17.98|  0|  0|    3|    4|
|Lincoln Continental | 10.4|   8| 460.0| 215| 3.00| 5.424| 17.82|  0|  0|    3|    4|
|Chrysler Imperial   | 14.7|   8| 440.0| 230| 3.23| 5.345| 17.42|  0|  0|    3|    4|
|Fiat 128            | 32.4|   4|  78.7|  66| 4.08| 2.200| 19.47|  1|  1|    4|    1|
|Honda Civic         | 30.4|   4|  75.7|  52| 4.93| 1.615| 18.52|  1|  1|    4|    2|
|Toyota Corolla      | 33.9|   4|  71.1|  65| 4.22| 1.835| 19.90|  1|  1|    4|    1|
|Toyota Corona       | 21.5|   4| 120.1|  97| 3.70| 2.465| 20.01|  1|  0|    3|    1|
|Dodge Challenger    | 15.5|   8| 318.0| 150| 2.76| 3.520| 16.87|  0|  0|    3|    2|
|AMC Javelin         | 15.2|   8| 304.0| 150| 3.15| 3.435| 17.30|  0|  0|    3|    2|
|Camaro Z28          | 13.3|   8| 350.0| 245| 3.73| 3.840| 15.41|  0|  0|    3|    4|
|Pontiac Firebird    | 19.2|   8| 400.0| 175| 3.08| 3.845| 17.05|  0|  0|    3|    2|
|Fiat X1-9           | 27.3|   4|  79.0|  66| 4.08| 1.935| 18.90|  1|  1|    4|    1|
|Porsche 914-2       | 26.0|   4| 120.3|  91| 4.43| 2.140| 16.70|  0|  1|    5|    2|
|Lotus Europa        | 30.4|   4|  95.1| 113| 3.77| 1.513| 16.90|  1|  1|    5|    2|
|Ford Pantera L      | 15.8|   8| 351.0| 264| 4.22| 3.170| 14.50|  0|  1|    5|    4|
|Ferrari Dino        | 19.7|   6| 145.0| 175| 3.62| 2.770| 15.50|  0|  1|    5|    6|
|Maserati Bora       | 15.0|   8| 301.0| 335| 3.54| 3.570| 14.60|  0|  1|    5|    8|
|Volvo 142E          | 21.4|   4| 121.0| 109| 4.11| 2.780| 18.60|  1|  1|    4|    2|

## 计算相关性系数

通过自带的`cor()`函数可以很快的计算各矩阵参数直接的相关性系数，如下所示：

    M <- cor(mtcars)  # 相关性分析
    round(M,3) # 只显示小数点后三位数


|     |    mpg|    cyl|   disp|     hp|   drat|     wt|   qsec|     vs|     am|   gear|   carb|
|:----|------:|------:|------:|------:|------:|------:|------:|------:|------:|------:|------:|
|mpg  |  1.000| -0.852| -0.848| -0.776|  0.681| -0.868|  0.419|  0.664|  0.600|  0.480| -0.551|
|cyl  | -0.852|  1.000|  0.902|  0.832| -0.700|  0.782| -0.591| -0.811| -0.523| -0.493|  0.527|
|disp | -0.848|  0.902|  1.000|  0.791| -0.710|  0.888| -0.434| -0.710| -0.591| -0.556|  0.395|
|hp   | -0.776|  0.832|  0.791|  1.000| -0.449|  0.659| -0.708| -0.723| -0.243| -0.126|  0.750|
|drat |  0.681| -0.700| -0.710| -0.449|  1.000| -0.712|  0.091|  0.440|  0.713|  0.700| -0.091|
|wt   | -0.868|  0.782|  0.888|  0.659| -0.712|  1.000| -0.175| -0.555| -0.692| -0.583|  0.428|
|qsec |  0.419| -0.591| -0.434| -0.708|  0.091| -0.175|  1.000|  0.745| -0.230| -0.213| -0.656|
|vs   |  0.664| -0.811| -0.710| -0.723|  0.440| -0.555|  0.745|  1.000|  0.168|  0.206| -0.570|
|am   |  0.600| -0.523| -0.591| -0.243|  0.713| -0.692| -0.230|  0.168|  1.000|  0.794|  0.058|
|gear |  0.480| -0.493| -0.556| -0.126|  0.700| -0.583| -0.213|  0.206|  0.794|  1.000|  0.274|
|carb | -0.551|  0.527|  0.395|  0.750| -0.091|  0.428| -0.656| -0.570|  0.058|  0.274|  1.000|

## 可视化分析

有了这个相关性系数，我们就可以可视化作图了，作图用的是`corrplot()`函数，我们可以先看一下这个包的基本参数

    corrplot(corr, method = c("circle", "square", "ellipse", "number", "shade", "color", "pie"), 
             type = c("full", "lower", "upper"), add = FALSE, col = NULL, 
             bg = "white", title = "", is.corr = TRUE, diag = TRUE,
             outline = FALSE, mar = c(0, 0, 0, 0), addgrid.col = NULL,
             addCoef.col = NULL, addCoefasPercent = FALSE, 
             order = c("original","AOE", "FPC", "hclust", "alphabet"), 
             hclust.method = c("complete", "ward", "ward.D", "ward.D2", 
                              "single", "average", "mcquitty", "median", "centroid"),
             addrect = NULL, rect.col = "black", rect.lwd = 2, tl.pos = NULL,
             tl.cex = 1, tl.col = "red", tl.offset = 0.4, tl.srt = 90,
             cl.pos = NULL, cl.lim = NULL, cl.length = NULL, cl.cex = 0.8,
             cl.ratio = 0.15, cl.align.text = "c", cl.offset = 0.5, number.cex = 1,
             number.font = 2, number.digits = NULL, 
             addshade = c("negative", "positive", "all"), shade.lwd = 1, shade.col = "white", p.mat = NULL,
             sig.level = 0.05, insig = c("pch", "p-value", "blank", "n", "label_sig"),
             pch = 4, pch.col = "black", pch.cex = 3, plotCI = c("n", "square", "circle", "rect"), 
             lowCI.mat = NULL, uppCI.mat = NULL, na.label = "?",
             na.label.col = "black", win.asp = 1, ...)

在CSDN博主「R语言中文社区」的原创文章[**corrplot包与ggcorrplot相关图（一）**](https://blog.csdn.net/kMD8d5R/article/details/89346052)中有如下解释：

> corr, 需要可视化的相关系数矩阵，
>
> method, 指定可视化的形状，可以是circle圆形(默认)，square方形，
>
> ellipse, 椭圆形，number数值，shade阴影，color颜色，pie饼图。
>
> type，指定显示范围，可以是full完全(默认)，lower下三角，upper上三角。
>
> col, 指定图形展示的颜色，默认以均匀的颜色展示。
>
> 支持grDevices包中的调色板，也支持RColorBrewer包中调色板。
>
> bg, 指定背景颜色。
>
> add, 表示是否添加到已经存在的plot中。默认FALSE生成新plot。
>
> title, 指定标题，
>
> is.corr，是否为相关系数绘图，默认为TRUE,FALSE则可将其它数字矩阵进行可视化。
>
> diag, 是否展示对角线上的结果，默认为TRUE，
>
> outline, 是否添加圆形、方形或椭圆形的外边框，默认为FALSE。
>
> mar， 设置图形的四边间距。数字分别对应(bottom, left, top, right)。
>
> addgrid.col, 设置网格线颜色，当指定method参数为color或shade时， 默认的网格线颜色为白色，其它method则默认为灰色，也可以自定义颜色。
>
> addCoef.col， 设置相关系数值的颜色，只有当method不是number时才有效。
>
> addCoefasPercent, 是否将相关系数转化为百分比形式，以节省空间，默认为FALSE。
>
> order, 指定相关系数排序的方法, 可以是original原始顺序，AOE特征向量角序，
>
> FPC第一主成分顺序，hclust层次聚类顺序，alphabet字母顺序。
>
> hclust.method, 指定hclust中细分的方法，只有当指定order参数为hclust时有效，
>
> 有7种可选：complete, ward, single, average, mcquitty, median, centroid。
>
> addrect， 是否添加矩形框，只有当指定order参数为hclust时有效， 默认不添加， 用整数指定即可添加。
>
> rect.col, 指定矩形框的颜色。
>
> rect.lwd, 指定矩形框的线宽。
>
> tl.pos, 指定文本标签(变量名称)相对绘图区域的位置，为"lt"(左侧和顶部),
>
> "ld"(左侧和对角线), "td"(顶部和对角线),"d"(对角线),"n"(无)之一。
>
> 当type="full"时,默认"lt"。
>
> 当type="lower"时，默认"ld"。
>
> 当type="upper"时，默认"td"。
>
> tl.cex, 设置文本标签的大小。
>
> tl.col, 设置文本标签的颜色。
>
> cl.pos, 设置图例位置，为"r"(右边), "b"(底部),"n"(无)之一。
>
> 当type="full"/"upper"时，默认"r"; 当type="lower"时，默认"b"。
>
> addshade, 表示给增加阴影，只有当method="shade"时有效。
>
> 为"negative"(对负相关系数增加阴影),负相关系数的阴影是135度；
>
> "positive"(对正相关系数增加阴影), 正相关系数的阴影是45度；
>
> "all"(对所有相关系数增加阴影)，之一。
>
> shade.lwd, 指定阴影线宽。
>
> shade.col, 指定阴影线的颜色。

---

而在帮助文档里有一个比较详细的介绍，我这里不做介绍，大家可以自己去看，我先按介绍的基本参数跑一下：

-   可视化的形状有七种，默认是circle，我们可以进行修改


```r
corrplot(M)  # 默认是circle，即method = 'circle'
```

![plot of chunk unnamed-chunk-3](/figures/course/2021-10-17-corrplot/corrplot/unnamed-chunk-3-1.png)


```r
corrplot(M, method = "number")  # 按数字大小绘制颜色
```

![plot of chunk unnamed-chunk-4](/figures/course/2021-10-17-corrplot/corrplot/unnamed-chunk-4-1.png)


```r
corrplot(M, method = "color", order = "alphabet")  # 按颜色绘制，排序
```

![plot of chunk unnamed-chunk-5](/figures/course/2021-10-17-corrplot/corrplot/unnamed-chunk-5-1.png)


```r
corrplot(M, order = "AOE")  # 按'AOE'排序
```

![plot of chunk unnamed-chunk-6](/figures/course/2021-10-17-corrplot/corrplot/unnamed-chunk-6-1.png)


```r
corrplot(M, method = "shade", order = "AOE", diag = FALSE)  #换个形状
```

![plot of chunk unnamed-chunk-7](/figures/course/2021-10-17-corrplot/corrplot/unnamed-chunk-7-1.png)


```r
corrplot(M, method = "square", order = "FPC", type = "lower", diag = FALSE)
```

![plot of chunk unnamed-chunk-8](/figures/course/2021-10-17-corrplot/corrplot/unnamed-chunk-8-1.png)


```r
corrplot(M, method = "ellipse", order = "AOE", type = "upper")
```

![plot of chunk unnamed-chunk-9](/figures/course/2021-10-17-corrplot/corrplot/unnamed-chunk-9-1.png)


```r
corrplot.mixed(M, order = "AOE")
```

![plot of chunk unnamed-chunk-10](/figures/course/2021-10-17-corrplot/corrplot/unnamed-chunk-10-1.png)


```r
corrplot.mixed(M, lower = "shade", upper = "pie", order = "hclust")
```

![plot of chunk unnamed-chunk-11](/figures/course/2021-10-17-corrplot/corrplot/unnamed-chunk-11-1.png)

还可以分镞排序


```r
corrplot(M, order = "hclust", addrect = 2)
```

![plot of chunk unnamed-chunk-12](/figures/course/2021-10-17-corrplot/corrplot/unnamed-chunk-12-1.png)

---

简书上还有一篇文章[使用corrplot包绘制相关性图 - 简书 (jianshu.com)](https://www.jianshu.com/p/00000f6f32df)，也挺好，大家可以试着跑跑。
