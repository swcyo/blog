---
title: R中的全自动单因素方差分析
author: 生态R学社
date: '2024-02-07'
slug: r-auto-anova
categories:
  - R
  - 单因素方差分析
  - ANOVA
tags:
  - ANOVA
  - 教程
  - 统计学
from_Rmd: yes
---

-   教程来自https://mp.weixin.qq.com/s/bnh0FJIGNZOsJ_Ku2lySDA

> 链接：<https://pan.baidu.com/s/1verpap0DrW22mxv4JVlM7Q> 提取码：lfly

## 数据准备

导入[`csv`](/course/2024-02-07-r-auto-anova/data.csv)数据，有 A、B、C、D、E、F六组数据的TN TP TOC AN AP AK个类别

```         
data<-read.csc('data.csv')
data[1:18,1:7] ## 显示前几行
```


|Treat |  TN|     TP|   TOC|  AN|     AP|    AK|
|:-----|---:|------:|-----:|---:|------:|-----:|
|A     | 159| 0.1517| 37.77| 159| 0.1517| 37.77|
|A     | 160| 0.1566| 38.16| 160| 0.1566| 38.16|
|A     | 158| 0.1497| 41.82| 158| 0.1497| 41.82|
|A     | 155| 0.1605| 41.96| 155| 0.1605| 41.96|
|A     | 154| 0.1568| 42.24| 154| 0.1568| 42.24|
|A     | 157| 0.1514| 37.86| 157| 0.1514| 37.86|
|A     | 151| 0.1627| 37.36| 151| 0.1627| 37.36|
|A     | 152| 0.1548| 40.46| 152| 0.1548| 40.46|
|A     | 150| 0.1485| 40.37| 150| 0.1485| 40.37|
|B     | 165| 0.1664| 40.52| 165| 0.1664| 40.52|
|B     | 166| 0.1618| 39.35| 166| 0.1618| 39.35|
|B     | 163| 0.1573| 47.47| 163| 0.1573| 47.47|
|B     | 159| 0.1644| 46.32| 159| 0.1644| 46.32|
|B     | 158| 0.1605| 40.80| 158| 0.1605| 40.80|
|B     | 158| 0.1592| 42.42| 158| 0.1592| 42.42|
|B     | 153| 0.1759| 39.96| 153| 0.1759| 39.96|
|B     | 150| 0.1597| 41.97| 150| 0.1597| 41.97|
|B     | 151| 0.1578| 40.53| 151| 0.1578| 40.53|

## 数据实践

试用两个循环，试用完全自动化，可以分析多个变量。

### 定义单因素方差分析的函数功能

注意，需提前安装`agricolae`包
```
install.packages('agricolae')
```

```r
# 定义一个函数，用于执行方差分析和生成绘图数据
perform_analysis <- function(data, indicator) {
  # 读取指标
  data$IND <- data[[indicator]]

  # 将处理因子化
  data$Treat <- as.factor(data$Treat)

  # 方差齐性检验 bartlett.test
  nom <- bartlett.test(data$IND ~ data$Treat, data = data)
  print(nom)

  # 单因素方差分析整体检验
  oneway <- aov(data$IND ~ data$Treat, data = data)
  print(anova(oneway))

  # LSD多重比较
  library(agricolae)  # 需提前安装
  out <- LSD.test(oneway, "data$Treat", p.adj = "none")
  print(out)

  # 整理绘图所需数据
  mar <- out$groups
  rownamemar <- row.names(mar)
  newmar <- data.frame(rownamemar, mar$`data$IND`, mar$groups)
  sort <- newmar[order(newmar$rownamemar), ]

  # 将groups的数据框按列名排序，目的是保持与均值标准差的数据一一对应
  rowname <- row.names(out$means)
  mean <- out$means[, 1]
  se <- out$means[, 2]/sqrt(out$means[, 3])  # 计算标准误差
  marker <- sort$mar.groups

  # 创建绘图数据框
  plotdata <- data.frame(rowname, mean, se, marker)

  return(plotdata)
}
```

### 读取数据和显示结果

这个最终显示的是运行结果，设置**只显示了前三组运行结果**，有了这个结果绘图起来就容易多了，简直是解放生产力。

```
# 手动读取数据，换成自己的文件名即可
data <- read.csv(file.choose(), header = TRUE) 
```

```r
# 输出列名
column_names <- names(data)[-1]
column_names
```

```
## [1] "TN"  "TP"  "TOC" "AN"  "AP"  "AK"
```

```r
# 创建一个空的数据框用于存储结果
merged_data <- data.frame()

# 定义变量，表示要分析的列名 转换为变量向量
variables <- as.vector(column_names)

# 使用循环执行方差分析
for (variable in variables) {
  # 执行方差分析并生成绘图数据
  plotdata <- perform_analysis(data, variable)

  # 添加来源列
  plotdata$source <- variable

  # 合并数据
  merged_data <- rbind(merged_data, plotdata)
}
```

```
## 
## 	Bartlett test of homogeneity of variances
## 
## data:  data$IND by data$Treat
## Bartlett's K-squared = 7.3, df = 5, p-value = 0.2
## 
## Analysis of Variance Table
## 
## Response: data$IND
##            Df Sum Sq Mean Sq F value Pr(>F)    
## data$Treat  5   5274    1055      36  4e-15 ***
## Residuals  48   1408      29                   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## $statistics
##   MSerror Df  Mean    CV t.value   LSD
##     29.32 48 152.5 3.551   2.011 5.133
## 
## $parameters
##         test p.ajusted     name.t ntr alpha
##   Fisher-LSD      none data$Treat   6  0.05
## 
## $means
##   data$IND   std r    se   LCL   UCL Min Max Q25 Q50 Q75
## A    155.1 3.621 9 1.805 151.5 158.7 150 160 152 155 158
## B    158.1 5.883 9 1.805 154.5 161.7 150 166 153 158 163
## C    144.3 5.099 9 1.805 140.7 148.0 137 151 140 145 149
## D    155.6 4.275 9 1.805 151.9 159.2 149 162 153 156 157
## E    166.2 8.182 9 1.805 162.6 169.9 155 178 159 167 172
## F    135.7 4.123 9 1.805 132.0 139.3 130 141 131 137 138
## 
## $comparison
## NULL
## 
## $groups
##   data$IND groups
## E    166.2      a
## B    158.1      b
## D    155.6      b
## A    155.1      b
## C    144.3      c
## F    135.7      d
## 
## attr(,"class")
## [1] "group"
## 
## 	Bartlett test of homogeneity of variances
## 
## data:  data$IND by data$Treat
## Bartlett's K-squared = 8, df = 5, p-value = 0.2
## 
## Analysis of Variance Table
## 
## Response: data$IND
##            Df  Sum Sq  Mean Sq F value Pr(>F)  
## data$Treat  5 0.00103 2.07e-04    2.56   0.04 *
## Residuals  48 0.00388 8.08e-05                 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## $statistics
##     MSerror Df   Mean    CV t.value      LSD
##   8.078e-05 48 0.1561 5.756   2.011 0.008519
## 
## $parameters
##         test p.ajusted     name.t ntr alpha
##   Fisher-LSD      none data$Treat   6  0.05
## 
## $means
##   data$IND      std r       se    LCL    UCL    Min    Max    Q25    Q50    Q75
## A   0.1548 0.004856 9 0.002996 0.1487 0.1608 0.1485 0.1627 0.1514 0.1548 0.1568
## B   0.1626 0.005820 9 0.002996 0.1565 0.1686 0.1573 0.1759 0.1592 0.1605 0.1644
## C   0.1582 0.011233 9 0.002996 0.1522 0.1642 0.1340 0.1713 0.1530 0.1587 0.1666
## D   0.1496 0.010963 9 0.002996 0.1436 0.1557 0.1215 0.1605 0.1511 0.1513 0.1532
## E   0.1594 0.010085 9 0.002996 0.1533 0.1654 0.1411 0.1691 0.1570 0.1607 0.1666
## F   0.1523 0.008896 9 0.002996 0.1463 0.1583 0.1410 0.1703 0.1487 0.1520 0.1569
## 
## $comparison
## NULL
## 
## $groups
##   data$IND groups
## B   0.1626      a
## E   0.1594     ab
## C   0.1582     ab
## A   0.1548    abc
## F   0.1523     bc
## D   0.1496      c
## 
## attr(,"class")
## [1] "group"
## 
## 	Bartlett test of homogeneity of variances
## 
## data:  data$IND by data$Treat
## Bartlett's K-squared = 4, df = 5, p-value = 0.6
## 
## Analysis of Variance Table
## 
## Response: data$IND
##            Df Sum Sq Mean Sq F value Pr(>F)  
## data$Treat  5   73.4   14.67    2.54  0.041 *
## Residuals  48  277.5    5.78                 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## $statistics
##   MSerror Df Mean    CV t.value   LSD
##     5.781 48 41.4 5.808   2.011 2.279
## 
## $parameters
##         test p.ajusted     name.t ntr alpha
##   Fisher-LSD      none data$Treat   6  0.05
## 
## $means
##   data$IND   std r     se   LCL   UCL   Min   Max   Q25   Q50   Q75
## A    39.78 1.999 9 0.8015 38.17 41.39 37.36 42.24 37.86 40.37 41.82
## B    42.15 2.862 9 0.8015 40.54 43.76 39.35 47.47 40.52 40.80 42.42
## C    39.85 1.985 9 0.8015 38.24 41.46 37.55 43.09 37.91 40.27 41.27
## D    42.65 1.658 9 0.8015 41.04 44.26 39.48 44.75 42.15 42.93 43.84
## E    41.59 2.753 9 0.8015 39.98 43.20 38.57 45.29 39.43 41.56 44.54
## F    42.37 2.869 9 0.8015 40.76 43.98 38.48 46.61 40.19 41.48 44.44
## 
## $comparison
## NULL
## 
## $groups
##   data$IND groups
## D    42.65      a
## F    42.37      a
## B    42.15      a
## E    41.59     ab
## C    39.85      b
## A    39.78      b
## 
## attr(,"class")
## [1] "group"
## 
## 	Bartlett test of homogeneity of variances
## 
## data:  data$IND by data$Treat
## Bartlett's K-squared = 7.3, df = 5, p-value = 0.2
## 
## Analysis of Variance Table
## 
## Response: data$IND
##            Df Sum Sq Mean Sq F value Pr(>F)    
## data$Treat  5   5274    1055      36  4e-15 ***
## Residuals  48   1408      29                   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## $statistics
##   MSerror Df  Mean    CV t.value   LSD
##     29.32 48 152.5 3.551   2.011 5.133
## 
## $parameters
##         test p.ajusted     name.t ntr alpha
##   Fisher-LSD      none data$Treat   6  0.05
## 
## $means
##   data$IND   std r    se   LCL   UCL Min Max Q25 Q50 Q75
## A    155.1 3.621 9 1.805 151.5 158.7 150 160 152 155 158
## B    158.1 5.883 9 1.805 154.5 161.7 150 166 153 158 163
## C    144.3 5.099 9 1.805 140.7 148.0 137 151 140 145 149
## D    155.6 4.275 9 1.805 151.9 159.2 149 162 153 156 157
## E    166.2 8.182 9 1.805 162.6 169.9 155 178 159 167 172
## F    135.7 4.123 9 1.805 132.0 139.3 130 141 131 137 138
## 
## $comparison
## NULL
## 
## $groups
##   data$IND groups
## E    166.2      a
## B    158.1      b
## D    155.6      b
## A    155.1      b
## C    144.3      c
## F    135.7      d
## 
## attr(,"class")
## [1] "group"
## 
## 	Bartlett test of homogeneity of variances
## 
## data:  data$IND by data$Treat
## Bartlett's K-squared = 8, df = 5, p-value = 0.2
## 
## Analysis of Variance Table
## 
## Response: data$IND
##            Df  Sum Sq  Mean Sq F value Pr(>F)  
## data$Treat  5 0.00103 2.07e-04    2.56   0.04 *
## Residuals  48 0.00388 8.08e-05                 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## $statistics
##     MSerror Df   Mean    CV t.value      LSD
##   8.078e-05 48 0.1561 5.756   2.011 0.008519
## 
## $parameters
##         test p.ajusted     name.t ntr alpha
##   Fisher-LSD      none data$Treat   6  0.05
## 
## $means
##   data$IND      std r       se    LCL    UCL    Min    Max    Q25    Q50    Q75
## A   0.1548 0.004856 9 0.002996 0.1487 0.1608 0.1485 0.1627 0.1514 0.1548 0.1568
## B   0.1626 0.005820 9 0.002996 0.1565 0.1686 0.1573 0.1759 0.1592 0.1605 0.1644
## C   0.1582 0.011233 9 0.002996 0.1522 0.1642 0.1340 0.1713 0.1530 0.1587 0.1666
## D   0.1496 0.010963 9 0.002996 0.1436 0.1557 0.1215 0.1605 0.1511 0.1513 0.1532
## E   0.1594 0.010085 9 0.002996 0.1533 0.1654 0.1411 0.1691 0.1570 0.1607 0.1666
## F   0.1523 0.008896 9 0.002996 0.1463 0.1583 0.1410 0.1703 0.1487 0.1520 0.1569
## 
## $comparison
## NULL
## 
## $groups
##   data$IND groups
## B   0.1626      a
## E   0.1594     ab
## C   0.1582     ab
## A   0.1548    abc
## F   0.1523     bc
## D   0.1496      c
## 
## attr(,"class")
## [1] "group"
## 
## 	Bartlett test of homogeneity of variances
## 
## data:  data$IND by data$Treat
## Bartlett's K-squared = 4, df = 5, p-value = 0.6
## 
## Analysis of Variance Table
## 
## Response: data$IND
##            Df Sum Sq Mean Sq F value Pr(>F)  
## data$Treat  5   73.4   14.67    2.54  0.041 *
## Residuals  48  277.5    5.78                 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## $statistics
##   MSerror Df Mean    CV t.value   LSD
##     5.781 48 41.4 5.808   2.011 2.279
## 
## $parameters
##         test p.ajusted     name.t ntr alpha
##   Fisher-LSD      none data$Treat   6  0.05
## 
## $means
##   data$IND   std r     se   LCL   UCL   Min   Max   Q25   Q50   Q75
## A    39.78 1.999 9 0.8015 38.17 41.39 37.36 42.24 37.86 40.37 41.82
## B    42.15 2.862 9 0.8015 40.54 43.76 39.35 47.47 40.52 40.80 42.42
## C    39.85 1.985 9 0.8015 38.24 41.46 37.55 43.09 37.91 40.27 41.27
## D    42.65 1.658 9 0.8015 41.04 44.26 39.48 44.75 42.15 42.93 43.84
## E    41.59 2.753 9 0.8015 39.98 43.20 38.57 45.29 39.43 41.56 44.54
## F    42.37 2.869 9 0.8015 40.76 43.98 38.48 46.61 40.19 41.48 44.44
## 
## $comparison
## NULL
## 
## $groups
##   data$IND groups
## D    42.65      a
## F    42.37      a
## B    42.15      a
## E    41.59     ab
## C    39.85      b
## A    39.78      b
## 
## attr(,"class")
## [1] "group"
```

```r
# 打印合并后的数据框
print(merged_data)
```

```
##    rowname     mean       se marker source
## 1        A 155.1111 1.206976      b     TN
## 2        B 158.1111 1.961040      b     TN
## 3        C 144.3333 1.699673      c     TN
## 4        D 155.5556 1.425084      b     TN
## 5        E 166.2222 2.727319      a     TN
## 6        F 135.6667 1.374369      d     TN
## 7        A   0.1548 0.001619    abc     TP
## 8        B   0.1626 0.001940      a     TP
## 9        C   0.1582 0.003744     ab     TP
## 10       D   0.1496 0.003654      c     TP
## 11       E   0.1594 0.003362     ab     TP
## 12       F   0.1523 0.002965     bc     TP
## 13       A  39.7778 0.666493      b    TOC
## 14       B  42.1489 0.954059      a    TOC
## 15       C  39.8489 0.661686      b    TOC
## 16       D  42.6533 0.552660      a    TOC
## 17       E  41.5900 0.917601     ab    TOC
## 18       F  42.3722 0.956183      a    TOC
## 19       A 155.1111 1.206976      b     AN
## 20       B 158.1111 1.961040      b     AN
## 21       C 144.3333 1.699673      c     AN
## 22       D 155.5556 1.425084      b     AN
## 23       E 166.2222 2.727319      a     AN
## 24       F 135.6667 1.374369      d     AN
## 25       A   0.1548 0.001619    abc     AP
## 26       B   0.1626 0.001940      a     AP
## 27       C   0.1582 0.003744     ab     AP
## 28       D   0.1496 0.003654      c     AP
## 29       E   0.1594 0.003362     ab     AP
## 30       F   0.1523 0.002965     bc     AP
## 31       A  39.7778 0.666493      b     AK
## 32       B  42.1489 0.954059      a     AK
## 33       C  39.8489 0.661686      b     AK
## 34       D  42.6533 0.552660      a     AK
## 35       E  41.5900 0.917601     ab     AK
## 36       F  42.3722 0.956183      a     AK
```

### 保存运行结果
```
write.csv(merged_data ,"单因素分析结果.csv")
```

### 绘图
直接使用 **plotdata** 进行绘图,我们看看数据
```
plotdata
```

|rowname |  mean|     se|marker |source |
|:-------|-----:|------:|:------|:------|
|A       | 39.78| 0.6665|b      |AK     |
|B       | 42.15| 0.9541|a      |AK     |
|C       | 39.85| 0.6617|b      |AK     |
|D       | 42.65| 0.5527|a      |AK     |
|E       | 41.59| 0.9176|ab     |AK     |
|F       | 42.37| 0.9562|a      |AK     |


```r
library(ggplot2)
ggplot(plotdata, aes(rowname, mean)) + geom_bar(aes(fill = rowname), stat = "identity",
  color = "black", size = 0.8, position = position_dodge(0.9), width = 0.7, alpha = 0.5,
  data = plotdata) + geom_errorbar(aes(ymin = mean - se, ymax = mean + se, group = rowname),
  width = 0.3, size = 0.8, position = position_dodge(0.9), data = plotdata) + theme_bw() +
  geom_text(aes(x = factor(rowname), y = mean + se + 5, label = marker))
```

![plot of chunk unnamed-chunk-5](/figures/course/2024-02-07-r-auto-anova/auto-anova/unnamed-chunk-5-1.png)
