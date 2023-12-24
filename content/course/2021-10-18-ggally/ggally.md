---
title: 可视化矩阵的相关性分析之GGally
author: 欧阳松
date: '2021-10-18'
slug: GGally
categories:
  - GGally
tags:
  - 相关性分析
from_Rmd: yes
---

学习文档来自[GGally包可视化相关性矩阵的详细教程 (qq.com)](https://mp.weixin.qq.com/s/t2yyCVWAWBG1Urb70FymSw)

**GGally**是一个功能强大的可视化包，号称是[**集相关关系图、箱线图、直方图等于一身的R绘图包**](https://www.jianshu.com/p/ea23666dcc42)**，**而对相关性分析则使用的是 `ggcorr()`函数,最大的好处就是不需要使用cor()函数进行计算，后台直接就计算好了。

比如还是使用mtcars的数据

    rm(list = ls())
    library(GGally)
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

## **绘制简单图形**

**GGally**不需要`cor()`函数可以计算相关性系数，因此可以省去计算步骤，可以一步出图，如下图


```r
ggcorr(mtcars)
```

![plot of chunk unnamed-chunk-2](/figures/course/2021-10-18-ggally/ggally/unnamed-chunk-2-1.png)

## **自定义图形**

### **修改几何对象**

默认图形输出的是方框，可以修改调整为circle，其他可以为tile，text或blank之一。


```r
ggcorr(mtcars, geom = "circle")  # 设置为圆形
```

![plot of chunk unnamed-chunk-3](/figures/course/2021-10-18-ggally/ggally/unnamed-chunk-3-1.png)

### **修改图形颜色**

函数默认低、中、高颜色分别为"\#3B9AB2"、"\#EEEEEE"、"\#F21A00"，可以修改这些色值来设置其他颜色。


```r
ggcorr(mtcars,
       low = "brown", # 对应低颜色
       mid = "gray", # 对应中间颜色
       high = "steelblue") # 对应高颜色
```

![plot of chunk unnamed-chunk-4](/figures/course/2021-10-18-ggally/ggally/unnamed-chunk-4-1.png)

### **图上显示相关系数**

图形上默认是不显示相关系数的，可以在图形上显示相关系数，加个`label=T`即可。


```r
ggcorr(mtcars, label = TRUE)
```

![plot of chunk unnamed-chunk-5](/figures/course/2021-10-18-ggally/ggally/unnamed-chunk-5-1.png)

上面默认显示的相关系数比较拥挤，不够美观，我们可以使用相关参数来修改。

可以调整digits、label_alpha、label_color、label_round、label_size等参数来调整相关系数的文本属性。


```r
ggcorr(mtcars,
       label = TRUE,
       digits = 2, # 设置小数位数
       label_alpha = 0.8, # 设置透明度
       label_color = "steelblue", # 设置文本颜色
       label_size = 1.6)  # 设置文本大小
```

![plot of chunk unnamed-chunk-6](/figures/course/2021-10-18-ggally/ggally/unnamed-chunk-6-1.png)

### **间断相关性系数**

可以将连续性的相关系数划分为几段，转为分段显示。


```r
ggcorr(mtcars,
       nbreaks = 6, # 分为6段
       palette = "PuOr") # 设置调色板
```

![plot of chunk unnamed-chunk-7](/figures/course/2021-10-18-ggally/ggally/unnamed-chunk-7-1.png)

如需要深入学习其他内容可查看[帮助文件](https://briatte.github.io/ggcorr/)哦！,比如这个


```r
ggcorr(mtcars, geom = "blank", label = TRUE, hjust = 0.75) + geom_point(size = 10,
  aes(color = coefficient > 0, alpha = abs(coefficient) > 0.5)) + scale_alpha_manual(values = c(`TRUE` = 0.25,
  `FALSE` = 0)) + guides(color = FALSE, alpha = FALSE)
```

```
## Warning: The `<scale>` argument of `guides()` cannot be `FALSE`. Use "none" instead as
## of ggplot2 3.3.4.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

![plot of chunk unnamed-chunk-8](/figures/course/2021-10-18-ggally/ggally/unnamed-chunk-8-1.png)
