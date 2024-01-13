---
title: R语言实战医学统计
author: 阿越就是我
date: '2024-01-13'
slug: R_medical_stat
categories:
  - R
  - 医学统计
  - R语言实战
tags:
  - R
  - 临床预测
  - 医学统计
  - R语言实战
---

推荐一本**bookdown**制作的电子书**R语言实战医学统计**

-   浏览地址是：<https://ayueme.github.io/R_medical_stat/>

-   本书github地址：[https://github.com/ayueme/R_medical_stat](https://hub.fgit.cf/ayueme/R_medical_stat)

## 书籍简介

R语言是一门编程语言，但同时也是一个统计软件，R语言是由统计学家开发的，所以天生就适合做统计。

很多刚接触R语言的朋友不知道如何入手，只知道目前R语言在临床医学领域很火爆，做统计分析、画图、做生信分析、孟德尔随机化、数据库挖掘等都离不开R语言。

万事开头难，我非常理解新手面对R语言的痛苦，因为我也是从0开始的，作为从未接触过编程的医学生/医生来说，初学R语言简直就是读天书！我最开始接触R语言是因为偶然间听师兄师姐说R语言可以做统计学，当时的我对SPSS的使用不熟练，觉得SPSS的使用步骤太多，难以记住，于是入了R语言的坑...没想到从此一发不可收拾，打开了新世界的大门。

> 本书不适合R语言零基础的人。
>
> 如果你是刚入门的小白，我首先推荐你了解下R语言的基础知识，比如R语言和R包安装（初学者可参考附录）、Rstudio的界面、R语言中数据类型（向量、矩阵、数据框、列表等）、R语言中的数据导入导出、R语言的基础数据操作等。
>
> 对于医学生/医生，我比较推荐先看《R语言实战》，再看《R数据科学》，无需全部了解，只需要熟悉其中的基础知识即可。

然后你就可以跟着本系列一起学习R语言在医学统计学中的使用。这个系列非常适合初学者，因为是按照课本来的，使用R语言复现课本中的例题，得到结果后可以与课本对照！我使用的课本是孙振球主编的《医学统计学》第4版（第5版和第4版内容变化不大），封面如下：

![](https://ayueme.github.io/R_medical_stat/figs/Snipaste_2023-04-05_12-46-31.png)

由于R和SPSS在进行统计分析时的一些数学计算方面并不是完全一致，所以导致有些结果和课本中的结果有些出入，但是并不影响结果的正确性。

## 作者简介

-   阿越，外科医生，R语言爱好者，长期分享R语言和医学统计学、临床预测模型、生信数据挖掘、R语言机器学习等知识。
-   公众号：**医学和生信笔记**
-   知乎：[医学和生信笔记](https://www.zhihu.com/people/li-xiao-yue-65-90)
-   CSDN：[医学和生信笔记](https://blog.csdn.net/Ayue0616)
-   哔哩哔哩：[阿越就是我](https://space.bilibili.com/42460432)
-   Github：[ayueme](https://github.com/ayueme)

## 本书目录

### 基础统计分析

-   [1  t检验](https://ayueme.github.io/R_medical_stat/1001-ttest.html)

-   [2  多样本均数比较的方差分析](https://ayueme.github.io/R_medical_stat/1002-anova.html)

-   [3  多因素方差分析](https://ayueme.github.io/R_medical_stat/1003-dysanova.html)

-   [4  重复测量方差分析](https://ayueme.github.io/R_medical_stat/1004-repeatedanova.html)

-   [5  协方差分析](https://ayueme.github.io/R_medical_stat/1005-ancova.html)

-   [6  卡方检验](https://ayueme.github.io/R_medical_stat/1006-chisq.html)

-   [7  秩和检验](https://ayueme.github.io/R_medical_stat/1007-wilcoxon.html)

-   [8  球对称检验](https://ayueme.github.io/R_medical_stat/1008-mauchly.html)

-   [9  R语言Cochran Armitage检验](https://ayueme.github.io/R_medical_stat/1009-cochranarmitage.html)

-   [10  R语言方差分析注意事项](https://ayueme.github.io/R_medical_stat/1010-anovaattention.html)

-   [11  样本量计算](https://ayueme.github.io/R_medical_stat/1011-samplesize.html)

-   [12  随机分组](https://ayueme.github.io/R_medical_stat/1012-randomgroup.html)

-   [13  R语言tidy风格医学统计学](https://ayueme.github.io/R_medical_stat/1013-rstatix.html)

-   [14  R语言多个变量同时进行t检验](https://ayueme.github.io/R_medical_stat/1014-batchttest.html)

-   [15  双变量回归与相关](https://ayueme.github.io/R_medical_stat/1015-twocorrelation.html)

-   [16  偏相关和典型相关](https://ayueme.github.io/R_medical_stat/1016-partialcorrelation.html)

### 高级统计分析

-   [17  多元线性回归](https://ayueme.github.io/R_medical_stat/1017-multireg.html)

-   [18  Logistic回归](https://ayueme.github.io/R_medical_stat/1018-logistic.html)

-   [19  分类变量进行回归分析时的编码方案](https://ayueme.github.io/R_medical_stat/1019-codescheme.html)

-   [20  R语言判别分析](https://ayueme.github.io/R_medical_stat/1020-discriminant.html)

-   [21  R语言聚类分析](https://ayueme.github.io/R_medical_stat/1021-cluster.html)

-   [22  R语言主成分分析](https://ayueme.github.io/R_medical_stat/1022-pca.html)

-   [23  R语言因子分析](https://ayueme.github.io/R_medical_stat/1023-factoranalysis.html)

-   [24  二分类资料ROC曲线绘制](https://ayueme.github.io/R_medical_stat/roc-binominal.html)

-   [25  生存资料ROC曲线绘制](https://ayueme.github.io/R_medical_stat/roc-survive.html)

-   [26  ROC曲线的最佳截点](https://ayueme.github.io/R_medical_stat/roc-bestcut.html)

-   [27  平滑ROC曲线](https://ayueme.github.io/R_medical_stat/roc-smooth.html)

-   [28  ROC曲线的显著性检验](https://ayueme.github.io/R_medical_stat/roc-compare.html)

-   [29  R语言计算AUC(ROC)注意事项](https://ayueme.github.io/R_medical_stat/roc-attention.html)

-   [30  多指标联合诊断的ROC曲线](https://ayueme.github.io/R_medical_stat/roc-many.html)

-   [31  bootstrap ROC/AUC](https://ayueme.github.io/R_medical_stat/roc-bootstrap.html)

-   [32  多分类数据的ROC曲线](https://ayueme.github.io/R_medical_stat/%E5%A4%9A%E5%88%86%E7%B1%BB%E6%95%B0%E6%8D%AE%E7%9A%84ROC%E6%9B%B2%E7%BA%BF.html)

-   [33  R语言生存分析](https://ayueme.github.io/R_medical_stat/1032-survival.html)

-   [34  R语言生存曲线可视化](https://ayueme.github.io/R_medical_stat/1033-survivalvis.html)

### 文献常见统计分析

-   [35  Fine-Gray检验和竞争风险模型列线图](https://ayueme.github.io/R_medical_stat/1034-finegray.html)

-   [36  R语言倾向性评分：匹配](https://ayueme.github.io/R_medical_stat/1035-psm.html)

-   [37  R语言倾向性评分：回归和分层](https://ayueme.github.io/R_medical_stat/1036-pssc.html)

-   [38  R语言倾向性评分：加权](https://ayueme.github.io/R_medical_stat/1037-psw.html)

-   [39  p-for-trend/ p-for-interaction/ per-1-sd R语言实现](https://ayueme.github.io/R_medical_stat/1038-p4trend.html)

-   [40  R语言多项式拟合](https://ayueme.github.io/R_medical_stat/1039-nonlinear.html)

-   [41  R语言样条回归](https://ayueme.github.io/R_medical_stat/1040-rcs.html)

-   [42  R语言亚组分析及森林图绘制](https://ayueme.github.io/R_medical_stat/1041-subgroupanalysis.html)

-   [43  亚组分析1行代码实现](https://ayueme.github.io/R_medical_stat/1042-subgroup1code.html)

-   [44  亚组分析和多因素回归的森林图](https://ayueme.github.io/R_medical_stat/%E4%BA%9A%E7%BB%84%E5%88%86%E6%9E%90%E5%92%8C%E5%A4%9A%E5%9B%A0%E7%B4%A0%E5%9B%9E%E5%BD%92%E7%9A%84%E6%A3%AE%E6%9E%97%E5%9B%BE.html)

-   [45  compareGroups绘制三线表](https://ayueme.github.io/R_medical_stat/comparegroups.html)

-   [46  tableone绘制三线表](https://ayueme.github.io/R_medical_stat/tableone.html)

-   [47  table1绘制三线表](https://ayueme.github.io/R_medical_stat/table1.html)

-   [48  gt绘制表格](https://ayueme.github.io/R_medical_stat/gt.html)

-   [49  gtExtras美化表格](https://ayueme.github.io/R_medical_stat/gtExtra.html)

-   [50  gtsummary绘制表格](https://ayueme.github.io/R_medical_stat/gtsummary.html)

### 附录

-   [A  其他合集](https://ayueme.github.io/R_medical_stat/9999-appendix.html)
