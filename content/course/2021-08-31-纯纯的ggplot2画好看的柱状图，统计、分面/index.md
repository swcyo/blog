---
title: 纯纯的ggplot2画好看的柱状图，统计、分面
author: 欧阳松
date: '2021-08-31'
slug: 纯纯的ggplot2画好看的柱状图，统计、分面
categories:
  - 教程
  - R
tags:
  - 教程
  - R Markdown
  - R
from_Rmd: yes
---

之前我在简书上写过几个画柱状图和统计的教程，随着对R语言的熟练，加上一些新包的出现，其实已经有了更简单的办法和代码，我这人很懒，能少写代码就少写，虽然很多啰嗦的代码可以让你更清楚的了解它的含义。

目前而言，**ggplot2**和**ggpubr**是最常用的统计作图工具，当然也有**ggstatsplot**这种更专业的统计作图包，不过个人而言，我觉得最好的作图工具还是**ggplot2**，但是**ggpubr**的代码更简单，两者各有各的好，熟悉的话一起使用也没有问题。

柱状图可以很好的看出两组或者多组的比较趋势，也是最常用的统计方法，我们的PCR和WB都离不开它，当然用Prisim可以很简单的出图，不过颜值差了很多。

比如我们得出了三种基因在对照组和处理组的结果，并做了三重复，我们先把表格读进来，表格可以是这样的：

```
data<-read.csv("~/bar.csv")
```




|gene  | Contraol1| Contraol2| Contraol3| Treat1| Treat2| Treat3|
|:-----|---------:|---------:|---------:|------:|------:|------:|
|GeneA |    1.0918|    0.8746|     1.047|  22.11|  18.85|  22.58|
|GeneB |    1.0570|    1.0570|     0.895|  51.27|  43.41|  46.85|
|GeneC |    0.9749|    0.9682|     1.060|  14.16|  16.37|  19.34|

作图要用长数据，我们可以转换一下，当然也可以直接用Excel先做成长数据的格式


```r
library(reshape2) 
bar<-melt(data,
                 id.vars = c('gene'), # 定义需要保留的部分
                 variable.name='sample', #定义样本的列名
                 value.name='value') #定义数值的列名
bar$group=rep(c('control', 'treat'), each = 9) ## 新增一个分组
```


|gene  |sample    |   value|group   |
|:-----|:---------|-------:|:-------|
|GeneA |Contraol1 |  1.0918|control |
|GeneB |Contraol1 |  1.0570|control |
|GeneC |Contraol1 |  0.9749|control |
|GeneA |Contraol2 |  0.8746|control |
|GeneB |Contraol2 |  1.0570|control |
|GeneC |Contraol2 |  0.9682|control |
|GeneA |Contraol3 |  1.0473|control |
|GeneB |Contraol3 |  0.8950|control |
|GeneC |Contraol3 |  1.0595|control |
|GeneA |Treat1    | 22.1106|treat   |
|GeneB |Treat1    | 51.2685|treat   |
|GeneC |Treat1    | 14.1559|treat   |
|GeneA |Treat2    | 18.8523|treat   |
|GeneB |Treat2    | 43.4113|treat   |
|GeneC |Treat2    | 16.3740|treat   |
|GeneA |Treat3    | 22.5752|treat   |
|GeneB |Treat3    | 46.8507|treat   |
|GeneC |Treat3    | 19.3376|treat   |

## ggplot2作图

我们先用**ggplot2**画图看看趋势，这里有一个办法可以一步计算均数和标准差，那就是加载**ggpubr**


```r
library(ggplot2) 
library(ggpubr)
p<-ggplot(bar,
       aes(group,value,color=group,fill=group))+
  geom_bar(stat="summary",fun=mean,position="dodge")+ #柱状图
  stat_summary(fun.data = 'mean_sd', geom = "errorbar", width = 0.5,position = position_dodge(0.9))+ ##一定要加载ggpubr，调用函数可以计算均数、标准差、CI等
  facet_grid(~gene,scales = 'free')+ #分面
#  theme_minimal(base_size = 13)+ #自己定义一个主题和字体大小
  scale_color_manual(values = c('steelblue','brown'))+ # 定义一下配色
  scale_fill_manual(values = c('steelblue','brown'))+ #填充的配色也定义一下
    labs(x=NULL,y='Relative gene expression')
p
```

![plot of chunk unnamed-chunk-5](/figures/course/2021-08-31-纯纯的ggplot2画好看的柱状图，统计、分面/index/unnamed-chunk-5-1.png)

```r
## 我们再做一个t检验，当然首先还是要看能不能用t检验，这个可以参照我之前的教程
p1<-p+
  geom_signif(comparisons = list(c("control","treat")),map_signif_level=T,test = 't.test')
p1
```

![plot of chunk unnamed-chunk-5](/figures/course/2021-08-31-纯纯的ggplot2画好看的柱状图，统计、分面/index/unnamed-chunk-5-2.png)

可以看到对照组和处理组的差别很大，有时候甚至看不到对照组的误差棒，这个时候我们可以用Y叔开发的**ggbreak**给它截断，然后还可以给每个数据标上一个点，这样可以清楚的知道真实数据，这里推荐用`geom_jitter()`定义数据（之前我用过`geom_dotplot()`，但是最后的结果是底下的那个点特别大，这个问题我跟Y叔的团队反映过，得到的答复是改不了） 不知道为什么截断以后，显著性标识遮挡了，可能也是bug，AI调一下就可以


```r
library(ggbreak) # 截断的包
```

```
## ggbreak v0.1.2
## 
## If you use ggbreak in published research, please cite the following
## paper:
## 
## S Xu, M Chen, T Feng, L Zhan, L Zhou, G Yu. Use ggbreak to effectively
## utilize plotting space to deal with large datasets and outliers.
## Frontiers in Genetics. 2021, 12:774846. doi: 10.3389/fgene.2021.774846
```

```r
library(ggsignif) # 统计
p1+geom_jitter(color='gray', #定义一个点的颜色
              size=2, width = 0.2)+
  scale_y_break(c(1.5, 10), scales=0.8) #设置上下截断的坐标
```

![plot of chunk unnamed-chunk-6](/figures/course/2021-08-31-纯纯的ggplot2画好看的柱状图，统计、分面/index/unnamed-chunk-6-1.png)

这样一个近乎完美的柱状图就做好。

---

## ggpubr作图

当然ggpubr的代码更简单，一步到位，一个包里就包含了作图和统计，但是限制的地方也多，很难一次性做到完美，比如用了ggbreak就看不到头，


```r
ggbarplot(bar, "group", "value", fill = "group", color = "group", facet.by = "gene",
  palette = c("steelblue", "brown"), ggtheme = theme_bw(), legend = "none", add = c("mean_sd",
    "jitter"), , add.params = list(color = "black")) + stat_compare_means(comparisons = list(c("control",
  "treat")), label = "p.signif", method = "t.test")
```

![plot of chunk unnamed-chunk-7](/figures/course/2021-08-31-纯纯的ggplot2画好看的柱状图，统计、分面/index/unnamed-chunk-7-1.png)
