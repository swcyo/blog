---
title: R爬取民政部官网数据绘制标准中国地图
author: 欧阳松
date: '2024-02-08'
slug: r-map-mzb
categories:
  - 地图
  - 民政部
  - 教程
  - sf
tags:
  - sf
  - 地图
from_Rmd: yes
---

**中华人民共和国**的国土神圣不可侵犯，我国的民政部的全国行政区划唯一数据来源是<http://xzqh.mca.gov.cn/map>

中国民政部官网【<http://xzqh.mca.gov.cn/map】提供了>**省级**与**县级**两种类型的地图，目前审图号为：**GS(2022)1873号**.

可以通过Chrome浏览器查看民政部官网的源代码，发送点击请求，可以发现服务器返回的**json**格式地图数据。

> API前缀都是 <http://xzqh.mca.gov.cn/data/>

-   获取全国省级地图，则加后缀**quanguo.json**;

-   获取全国县级地图，则加后缀**xian_quanguo.json**;

-   获取部分地区，如某个市的县级地图，则加该行政区域代码，再加**.json**;

-   如果要获取市级地图，需要按遍历行政区域代码获取所有市的地图，然后合并县级区域；

-   全国主要山脉，南海九段线数据，则加后缀quanguo_Line.geojson;

注：县级地图数据不包括香港和澳门特别行政区，市级地图数据不包括台湾省。

## 爬取数据

通过爬取数据，可以获得最实时的数据，避免落后。


```r
library(geojsonsf)  ## 直接CRAN 安装
library(sf)  ## 直接CRAN 安装
```

```
## Linking to GEOS 3.11.0, GDAL 3.5.3, PROJ 9.1.0; sf_use_s2() is TRUE
```

```r
## API前缀
API_pre = "http://xzqh.mca.gov.cn/data/"
```

### 爬取全国数据

```r
China = st_read(dsn = paste0(API_pre, "quanguo.json"), stringsAsFactors = FALSE)
```

```
## Reading layer `quanguo' from data source 
##   `http://xzqh.mca.gov.cn/data/quanguo.json' using driver `TopoJSON'
## Simple feature collection with 156 features and 4 fields
## Geometry type: POLYGON
## Dimension:     XY
## Bounding box:  xmin: 73.68 ymin: 3.984 xmax: 135.2 ymax: 53.65
## CRS:           NA
```

```r
st_crs(China) = 4326
```

### 爬取全国县级数据

```r
xian_quanguo = st_read(dsn = paste0(API_pre, "xian_quanguo.json"), stringsAsFactors = FALSE)
```

```
## Reading layer `xian_quanguo' from data source 
##   `http://xzqh.mca.gov.cn/data/xian_quanguo.json' using driver `TopoJSON'
## Simple feature collection with 3025 features and 5 fields
## Geometry type: MULTIPOLYGON
## Dimension:     XY
## Bounding box:  xmin: 73.62 ymin: 3.984 xmax: 135.2 ymax: 53.69
## CRS:           NA
```

```r
st_crs(xian_quanguo) = 4326
```


## 绘制地图
直接使用 **ggplot2**的 `geom_sf()` 函数绘制地图

### 绘制全国地图


```r
library(ggplot2)
ggplot(China) + geom_sf() + labs(title = "Map of PRC", x = "Lon", y = "Lat") + theme_bw()
```

![PRC](/figures/course/2024-02-08-r-map-mzb/mzb-map/map1-1.png)

### 绘制全国县级地图


```r
ggplot(xian_quanguo) + geom_sf(aes(fill = QUHUADAIMA), show.legend = FALSE) + coord_sf() +
  scale_fill_manual(values = xian_quanguo$FillColor, labels = xian_quanguo$QUHUADAIMA) +
  theme_bw()
```

![plot of chunk unnamed-chunk-4](/figures/course/2024-02-08-r-map-mzb/mzb-map/unnamed-chunk-4-1.png)
