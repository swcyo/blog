---
title: "单基因的肿瘤细胞系表达怎么看？CCLE告诉你"
author: "欧阳松"
date: '2021-08-30'
slug: ccle
categories:
- 教程
- R
tags:
- R
- R Markdown
- 教程
from_Rmd: yes
---

泛癌的基因表达量一般可以用TCGA和GTEx实现，而肿瘤细胞系一般用CCLE数据库. [临床生信之家](https://www.aclbi.com/static/index.html#/ccle)是一个很好的在线工具，目前上架了CCLE的功能，出的图见下，可以实现单基因在泛癌和单病种的可视化，但是这个网址什么都好，就是有次数限制，后面使用要`加钱`，而且价钱不菲，学生党望而却步。。。

会R语言，当然可以省掉这笔巨款，而且可以DIY，乐趣无穷

首先去CCLE官网下载数据，目前网页更新了，功能也多了 比如`TP53`，访问这个网址[TP53 DepMap Gene Summary](https://depmap.org/portal/gene/TP53?tab=overview&characterization=expressio#characterization)就行，在**Characterization**里~~Expression 21Q3 Public~~ **Expression Public 23Q4**右边有个下载标志，基因单位是`Log2(TPM+1)`，很科学

CCLE2021年的更新了很多,比以前好看多了,也科学多了啊...

下载后的数据默认命名是：~~TP53 Expression 21Q3 Public.csv~~ [TP53 Expression Public 23Q4.csv](https://depmap.org/portal/partials/entity_summary/download?entity_id=38037&dep_enum_name=expression&size_biom_enum_name=none&color=none)

不过这里最大的问题就是国内访问的速度真的是很随机啊,想快有时候根本快不了,如果电脑一直打不开,就用手机打开再传到电脑上吧,~~当然你也可以去hiplot.com.cn上去下载数据,虽然不是最新的,然而至少网速很快,但是需要二次处理一下数据~~

首先把数据读进R里面

```         
library(readr)
TP53<- read_csv("~/TP53 Expression Public 23Q4.csv")
```



可以看到表格里很多有用信息，包括基因表达量、细胞系名、原发病、器官和亚型，这样我们不用二次处理了,可以跑代码了

-   第一步，画个泛癌的**boxplot**，可以用**ggplot2**包，也可以用**ggpubr**包的`ggboxplot()`函数，但是最好还是**ggplot2**，可以按中位数排序，标上均数标准差，还可以标一下所有数值的均值。


```r
library(ggplot2)
library(ggpubr)
## plot
ggplot(TP53, 
       aes(x = reorder(`Primary Disease`,`Expression Public 23Q4`, FUN = median),  #按中位数自动排序
           y =`Expression Public 23Q4`,color=`Primary Disease`)) + #y也可以是Lineage
    geom_boxplot()+ #添加boxplot
    geom_point() + #添加点
    theme_classic(base_size = 12)+ #主题和字体大小
    rotate_x_text(45)+ #X轴45度倾斜一下
    theme(legend.position="none")+ #不需要显示标签
    xlab(NULL)+ylab("TP53 expression \nLog2(TPM+1)")+ #改下坐标名称
    stat_summary(fun.data = 'mean_sd', geom = "errorbar", width = 0.5,position = position_dodge(0.9))+ #自动计算均数标准差，加个误差棒
    geom_hline(yintercept = mean(TP53$`Expression Public 23Q4`), lty = 4)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-08-30-ccle/ccle/fig1-1.png" alt="基础绘图"  />
<p class="caption">基础绘图</p>
</div>

```r
#自动计算均值，标个虚线
```

当然也可以统计一下差异，再加一句`+stat_compare_means(method = "anova")`就行。

-   第二步，提取单个癌症的数据画个棒棒糖图，可以用**ggplot2**，也可以用**ggpubr**包的`ggdotchart()`画图。

-   比如，你只想提取肾细胞癌的数据,可以用这个公式,由于是文字,需要加引号,如果是数字,就不需要引号


```r
data <- TP53[TP53$`Primary Disease` == "Renal Cell Carcinoma", ]
```

我是这样设计图片的，以点的大小代表基因表达量，按颜色表达程度，颜色从蓝到红，可以从大到小排序，也可以从小到大排列，然后用均数隔开


```r
ggplot(data, aes(x = reorder(`Cell Line Name`, `Expression Public 23Q4`), y = `Expression Public 23Q4`)) +
  geom_point(aes(size = `Expression Public 23Q4`, color = `Expression Public 23Q4`),
    stat = "identity") + scale_color_continuous(low = "steelblue", high = "brown") +
  geom_segment(aes(y = mean(`Expression Public 23Q4`), x = `Cell Line Name`, yend = `Expression Public 23Q4`,
    xend = `Cell Line Name`), color = "black") + theme_bw(base_size = 12) + coord_flip() +
  xlab(NULL) + ylab("TP53 expression") + geom_hline(yintercept = mean(data$`Expression Public 23Q4`),
  lty = 2)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-08-30-ccle/ccle/fig2-1.png" alt="提取肾细胞癌数据绘图"  />
<p class="caption">提取肾细胞癌数据绘图</p>
</div>

从小到大再来一次，reorder里加个-就行


```r
ggplot(data, aes(x = reorder(`Cell Line Name`, -`Expression Public 23Q4`), y = `Expression Public 23Q4`)) +
  geom_point(aes(size = `Expression Public 23Q4`, color = `Expression Public 23Q4`),
    stat = "identity") + scale_color_continuous(low = "steelblue", high = "brown") +
  geom_segment(aes(y = mean(`Expression Public 23Q4`), x = `Cell Line Name`, yend = `Expression Public 23Q4`,
    xend = `Cell Line Name`), color = "black") + theme_classic(base_size = 12) +
  coord_flip() + xlab(NULL) + ylab("TP53 expression") + geom_hline(yintercept = mean(data$`Expression Public 23Q4`),
  lty = 2)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-08-30-ccle/ccle/fig3-1.png" alt="更改排序"  />
<p class="caption">更改排序</p>
</div>

不要钱的，不香吗？
