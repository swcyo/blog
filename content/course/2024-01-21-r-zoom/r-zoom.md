---
title: R语言画图之图片局部放大
author: 欧阳松
date: '2024-01-21'
slug: r-zoom
categories:
  - R
  - 图片放大
  - ggplot2
  - 教程
  - ggmagnify
tags:
  - R
  - 画图
  - ggplot2
  - ggmagnify
from_Rmd: yes
---

 在处理图片的时候，有时候需要将局部图片放大，比如病理的免疫组化的图，那么如何实现R的局部放大呢？
 
> 记录一个蛮有趣的功能，选择性放大图片的部分区域。处理密集分布数据应该很有效，示例数据不是很合适。

## 示例数据演示
 我们使用测试数据*iris*，绘制Petal.Length与Petal.Width相关散点图。
### 加载数据 

```r
data(iris)
## 显示前10行数据
knitr::kable(iris[1:10, ])
```



| Sepal.Length| Sepal.Width| Petal.Length| Petal.Width|Species |
|------------:|-----------:|------------:|-----------:|:-------|
|          5.1|         3.5|          1.4|         0.2|setosa  |
|          4.9|         3.0|          1.4|         0.2|setosa  |
|          4.7|         3.2|          1.3|         0.2|setosa  |
|          4.6|         3.1|          1.5|         0.2|setosa  |
|          5.0|         3.6|          1.4|         0.2|setosa  |
|          5.4|         3.9|          1.7|         0.4|setosa  |
|          4.6|         3.4|          1.4|         0.3|setosa  |
|          5.0|         3.4|          1.5|         0.2|setosa  |
|          4.4|         2.9|          1.4|         0.2|setosa  |
|          4.9|         3.1|          1.5|         0.1|setosa  |

### 绘制散点图

```r
library(ggplot2)
theme <- theme_bw() + theme(plot.title = element_text(hjust = 0.5, size = 22), axis.text.x = element_text(hjust = 0.5,
  size = 18), axis.text.y = element_text(hjust = 0.5, size = 18), axis.title.y = element_text(size = 18),
  axis.title.x = element_text(size = 18), legend.text = element_text(size = 18),
  legend.title = element_blank(), legend.position = c(0.85, 0.2), legend.background = element_blank())
p1 <- ggplot(data = iris, aes(x = Petal.Length, y = Petal.Width)) + geom_point(aes(color = Species),
  size = 2) + scale_color_manual(values = c("#2fa1dd", "#e6b707", "#f87669")) +
  theme
p1 <- p1 + geom_smooth(method = "lm", formula = y ~ x, se = TRUE, show.legend = FALSE,
  color = "#66C2A5")
p1
```

![plot of chunk unnamed-chunk-2](/figures/course/2024-01-21-r-zoom/r-zoom/unnamed-chunk-2-1.png)

### 安装并加载ggmagnify
ggmagnify目前还没有被CRAN收录，目前有两种安装方式：GitHub和r-universe

```
# 如果可以访问GitHub
# remotes::install_github("hughjonesd/ggmagnify")
# 国内可通过r-universe进行安装
install.packages("ggmagnify", repos = c("https://hughjonesd.r-universe.dev", 
                 "https://cloud.r-project.org"))
library(ggmagnify)                
```



### 指定局部放大区域

 指定局部放大区域的话，我们需要定义相应的坐标，比如原图需要放大的区域坐标，以及将放大后的局部图插入到原图的位置坐标。
 
 指定放大坐标范围与放大后图片放置区域，顺序为`xmin`, `xmax`, `ymin`, `ymax`

```r
target = c(4.6, 5.4, 1.5, 2)  ##目标区域的x轴为4.6-5.4，y轴为4.1-5.2
insert = c(1.2, 3, 1.3, 2.5)  ##插入区域的x轴为1.2-3，y轴为1.3-2.5
```

放大局部区域只需要一步函数`geom_magnify()`


```r
p2 <- p1 + geom_magnify(from = target, to = insert)
p2
```

![plot of chunk unnamed-chunk-5](/figures/course/2024-01-21-r-zoom/r-zoom/unnamed-chunk-5-1.png)

我们还可以放大局部区域（添加坐标轴）,并自定义轮廓线颜色

```r
p3 <- p1 + geom_magnify(from = target, to = insert, proj = "facing", colour = "#0066fe",
  linewidth = 0.5, axes = TRUE)
p3
```

![plot of chunk unnamed-chunk-6](/figures/course/2024-01-21-r-zoom/r-zoom/unnamed-chunk-6-1.png)

对比看一下三图的效果

```r
ggpubr::ggarrange(p1, p2, p3, ncol = 3)
```

![plot of chunk unnamed-chunk-7](/figures/course/2024-01-21-r-zoom/r-zoom/unnamed-chunk-7-1.png)

## 更多功能演示
根据官网文档介绍，适当进行演示

### 基本嵌入

要创建嵌入，使用 `geom_magnify(from,to)`。`from`可以是一个列表，给出要放大区域的四个角：
`from = c(xmin, xmax, ymin, ymax)`。也可以使用`xmin`等。

同样，`to` 指定了放大后的嵌入部分的位置： `to = c(xmin, xmax, ymin, ymax)`。



```r
library(ggplot2)
library(ggmagnify)

ggp <- ggplot(iris, aes(Sepal.Width, Sepal.Length, colour = Species)) + geom_point() +
  xlim(c(2, 6)) + theme_bw()

from <- list(3, 4, 6.5, 7.5)
to <- list(4, 6, 5, 6.5)

ggp + geom_magnify(from = from, to = to)
```

![plot of chunk example-basic](/figures/course/2024-01-21-r-zoom/r-zoom/example-basic-1.png)

### 插入阴影


```r
loadNamespace("ggfx")
```

```
## <environment: namespace:ggfx>
```

```r
ggp + geom_magnify(from = from, to = to, shadow = TRUE)
```

![plot of chunk example-shadow](/figures/course/2024-01-21-r-zoom/r-zoom/example-shadow-1.png)

### 椭圆形

需要 R 4.1 或更高版本和适当的图形设备。


```r
ggp + geom_magnify(from = from, to = to, shape = "ellipse", shadow = TRUE)
```

![plot of chunk example-ellipse](/figures/course/2024-01-21-r-zoom/r-zoom/example-ellipse-1.png)

### 选择特定要放大的点

选择要放大特定值的点，可在 `aes()` 中映射 `from`：
  

```r
ggpi <- ggplot(iris, aes(Sepal.Width, Sepal.Length, colour = Species)) +
  geom_point() + xlim(c(1.5, 6))+theme_linedraw()

ggpi + geom_magnify(aes(from = Species == "setosa" & Sepal.Length < 5),  ## 选择物种为setosa，且长度<5
                    to = c(4, 6, 6, 7.5))
```

![plot of chunk example-logical-from](/figures/course/2024-01-21-r-zoom/r-zoom/example-logical-from-1.png)

### 分面


```r
ggpi + facet_wrap(vars(Species)) + geom_magnify(aes(from = Sepal.Length > 5 & Sepal.Length <
  6.5), to = c(4.5, 6, 6, 7.5), shadow = TRUE)
```

![plot of chunk example-faceting](/figures/course/2024-01-21-r-zoom/r-zoom/example-faceting-1.png)

### 放大任意区域（实验）

使用 `shape = "outline"` 放大一组点的凸壳：


```r
library(dplyr)

starwars_plot <- starwars |>
  mutate(Human = species == "Human") |>
  select(mass, height, Human) |>
  na.omit() |>
  ggplot(aes(mass, height, color = Human)) + geom_point() + xlim(0, 220) + ylim(0,
  250) + theme_dark() + theme(panel.grid = element_line(linetype = 2, colour = "yellow"),
  axis.line = element_blank(), panel.background = element_rect(fill = "black"),
  legend.key = element_rect(fill = "black"), rect = element_rect(fill = "black"),
  text = element_text(colour = "white")) + scale_colour_manual(values = c(`TRUE` = "red",
  `FALSE` = "lightblue")) + ggtitle("Mass and height of Star Wars characters",
  subtitle = "Humans magnified")

starwars_plot + geom_magnify(aes(from = Human), to = c(30, 200, 0, 120), shadow = TRUE,
  shadow.args = list(colour = "yellow", sigma = 10, x_offset = 2, y_offset = 5),
  alpha = 0.8, colour = "yellow", linewidth = 0.6, shape = "outline", expand = 0.2)
```

![plot of chunk example-outline](/figures/course/2024-01-21-r-zoom/r-zoom/example-outline-1.png)

### 使用图形或数据框放大任何形状
  

```r
s <- seq(0, 2 * pi, length = 7)
hex <- data.frame(x = 3 + sin(s)/2, y = 6 + cos(s)/2)

ggpi + geom_magnify(from = hex, to = c(4, 6, 5, 7), shadow = TRUE, aspect = "fixed")
```

![plot of chunk example-grob](/figures/course/2024-01-21-r-zoom/r-zoom/example-grob-1.png)

### 地图 (试验)

对于地图，`shape = "outline"`只放大选定的地图多边形：
  

```r
usa <- sf::st_as_sf(maps::map("state", fill = TRUE, plot = FALSE))

ggpm <- ggplot(usa) + geom_sf(aes(fill = ID == "texas"), colour = "grey20") + coord_sf(default_crs = sf::st_crs(4326),
  ylim = c(10, 50)) + theme(legend.position = "none") + scale_fill_manual(values = c(`TRUE` = "red",
  `FALSE` = "steelblue4"))


ggpm + geom_magnify(aes(from = ID == "texas"), to = c(-125, -98, 10, 30), shadow = TRUE,
  linewidth = 1, colour = "orange2", shape = "outline", aspect = "fixed", expand = 0)
```

![plot of chunk example-map](/figures/course/2024-01-21-r-zoom/r-zoom/example-map-1.png)

### 添加轴


```r
ggp + scale_x_continuous(labels = NULL) + geom_magnify(from = from, to = to, axes = "xy")
```

![plot of chunk example-axes](/figures/course/2024-01-21-r-zoom/r-zoom/example-axes-1.png)


### 投影线和边界

#### 颜色和线型


```r
ggp + geom_magnify(from = from, to = to, colour = "darkgreen", linewidth = 0.5, proj.linetype = 3)
```

![plot of chunk example-colours](/figures/course/2024-01-21-r-zoom/r-zoom/example-colours-1.png)


#### 投影线样式


```r
ggpi <- ggplot(iris, aes(Sepal.Width, Sepal.Length, colour = Species)) + geom_point() +
  theme_bw()
from2 <- c(3, 3.5, 6.5, 7)
to2 <- c(2.75, 3.75, 5, 6)

ggpi + geom_magnify(from = from2, to = to2, proj = "facing")  # 默认
```

![plot of chunk example-proj](/figures/course/2024-01-21-r-zoom/r-zoom/example-proj-1.png)

```r
ggpi + geom_magnify(from = from2, to = to2, proj = "corresponding")  # 各角角落落
```

![plot of chunk example-proj](/figures/course/2024-01-21-r-zoom/r-zoom/example-proj-2.png)

```r
ggpi + geom_magnify(from = from2, to = to2, proj = "single")  # 只一行
```

![plot of chunk example-proj](/figures/course/2024-01-21-r-zoom/r-zoom/example-proj-3.png)

### 技巧和窍门

#### 为嵌入图添加图层

`geom_magnify()` 在添加图层时会存储绘图。因此，顺序很重要
  

```r
ggpi <- ggplot(iris, aes(Sepal.Width, Sepal.Length, colour = Species)) + geom_point() +
  xlim(2, 6)

from3 <- c(2.5, 3.5, 6, 7)
to3 <- c(4.7, 6.1, 4.3, 5.7)

ggpi + geom_smooth() + geom_magnify(from = from3, to = to3)
```

![plot of chunk example-order](/figures/course/2024-01-21-r-zoom/r-zoom/example-order-1.png)

```r
# 不打印平滑的插页:
ggpi + geom_magnify(from = from3, to = to3) + geom_smooth()
```

![plot of chunk example-order](/figures/course/2024-01-21-r-zoom/r-zoom/example-order-2.png)

如果要对插图进行复杂修改，请明确设置 `plot`：
  

```r
booms <- ggplot(faithfuld, aes(waiting, eruptions)) + geom_contour_filled(aes(z = density),
  bins = 50) + scale_fill_viridis_d(option = "B") + theme(legend.position = "none")

booms_inset <- booms + geom_point(data = faithful, color = "red", fill = "white",
  alpha = 0.7, size = 2, shape = "circle filled") + coord_cartesian(expand = FALSE)

shadow.args <- list(colour = alpha("grey80", 0.8), x_offset = 0, y_offset = 0, sigma = 10)

booms + geom_magnify(from = c(78, 90, 4, 4.8), to = c(70, 90, 1.7, 3.3), colour = "white",
  shape = "ellipse", shadow = TRUE, shadow.args = shadow.args, plot = booms_inset)
```

![plot of chunk example-advanced-2](/figures/course/2024-01-21-r-zoom/r-zoom/example-advanced-2-1.png)

### 保持网格线不变

确保插页使用与主图相同的网格线、在 `scale_x` 和 `scale_y` 中设置 `breaks`：
  

```r
ggp2 <- ggplot(iris, aes(Sepal.Width, Sepal.Length, color = Species)) + geom_point() +
  theme_classic() + theme(panel.grid.major = element_line("grey80"), panel.grid.minor = element_line("grey90"))

# 不同的网格线:
ggp2 + geom_magnify(from = c(2.45, 3.05, 5.9, 6.6), to = c(3.4, 4.4, 5.5, 6.6), shadow = TRUE)
```

![plot of chunk example-gridlines](/figures/course/2024-01-21-r-zoom/r-zoom/example-gridlines-1.png)

```r
# 修正网格线:
ggp2 + scale_x_continuous(breaks = seq(2, 5, 0.5)) + scale_y_continuous(breaks = seq(5,
  8, 0.5)) + geom_magnify(from = c(2.45, 3.05, 5.9, 6.6), to = c(3.4, 4.4, 5.5,
  6.6), shadow = TRUE)
```

![plot of chunk example-gridlines](/figures/course/2024-01-21-r-zoom/r-zoom/example-gridlines-2.png)

### 重新计算数据

如果要重新计算插图中的平滑器、密度等，请使用 `recompute`。


```r
df <- data.frame(x = seq(-5, 5, length = 500), y = 0)
df$y[abs(df$x) < 1] <- sin(df$x[abs(df$x) < 1])
df$y <- df$y + rnorm(500, mean = 0, sd = 0.25)

ggp2 <- ggplot(df, aes(x, y)) + geom_point() + geom_smooth(method = "loess", formula = y ~
  x) + ylim(-5, 5)

# 默认图形:
ggp2 + geom_magnify(from = c(-1.25, 1.25, -1, 1), to = c(2, 5, 1, 5))
```

![plot of chunk example-recompute](/figures/course/2024-01-21-r-zoom/r-zoom/example-recompute-1.png)

```r
# 重新计算会在再次计算插图的平滑度:
ggp2 + geom_magnify(from = c(-1.25, 1.25, -1, 1), to = c(2, 5, 1, 5), recompute = TRUE)
```

![plot of chunk example-recompute](/figures/course/2024-01-21-r-zoom/r-zoom/example-recompute-2.png)

### 图片二次放大


```r
data <- data.frame(x = runif(4000), y = runif(4000))
ggm_unif <- ggplot(data, aes(x, y)) + coord_cartesian(expand = FALSE) + geom_density2d_filled(bins = 50,
  linewidth = 0, n = 200) + geom_point(color = "white", alpha = 0.5, size = 0.5) +
  theme(legend.position = "none")


ggm_unif + geom_magnify(from = c(0.05, 0.15, 0.05, 0.15), to = c(0.2, 0.4, 0.2, 0.4),
  colour = "white", proj.linetype = 1, linewidth = 0.6) + geom_magnify(from = c(0.25,
  0.35, 0.25, 0.35), to = c(0.45, 0.85, 0.45, 0.85), expand = 0, colour = "white",
  proj.linetype = 1)
```

![plot of chunk example-magnify-twice](/figures/course/2024-01-21-r-zoom/r-zoom/example-magnify-twice-1.png)

嵌入式中的*嵌入*要复杂一些，但也是可行的：
  

```r
ggp <- data.frame(x = rnorm(1e+05), y = rnorm(1e+05), colour = sample(8L, 1e+05,
  replace = TRUE)) |>
  ggplot(aes(x = x, y = y, colour = factor(colour))) + scale_color_brewer(type = "qual",
  palette = 2) + geom_point(alpha = 0.12, size = 0.7) + lims(x = c(-3, 3), y = c(-3,
  3)) + theme_classic() + theme(panel.grid = element_blank(), axis.line = element_blank(),
  plot.background = element_rect(fill = "black"), panel.background = element_rect(fill = "black"),
  title = element_text(colour = "white"), legend.position = "none")

ggpm <- ggp + lims(x = c(-0.3, 0.3), y = c(-0.3, 0.3)) + geom_magnify(from = c(-0.03,
  0.03, -0.03, 0.03), to = c(-0.3, -0.1, -0.3, -0.1), expand = FALSE, colour = "white")

ggp + geom_magnify(plot = ggpm, from = c(-0.3, 0.3, -0.3, 0.3), to = c(-3, -1, -3,
  -1), expand = FALSE, colour = "white") + labs(title = "Normal data", subtitle = "The distribution gets more uniform as you zoom in")
```

![plot of chunk example-and-so-ad-infinitum](/figures/course/2024-01-21-r-zoom/r-zoom/example-and-so-ad-infinitum-1.png)
