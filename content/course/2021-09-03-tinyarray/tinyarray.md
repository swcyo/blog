---
title: tinyarray：一个几乎能满足我所有生信需求的包
author: 欧阳松
date: '2021-09-03'
slug: tinyarray
categories:
  - 教程
  - R
  - tinyarray
tags:
  - 教程
  - R
  - TCGA
  - GEO
from_Rmd: yes
---

最早了解到tinyarray这个包是在简书上，作者是[**小洁忘了怎么分身**](https://www.jianshu.com/u/c93ac360691a) ，当时只是看了一个ID转换的教程，后面发现这是个真正的大神，这简直就是一个包罗万象的包，而且还在不断的更新，功能也越来越完善，作者也不辞辛苦的写教程，大家可以自行前去学习。

当然作者也有微信公众号，叫做生信星球公众号，微信号: bioinfoplanet，里面也有很多很实用的教程，一直都指引着我，也希望大家多关注学习。

目前有了一个新的教程，叫做[**宝藏R包tinyarray：常用图表一键收走**](https://www.jianshu.com/p/f31a5ff62474)，最让人兴奋的是作者说已经把这个包提交到CRAN了，我相信要不了多久就可以上市了，真是造福一方啊，期待中。。。。

------------------------------------------------------------------------

我们在搞生信的时候，最常见的套路是要做PCA图、箱式图、火山图、热图、韦恩图、生存分析等等，高阶一点的还要去下载GEO的数据、去转换基因ID。。。基本这就是一个简单的纯生信套路，以前的我总是去加载各种包，去找各种作图的代码。诚然最后的图都很好看，但是一直没有一个包可以全部集成。

在这个信息开源的时代，能够把别人的包做个整合其实也挺好，可能有些人会说这就是抄袭，但是我们不应该去纠结抄袭这个词，能够把别人开发的拿来主义也是为了方便大家，迈克杰克逊还有一句歌词不就是**Make a better place for you and for me**吗？

说了那么多，直接开始介绍吧！

## 安装tinyarray

目前这个包托管在GitHub上，仓库是[xjsun1221/tinyarray](https://github.com/xjsun1221/tinyarray)。由于网络限制原因，有时候需要科学上网才能安装，不过我Fork在我的[Gitee](https://gitee.com/swcyo/tinyarray)上，最近也同步了，所以建议用Gitee安装。（写这个教程前是2.21版，刚才看了一下作者9个小时又更新了一下，现在是2.2.3版，估计马上就要上市了，真是孜孜不倦啊。。。）

    install.packages("remotes")
    remotes::install_git('https://gitee.com/swcyo/tinyarray')

## 有哪些功能

我们可以在Packages那里看以下Help文件，可以看到功能很多，有兴趣的可以点开看示例

| 函数                 | 介绍                                                 |
|----------------------|------------------------------------------------------|
| box_surv             | box_surv                                             |
| cod                  | cod                                                  |
| cor.full             | cor.test for all varibles                            |
| cor.one              | cor.test for one varible with all varibles           |
| deg                  | deg                                                  |
| deseq_data           | deseq_data                                           |
| double_enrich        | draw enrichment bar plots for both up and down genes |
| draw_boxplot         | draw boxplot for expression                          |
| draw_heatmap         | draw a heatmap plot                                  |
| draw_heatmap2        | draw heatmap plots                                   |
| draw_KM              | draw_KM                                              |
| draw_pca             | draw PCA plots                                       |
| draw_tsne            | draw_tsne                                            |
| draw_venn            | draw a venn plot                                     |
| draw_volcano         | draw a volcano plot                                  |
| draw_volcano2        | draw_volcano2                                        |
| dumd                 | count unique values in every colunms for data.frame  |
| edges_to_nodes       | edges_to_nodes                                       |
| exists_anno_list     | exists_anno_list                                     |
| exprSet_hub1         | exprSet_hub1                                         |
| exp_boxplot          | exp_boxplot                                          |
| exp_hub1             | exp_hub1                                             |
| exp_surv             | exp_surv                                             |
| find_anno            | find annotation package or files                     |
| genes                | genes                                                |
| geo_download         | geo_download                                         |
| get_cgs              | get_cgs                                              |
| get_deg              | get_deg                                              |
| get_deg_all          | get_deg_all                                          |
| ggheat               | ggheat                                               |
| hypertest            | hypertest                                            |
| interaction_to_edges | interaction_to_edges                                 |
| intersect_all        | intersect_all                                        |
| lnc_anno             | lnc_anno                                             |
| lnc_annov23          | lnc_annov23                                          |
| make_tcga_group      | make_tcga_group                                      |
| match_exp_cl         | match_exp_cl                                         |
| meta1                | meta1                                                |
| mRNA_anno            | mRNA_anno                                            |
| mRNA_annov23         | mRNA_annov23                                         |
| multi_deg            | multi_deg                                            |
| multi_deg_all        | multi_deg_all                                        |
| pkg_all              | pkg_all                                              |
| plcortest            | plcortest                                            |
| point_cut            | point_cut                                            |
| quick_enrich         | quick_enrich                                         |
| sam_filter           | sam_filter                                           |
| surv_cox             | surv_cox                                             |
| surv_KM              | surv_KM                                              |
| trans_array          | trans_array                                          |
| trans_exp            | trans_exp                                            |
| t_choose             | t_choose                                             |
| union_all            | union_all                                            |

### TCGA和GTEx数据转换

这是我最早搜索到的教程，也是原创功能。教程上[**TCGA的ID转换可以一步到位了**](https://www.jianshu.com/p/c17373dc052c)，原理是用xena上的genecode进行转换，同时实现了只转换mRNA或lncRNA

比如我们下载一个TCGA的数据（建议从Xena上下载）假设下载**KICH**的count数据放桌面上，格式上tsv.gz的压缩包，地址是<https://gdc-hub.s3.us-east-1.amazonaws.com/download/TCGA-KICH.htseq_counts.tsv.gz>。

由于xena上的数据都是经过了**log2(count+1)**的处理，所以我们要转回来,即整数形式

  ```
  exp<-read.csv('/Users/mac/Desktop/TCGA-KICH.htseq_counts.tsv.gz',
              sep="\t",row.names = 1,header = T)
exp = 2^exp -1
  ```




|                   | TCGA.KN.8426.11A| TCGA.KL.8332.01A| TCGA.KN.8423.01A| TCGA.KL.8335.01A| TCGA.KL.8327.01A|
|:------------------|----------------:|----------------:|----------------:|----------------:|----------------:|
|ENSG00000000003.13 |            11729|              958|            10000|             2036|              707|
|ENSG00000000005.5  |               43|              112|               15|                0|                9|
|ENSG00000000419.11 |             1906|             1108|             1788|             1260|              774|
|ENSG00000000457.12 |             1272|              307|             1380|              382|              184|
|ENSG00000000460.15 |              178|              109|              165|               62|               32|

转换的话只要一个`trans_exp()`函数。如果只想转换mRNA，可以设置mrna_only=T，如果只想转换lncRNA，那就设置lncrna_only=T

    # 官方函数
    trans_exp(exp, mrna_only = FALSE, lncrna_only = FALSE, gtex = FALSE)

比如说我现在只想转换mRNA，只需要输入这个代码,我们看以下表


```r
library(tinyarray)
k = trans_exp(exp, mrna_only = T)  #只转换mRNA
```


|         | TCGA.KN.8426.11A| TCGA.KL.8332.01A| TCGA.KN.8423.01A| TCGA.KL.8335.01A| TCGA.KL.8327.01A| TCGA.KL.8324.11A| TCGA.KN.8430.01A| TCGA.KN.8422.11A| TCGA.KL.8338.01A| TCGA.KN.8432.11A| TCGA.KN.8429.01A| TCGA.KO.8415.11A| TCGA.KN.8436.11A| TCGA.KN.8432.01A| TCGA.KN.8424.11A| TCGA.KO.8409.01A| TCGA.KN.8437.01A| TCGA.KL.8342.01A| TCGA.KM.8443.01A| TCGA.KN.8434.01A| TCGA.KN.8429.11A| TCGA.KL.8329.01A| TCGA.KN.8435.11A| TCGA.KM.8477.01A| TCGA.KL.8331.01A| TCGA.KO.8403.01A| TCGA.KN.8433.01A| TCGA.KL.8334.01A| TCGA.KN.8424.01A| TCGA.KO.8417.01A| TCGA.KN.8430.11A| TCGA.KM.8639.01A| TCGA.KN.8421.01A| TCGA.KN.8428.01A| TCGA.KN.8427.01A| TCGA.KN.8436.01A| TCGA.KM.8442.01A| TCGA.KN.8418.01A| TCGA.KL.8345.01A| TCGA.KN.8425.01A| TCGA.KL.8323.01A| TCGA.KN.8437.11A| TCGA.KN.8435.01A| TCGA.KL.8346.01A| TCGA.KL.8326.01A| TCGA.KO.8414.01A| TCGA.KM.8441.01A| TCGA.KN.8428.11A| TCGA.KO.8406.01A| TCGA.KO.8410.01A| TCGA.KN.8431.01A| TCGA.KL.8336.11A| TCGA.KN.8423.11A| TCGA.KN.8419.11A| TCGA.KL.8325.01A| TCGA.KL.8330.01A| TCGA.KL.8343.01A| TCGA.KL.8339.01A| TCGA.KL.8326.11A| TCGA.KO.8415.01A| TCGA.KN.8425.11A| TCGA.KL.8340.01A| TCGA.KL.8336.01A| TCGA.KN.8431.11A| TCGA.KO.8403.11A| TCGA.KM.8440.01A| TCGA.KN.8427.11A| TCGA.KL.8333.01A| TCGA.KN.8434.11A| TCGA.KO.8407.01A| TCGA.KO.8408.01A| TCGA.KN.8426.01A| TCGA.KL.8339.11A| TCGA.KM.8476.01A| TCGA.KN.8433.11A| TCGA.KL.8328.01A| TCGA.KO.8413.01A| TCGA.KO.8405.01A| TCGA.KO.8416.01A| TCGA.KL.8344.01A| TCGA.KO.8404.01A| TCGA.KO.8411.01A| TCGA.KM.8438.01A| TCGA.KL.8324.01A| TCGA.KL.8329.11A| TCGA.KL.8341.01A| TCGA.KL.8337.01A| TCGA.KM.8439.01A| TCGA.KN.8419.01A|
|:--------|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|
|TSPAN6   |            11729|              958|            10000|             2036|              707|             5793|             4104|             8835|             1759|            12853|             3387|             6809|             9553|             1921|            12604|             1675|             5095|             1788|             4309|             2055|            10588|             5131|             6483|              839|             5523|             2090|             1120|             5868|             4314|             3333|             9061|             9009|             1547|             4680|             2139|             3555|             5877|             1566|             5853|             4150|            12017|             3914|             3029|             4023|             1227|             1285|             3456|             5926|             3272|            12555|              722|             5152|             8882|             9948|             5145|             4408|             2533|             1583|             4560|             1177|            15598|             2905|             2147|            13640|            10881|             4641|             7257|             1418|             7751|             2249|             1739|             2445|             8479|              995|             8023|             3615|             3112|             2708|             2171|             3704|              928|             3885|             1801|             3315|             6154|             3122|             2886|             3560|             1102|
|TNMD     |               43|              112|               15|                0|                9|               94|               11|               76|               15|               50|                8|               46|               29|                9|              116|                9|                1|               12|               14|                6|               54|                4|              190|                2|               22|               11|                0|               12|               12|               47|               16|               13|               15|               11|                0|               19|                9|                6|               23|               14|              353|               22|               54|               19|                2|                7|                2|              190|               20|               10|                1|              466|               23|               55|              129|               10|                2|                0|              214|                4|               55|                4|                6|               84|               48|                3|               54|               67|               86|               20|               12|                4|              449|                8|              136|               16|                0|                5|               75|               31|                2|               75|               60|                5|              192|               21|               31|                0|                3|
|DPM1     |             1906|             1108|             1788|             1260|              774|             1971|             2207|             1804|             1457|             2944|             2426|             1648|             1409|             1540|             1988|              940|             1843|             2295|             2215|             1412|             2834|             2250|             1739|             1520|             2143|             1938|              930|             2720|             2649|             2753|             1721|             1666|             2259|             2697|             3651|             2177|             2256|             3382|             2163|             2042|             1697|             1668|             2576|             1269|             1883|             1980|             1749|             1720|             2092|             2524|             1073|             1613|             1968|             2641|             2033|             2408|             1757|             3011|             2048|             1317|             2069|             1834|             1139|             1989|             1711|             2530|             2279|             2688|             1986|             1807|             1408|              982|             2347|             1178|             1633|             2105|             1908|             1690|             1271|             1972|             1844|             2529|             2244|             1026|             1740|             3060|             1649|             1365|             1090|
|SCYL3    |             1272|              307|             1380|              382|              184|              952|              589|             1442|              325|             1609|              700|             1019|             1028|              453|             1138|              242|              972|              512|              487|              441|              913|              533|             1161|              913|              501|              410|              219|              582|              569|              572|             1033|              884|              245|              643|              515|              532|              541|              583|              553|             1095|              438|             1285|              566|              340|              455|              634|              662|              972|              606|              729|              160|              705|             1813|             1137|              716|              637|              807|              611|              864|              331|             1546|              391|              612|             1288|             1352|              512|             1345|              624|             1114|              378|              203|              542|             1199|              167|              894|              348|              568|              468|              259|              689|              322|              441|              284|              630|             1002|              803|              426|              610|              558|
|C1orf112 |              178|              109|              165|               62|               32|              146|              145|              194|               89|              263|              156|              141|              180|               62|              209|               38|              168|              129|              103|              132|              202|              137|              227|               88|              117|               95|               56|              137|              154|              144|              180|              158|               59|              463|              425|              154|              109|              101|              144|              191|              184|              241|              145|              105|               92|               97|              116|              158|              150|              140|               31|              288|              297|              102|              213|               96|              178|              163|              144|               76|              198|               94|              172|              190|              211|               90|              145|              152|              157|              121|               78|               77|              148|               17|              137|               78|              121|               60|               87|              212|              137|              119|               48|               61|              143|              212|               96|               84|               76|
|FGR      |             1220|              270|              303|               77|              196|              597|              476|              570|              224|              488|              179|              323|              682|              181|              717|              345|              335|              372|               59|              324|              919|              536|              746|              193|               77|              419|              302|              281|              449|              154|              365|              255|               55|              194|              494|               75|              139|              116|              139|              358|              150|             1556|              115|               26|               84|              301|              354|              440|              181|              321|              106|             1275|              400|             1188|               89|               72|              189|              139|              543|              291|              571|              154|              210|              307|              517|              164|              799|             1058|              378|              623|              191|              452|              459|              236|              521|               62|              628|             1914|              401|              331|             1081|              162|              153|              221|              563|               73|              201|              357|              208|

这样很简单的我们就实验了ID转换，并且提取了mRNA

------------------------------------------------------------------------

### 画主成分PCA图

很多时候生信分析第一就是画PCA图，因为可以很快的知道两组的分布情况。 首先我们一定要有分组信息，比如我们按照最常见的肿瘤和正常组织进行分组，然后定义分组列表（因子形式），我们知道TCGA命名规则中第14位和第15位就是肿瘤和正常组织的信息，11是正常组织，01是肿瘤组织,那么可以用下面这个代码进行识别。


```r
library(stringr)
k1 = str_starts(colnames(k), "TCGA")  #k为转换后的表达矩阵
k2 = as.numeric(str_sub(colnames(k), 14, 15)) < 10
group_list = ifelse(k1 & k2, "tumor", "normal")  #定义组别
group_list = factor(group_list, levels = c("normal", "tumor"))  # 转换为因子
table(group_list)  #查看各自的数量
```

```
## group_list
## normal  tumor 
##     24     65
```

我们用`table()`函数可以很快看到正常组织24例，肿瘤组织65例。

而画PCA图只需要一个`draw_pca()`，这是因为该函数调用了**factoextra**包的`fviz_pca()`函数，这样的画我们可以节省很多步骤，因为一个最基础的代码就可以很快的出图，见图pca所示

    draw_pca(k,group_list) #k是转为mRNA的图，如果看全部的结果则为exp

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-03-tinyarray/tinyarray/pca-1.png" alt="TCGA-KICH中肿瘤与正常组织的PCA图"  />
<p class="caption">TCGA-KICH中肿瘤与正常组织的PCA图</p>
</div>

当然我们也可以添加各种参数，比如背景和配色等，这里可以在`draw_pca()`函数里更改颜色，也可以使用**ggplot2**添加背景和手动上色或者使用**ggsci**进行专业期刊上色，比如我们想改成theme_bw背景，然后换成lancet的配色，见图 pca2所示


```r
draw_pca(k, group_list) + ggplot2::theme_bw() + ggsci::scale_color_lancet()
```

```
## Scale for colour is already present.
## Adding another scale for colour, which will replace the existing scale.
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-03-tinyarray/tinyarray/pca2-1.png" alt="TCGA-KICH中肿瘤与正常组织的PCA图"  />
<p class="caption">TCGA-KICH中肿瘤与正常组织的PCA图</p>
</div>

------------------------------------------------------------------------

### 指定基因的表达量t检验

有时候，你得到了一个hub基因，你想知道哪些基因上调，哪些基因下调，这里基于t检验，给出结果

即给定一个基因列表，直接基于T检验返回表达量增加/减少/变化的基因，这时候我们可以用t_choose()函数。

比如我们随机生成20个基因列表


```r
hubgenes = sample(rownames(k), 20)
hubgenes
```

```
##  [1] "ZDHHC1"  "TEX33"   "NME7"    "FMO1"    "SLC43A1" "MGAT2"   "DNAAF2" 
##  [8] "F3"      "PRKAG3"  "OVOL3"   "PPP1R35" "RB1CC1"  "ERG"     "NMU"    
## [15] "RNF123"  "PPP2R5B" "USP45"   "CD247"   "CFAP61"  "OR6K2"
```

如果我们想看**显著上调**的基因，那么输入这个代码


```r
t_choose(hubgenes, k, group_list, up_only = T)
```

```
## [1] "ZDHHC1"  "SLC43A1" "RB1CC1"
```

如果我们想看**显著下调**的基因，那么输入这个代码


```r
t_choose(hubgenes, k, group_list, down_only = T)
```

```
## [1] "NME7"   "FMO1"   "MGAT2"  "DNAAF2" "F3"     "PRKAG3" "ERG"    "USP45" 
## [9] "CFAP61"
```

### 画t-SNE图

t-SNE其实是针对单细胞亚群的，但是也可以用基因表达矩阵和分组进行绘制，这里假装是单细胞分群，记住这里要先安装Rtsne，输入以下代码后可以见图 \@ref(fig:tsne)所示。


```r
# install.packages('Rtsne') #没有安装Rtsne的一定要先安装
draw_tsne(k, group_list, perplexity = 5)  #设置分群数字
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-03-tinyarray/tinyarray/tsne-1.png" alt="t-SNE图"  />
<p class="caption">t-SNE图</p>
</div>

### 画热图

画热图其实是一个比较复杂的过程，我们要设置显示的基因数，还要设置分组，有时候还要进行聚类和归一化等。**tinyarray**是调用**pheatmap**这个包实现的。这里有`draw_heatmap()`和`draw_heatmap2()`两个函数，一般情况下选择`draw_heatmap()`函数即可，`draw_heatmap2()`需要差异基因的支持。

输入以下最基础的代码就可以绘制精美的热图，见图 \@ref(fig:heat)所示，由于设计19712个基因，所以会很慢，我们可以少选一些基因进行绘图，比如提取前100个基因。这里不能实现选择差异最显著的基因进行热图，不过在建明老师的**Annoprobe**包倒是实现了。


```r
draw_heatmap(k[1:100, ], group_list)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-03-tinyarray/tinyarray/heat-1.png" alt="热图"  />
<p class="caption">热图</p>
</div>

当然我们也可以直接在这个函数里进行细节的修改，比如热图格子的配色和标签的配色，是否显示标签和基因等功能，比如我们做下面的个性化定制，结果见图 heat2所示.


```r
draw_heatmap(
  k[1:100,],
  group_list,
  scale_before = FALSE,
  n_cutoff = 3,
  cluster_cols = TRUE, # 按行聚类
  legend = T, #显示标签
  show_rownames = FALSE, #是否显示行名，也就是基因名，如果想显示就设置为T
  annotation_legend = T, #是否显示标签注释标签
  color = (colorRampPalette(c("blue", "white", "red")))(100), #设置颜色区间
  color_an = c('darkblue','firebrick'), #设置分组颜色，自己选择喜欢的配色
  scale = TRUE, #是否归一化
  main = "Heatmap" )#设置标题
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-03-tinyarray/tinyarray/heat2-1.png" alt="热图"  />
<p class="caption">热图</p>
</div>

------------------------------------------------------------------------

另外还有一个ggheat函数也可以画热图，可以自己试试，这里就不演示了。

    ggheat(t(exp),group_list,show_colnames = F,show_rownames = F)

------------------------------------------------------------------------

### 画火山图

关于火山图的绘制，其实就是计算差异表达基因，目前有limma，edgeR和DESeq2三种算法，而`draw_volcano()`函数支持这三种算法的结果绘制。这个包还有一个get_deg()可以计算差异基因，但是是基于GEO数据，这个可以后面再说。

而本示例数据是TCGA的count值，如果要进行差异表达分析，最好使用DSEeq2法，我们可以这样算


```r
library(DESeq2)  # 仅支持count输入
k <- round(k, digits = 0)  # 转换为整数
#### 第一步，构建DESeq2的DESeq对象
colData <- data.frame(row.names = colnames(k), group_list = group_list)
dds <- DESeqDataSetFromMatrix(countData = k, colData = colData, design = ~group_list)
#### 第二步，进行差异表达分析
dds2 <- DESeq(dds)
res <- results(dds2, contrast = c("group_list", "tumor", "normal"))  # 对照组在后 提取差异分析结果，tumor组对normal组的差异分析结果
resOrdered <- res[order(res$padj), ]
DEG <- as.data.frame(resOrdered)
DEG <- na.omit(DEG)  #去除NA值
```


|        | baseMean| log2FoldChange|  lfcSE|   stat| pvalue| padj|
|:-------|--------:|--------------:|------:|------:|------:|----:|
|IRX1    |    509.1|         -8.809| 0.3357| -26.24|      0|    0|
|SLC9A3  |   3201.1|         -7.373| 0.3012| -24.48|      0|    0|
|ABCA4   |    901.1|         -7.412| 0.3077| -24.08|      0|    0|
|CDHR5   |    738.8|         -7.638| 0.3198| -23.88|      0|    0|
|UGT3A1  |   1068.6|         -9.546| 0.4009| -23.81|      0|    0|
|TMEM207 |    219.3|        -11.016| 0.4666| -23.61|      0|    0|

------------------------------------------------------------------------

画热图其实是**ggplot2**的`geom_point`函数，我们使用的是DESeq2法，也是默认的算法，不需要更改pkg，而这个包里面pkg可以等于1,2,3,4，分别代表 "DESeq2","edgeR","limma(voom)"和"limma"算法。

我们运行最基本的代码，见图 \@ref(fig:vol)所示，标题处甚至已经给我们统计好了上下调的基因


```r
draw_volcano(DEG)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-03-tinyarray/tinyarray/vol-1.png" alt="火山图"  />
<p class="caption">火山图</p>
</div>

我们也可以自己定制更多的参数，比如要校正p值，设置p和FC的截断值和颜色等等，比如图 \@ref(fig:vol2)


```r
draw_volcano(
  DEG,
  lab ="Log2 Fold Changge", #x轴的标签，默认是跟随pkg自动更改
  pvalue_cutoff = 0.05,
  logFC_cutoff = 1,
  pkg = 1,
  adjust = T, #使用校正的p值
  symmetry = FALSE, #是否让两边对称，默认不对称
  color = c("#2874C5", "grey", "#f87669") #挑一个自己喜欢的配色
)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-03-tinyarray/tinyarray/vol2-1.png" alt="火山图"  />
<p class="caption">火山图</p>
</div>

------------------------------------------------------------------------

### 画韦恩图

韦恩图一般用于多个数据集的汇总，比如有个三个数据的数字或基因，我们想知道哪些有交集的时候，可以用韦恩图，一般用于三种不同算法的差异基因的交集。

我们随机生成三组数字，然后画三组交集的韦恩图，代码如下，见图venn


```r
x= list(Deseq2=sample(1:100,30), #列表Deseq2
         edgeR=sample(1:100,30),#列表edgeR
         limma=sample(1:100,30))#limma
tinyarray::draw_venn(x,"venn plot")
```

------------------------------------------------------------------------

### 画生存分析图

一般我们用KM生存分析图，这里是调用**survival**这个包进行统计，然后调用**survminer**这个包进行图片绘制

示例数据用survival包自带的lung数据，画生存分析曲线必须至少要有三组数据：生存时间、生存状态和分组

示例是演示按性别分组，看两组的生存差异，我们用`draw_KM()`函数做人，代码如下，见图KM


```r
s = survival::lung
draw_KM(meta = s, group_list = s$sex, event_col = "status")
```

```
## Warning in draw_KM(meta = s, group_list = s$sex, event_col = "status"):
## group_list was covert to factor
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-03-tinyarray/tinyarray/KM-1.png" alt="生存分析图"  />
<p class="caption">生存分析图</p>
</div>

但是由于底层被限制了，我们不能进行更进一步的参数设置，当然我们也可以修改底层代码，或者用survminer画图。如果自己画图，需要先计算fit，然后作图，我们演示一下不用这个包需要哪些步骤，最终见图KM2


```r
library(survival)
fit<-survfit(Surv(time,status)~sex,data = lung) #计算fit值
survminer::ggsurvplot(fit, #加载fit数据
                      palette = c("#2874C5", "#f87669"), #配色，等ggpubr
                      conf.int = T,conf.int.style='step', # 置信区间，按虚线显示
                      pval = T,pval.method = T, #现在p值和方法
                      surv.median.line = "hv", #显示中位时间
                      risk.table = T,risk.table.pos=c("in"), #显示危险表，并放在表格内
                      legend=c(0.85,.85),
                      title="KM plot",#设置标签的位置
                      ggtheme = ggplot2::theme_bw()) #设置一个主题
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-03-tinyarray/tinyarray/KM2-1.png" alt="KM生存分析图"  />
<p class="caption">KM生存分析图</p>
</div>

其实，这个包除了画生存曲线以外，还可以算最佳截断值和cox值，函数分别是`surv_KM`和`surv_cox`，教程在[**太好用了！批量生存分析，一步到位，还支持最佳截点\~**](https://www.jianshu.com/p/5b623561eb29)，可以自己跑一下。

### 画箱式图

TCGA的单基因生信分析步骤第一个图一般都是单基因在泛癌中的表达，很多都是用boxplot，这个也是支持的。`draw_boxplot()`函数就支持，比如我们要看矩阵k的前5个基因在不同分组的情况，代码如下，见图 \@ref(fig:box)


```r
draw_boxplot(k[1:5, ], group_list)
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2021-09-03-tinyarray/tinyarray/box-1.png" alt="箱式图"  />
<p class="caption">箱式图</p>
</div>

### 下载GEO数据

建明老师开发了**Annoprobe**包，可以实现很多功能，其中最大的贡献就是动用了腾讯服务器免费搭建了GEO数据平台，支持绝大多数的GEO数据集，但是不可能完全收录的，不过可以进行ID转换倒是很不错的功能。

这个包也是调用**Annoprobe**包，我就不演示了，大家可以自己看示例

`geo_download()可以`用来下载GEO数据

`get_deg()`可以用来计算GEO数据的差异表达基因分析，并且可以注释ID

`multi_deg()`可以进行多个差异表达分析

### 富集分析

这个是调用了Y叔的**clusterprofiler**包，可以实现简单的KEGG，当然首先要进行差异表达分析，

`quick_enrich()`可以用来进行快速的KEGG富集分析

`double_enrich()`可以用来分别对上、下调的基因进行柱状图的绘制

------------------------------------------------------------------------

基本这就是这个包的大部分功能，当然更多的是自己去发现，基本上这个包满足了大多数的需求，调用了N多的包，能够简单的满足需求，又不用过多的去计算，何乐不为呢
