---
title: forestploter——助力实现发表级森林图绘制
author: 欧阳松
date: '2024-07-16'
slug: forestploter
categories:
  - forestploter
  - meta分析
  - 森林图
tags:
  - forestploter
  - 森林图
  - meta分析
from_Rmd: yes
---

## forestploter简介

说起亚组分析森林图，大家首先想到的肯定是R里面的`forestplot`包。今天大家一起学习另外一个包——`forestploter`。该包由Alim Dayim开发，该包可以实现发表及森林图绘制，另外还可以更改森林图中的元素，助力你的临床数据高效可视化。

学完这个，你仅需要按照示例输入文件准备你的输入文件，点点鼠标就能实现森林图绘制。你难道不心动吗？ ![](/course/2024-07-16-forestploter1/1721128559410.jpg)

上面这个是示例输入文件，将你的文件按照此格式放入表格，保存为`example_data.csv`格式。

### **加载、安装包，读入示例数据**

```         
devtools::install_github("adayim/forestploter")
library(grid)
library(forestploter)
# 读入示例数据
dt <- read.csv("example_data.csv",header = T)
View(dt)
#查看数据
```



示例数据有22行18列，我们只需要选择前6列来绘制森林图


```r
# 选择前6列
dt <- dt[, 1:6]
# indent the subgroup if there is a number in the placebo column
dt$Subgroup <- ifelse(is.na(dt$Placebo), dt$Subgroup, paste0("   ", dt$Subgroup))
# 上面这一句代码是利用ifelse函数，如果安慰剂组有NA，则Subgroup不变，否则（安慰剂组有数字，
# 利用pasteO函数将Subgroup向里面缩进）

# NA to blank or NA will be transformed to character
dt$Treatment <- ifelse(is.na(dt$Treatment), "", dt$Treatment)
dt$Placebo <- ifelse(is.na(dt$Placebo), "", dt$Placebo)
# 以上也是利用ifelse函数，将NA替换为字符。即如果Treatment和Placebo有NA，
# 则替换为字符。目的就是把NA去掉。
dt$se <- (log(dt$hi) - log(dt$est))/1.96  #计算标准误
# Add blank column for the forest plot to display CI.  Adjust the column width
# with space.
dt$` ` <- paste(rep(" ", 20), collapse = " ")
# 以上目的是产生一个新的空白列来展示可信区间

# Create confidence interval column to display
dt$`HR (95% CI)` <- ifelse(is.na(dt$se), "", sprintf("%.2f (%.2f to %.2f)", dt$est,
  dt$low, dt$hi))
# 这一句主要是进一步生成用于森林图展示的HR和95%CI。
# 也是首先利用ifelse函数，如果se为缺失值则替换为空白字符，否则利用sprintf
# 函数产生HR和95%CI，各保留两位小数点。
knitr::kable(dt)
```



|Subgroup                      |Treatment |Placebo |   est|    low|    hi|     se|   |HR (95% CI)         |
|:-----------------------------|:---------|:-------|-----:|------:|-----:|------:|:--|:-------------------|
|All Patients                  |781       |780     | 1.870| 0.1325| 3.607| 0.3352|   |1.87 (0.13 to 3.61) |
|Sex                           |          |        |    NA|     NA|    NA|     NA|   |                    |
|Male                          |535       |548     | 1.450| 0.0683| 2.831| 0.3415|   |1.45 (0.07 to 2.83) |
|Female                        |246       |232     | 2.275| 0.5077| 4.043| 0.2933|   |2.28 (0.51 to 4.04) |
|Age                           |          |        |    NA|     NA|    NA|     NA|   |                    |
|<65yr                         |297       |333     | 1.509| 0.6703| 2.348| 0.2255|   |1.51 (0.67 to 2.35) |
|>65yr                         |484       |447     | 1.469| 0.4550| 2.483| 0.2678|   |1.47 (0.45 to 2.48) |
|<75yr                         |638       |649     | 1.478| 0.5501| 2.405| 0.2485|   |1.48 (0.55 to 2.41) |
|>75yr                         |143       |131     | 1.013| 0.3437| 1.681| 0.2588|   |1.01 (0.34 to 1.68) |
|Body-mass-index               |          |        |    NA|     NA|    NA|     NA|   |                    |
|<Median                       |394       |385     | 1.185| 0.4917| 1.879| 0.2350|   |1.19 (0.49 to 1.88) |
|>Median                       |387       |394     | 1.632| 0.0176| 3.247| 0.3509|   |1.63 (0.02 to 3.25) |
|Race                          |          |        |    NA|     NA|    NA|     NA|   |                    |
|White                         |653       |685     | 1.168| 0.6373| 1.699| 0.1911|   |1.17 (0.64 to 1.70) |
|Black                         |110       |87      | 2.022| 0.8224| 3.222| 0.2377|   |2.02 (0.82 to 3.22) |
|Other                         |18        |8       | 2.623| 1.1809| 4.064| 0.2235|   |2.62 (1.18 to 4.06) |
|Baseline Statin Treatment     |          |        |    NA|     NA|    NA|     NA|   |                    |
|Yes                           |701       |692     | 2.707| 1.1797| 4.235| 0.2283|   |2.71 (1.18 to 4.24) |
|No                            |80        |88      | 1.655| 0.2913| 3.019| 0.3066|   |1.66 (0.29 to 3.02) |
|Intensity of statin treatment |          |        |    NA|     NA|    NA|     NA|   |                    |
|High                          |538       |546     | 2.544| 0.8853| 4.203| 0.2561|   |2.54 (0.89 to 4.20) |
|Not High                      |243       |234     | 1.114| 0.0385| 2.189| 0.3448|   |1.11 (0.04 to 2.19) |

准备绘图


```r
# 设置森林图参数
tm <- forest_theme(base_size = 10,#字符大小
                   ci_pch = 19,#森林图可信区间展示形状
                   ci_col = "blue",#森林图可信区间展示颜色
                   xaxis_lwd = 1.0,#横坐标宽度
                   refline_lwd = 2.0,#无效线宽度
                   refline_lty = "dashed",#无效线类型
                   refline_col = "red",#无效线颜色
                   footnote_cex = 0.6,#脚注字体大小
                   footnote_fontface = "italic",#脚注字体样式
                   footnote_col = "#636363")#脚注颜色
#绘图
p <- forest(dt[,c(1:3, 8:9)],#选择数据1-3列和8-9列作为森林图元素
            est = dt$est,#HR
            lower = dt$low,#可信区间下限
            upper = dt$hi,#可信区间上限
            sizes = dt$se,#点估计框大小，用标准误映射
            ci_column = 4,#可信区间在第几列展示
            ref_line = 1,#X轴对应无效线，默认为1
            arrow_lab = c("Placebo Better", "Treatment Better"),#箭头标签
            xlim = c(0, 4),#X轴范围
            ticks_at = c(0.5, 1, 2, 3),#设置X轴分割点
            footnote = "Dr. Song",#脚注内容
            theme = tm)#主题

# 打印图片
plot(p)
```

![plot of chunk unnamed-chunk-3](/figures/course/2024-07-16-forestploter1/forestploter/unnamed-chunk-3-1.png)

就说是不是发表级的？当然个人审美不一样，你可以自行设置参数直到符合你的审美为止。那如何把这个图片输出为矢量图呢？


```r
# pdf('forestplot.pdf',height = 8,width = 8)
forest(dt[, c(1:3, 8:9)], est = dt$est, lower = dt$low, upper = dt$hi, sizes = dt$se,
  ci_column = 4, ref_line = 1, arrow_lab = c("Placebo Better", "Treatment Better"),
  xlim = c(0, 4), ticks_at = c(0.5, 1, 2, 3), footnote = "Swcyo", theme = tm)
```

![plot of chunk unnamed-chunk-4](/figures/course/2024-07-16-forestploter1/forestploter/unnamed-chunk-4-1.png)

```r
dev.off()
```

```
## null device 
##           1
```

当然，以上只是一个初级的森林图。该包还可以对森林图进一步处理从而提升档次。


```r
# 编辑第3行文字
g <- edit_plot(p, row = 3, gp = gpar(col = "red", fontface = "italic"))  #第
# 3行字体颜色设置为红色，字体为斜体 加粗分组文本
g <- edit_plot(g, row = c(2, 5, 10, 13, 17, 20), gp = gpar(fontface = "bold"))

# 在顶部增加文本，在第2-3行，即treatment和placebo上加一行Treatment group
g <- insert_text(g, text = "Treatment group", col = 2:3, part = "header", gp = gpar(fontface = "bold"))


# 编辑第5行背景，第5行背景色改为橙色
g <- edit_plot(g, row = 5, which = "background", gp = gpar(fill = "darkorange"))

# 添加文本，第10行从最左边添加那本，文本颜色诶紫色，斜体，字体大小0.6
g <- insert_text(g, text = "This is a long text. Age and gender summarised above.\nBMI is next",
  row = 10, just = "left", gp = gpar(cex = 0.6, col = "purple", fontface = "italic"))
# 打印图片
plot(g)
```

![plot of chunk unnamed-chunk-5](/figures/course/2024-07-16-forestploter1/forestploter/unnamed-chunk-5-1.png)

那这个图片怎么输出呢？我们这次不用pdf函数，换`ggsave`试试。调整好宽度和高度，我们设置图片分辨率为600dpi，妥妥的发表级！

```
ggsave("plot.png", plot = g, width = 10, height = 8, dpi = 600, units = "in", device='png')
```
