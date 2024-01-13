---
title: R语言实战临床预测模型
author: 欧阳松
date: '2024-01-13'
slug: r_clinical_model
categories:
  - R
  - 临床预测模型
tags:
  - R
  - 临床预测
  - 预测模型
---

推荐一本**bookdown**制作的电子书**R语言实战临床预测模型**

-   浏览地址是：<https://ayueme.github.io/R_clinical_model/>

-   本书github地址：[https://github.com/ayueme/R_clinical_model](https://hub.fgit.cf/ayueme/R_clinical_model)

## 书籍简介

本书主要介绍R语言在临床预测模型中的应用，重实践，少理论，全书只有少量内容是理论，其余部分都是R语言实操。

临床预测模型和统计学以及机器学习交叉很多，本书虽然是R语言实现临床预测模型的入门书籍，但在阅读本书前，还需要你已经对**临床预测模型、统计学、机器学习**具有一定的了解。

> 本书不适合R语言零基础的人。
>
> 如果你是刚入门的小白，我首先推荐你了解下R语言的基础知识，比如R语言和R包安装（初学者可参考附录）、Rstudio的界面、R语言中数据类型（向量、矩阵、数据框、列表等）、R语言中的数据导入导出、R语言的基础数据操作等。
>
> 对于医学生/医生，我比较推荐先看《R语言实战》，再看《R数据科学》，无需全部了解，只需要熟悉其中的基础知识即可。

本书内容主要涉及**模型建立、模型评价、模型比较**3部分内容，其中模型建立和模型评价内容占比较多，模型比较部分主要是几个综合性R包的使用，简化多模型比较的流程。**变量筛选**内容较多，我把它单独放在一个章节中。对于临床预测模型中常见的列线图、C-index、ROC曲线、校准曲线、决策曲线、临床影响曲线、NRI、IDI等内容，皆进行了详细的操作演示，同时提供多种实现方法。

对于一些比较火爆的机器学习方法，如[随机生存森林](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzUzOTQzNzU0NA==&action=getalbum&album_id=2699591889800560640&from_itemidx=1&from_msgid=2247500351&scene=173&subscene=&sessionid=svr_c7db21a769f&enterid=1703484864&count=3&nolastread=1#wechat_redirect)、**生存支持向量机、提升法、神经网络**等内容，本书并未进行介绍，公众号会逐步更新这部分内容（随机生存森林、生存支持向量机已完结），需要的朋友可关注公众号：医学和生信笔记。

> 本书实际上是**医学和生信笔记**公众号历史推文的整理和汇总（部分内容有改动），**书中涉及的所有数据都可以在相关历史推文中免费获取！**推文合集链接：[临床预测模型](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzUzOTQzNzU0NA==&action=getalbum&album_id=2393825487539191816&scene=173&from_msgid=2247495829&from_itemidx=1&count=3&nolastread=1#wechat_redirect)
>
> 作者也准备了一个PDF版合集，内容和网页版一致，只是打包了所有的数据，付费获取（10元），介意勿扰！**PDF版合集获取链接**：[R语言实战临床预测模型](https://mp.weixin.qq.com/s/Sx7onA339TYSxIF6Bi6JTw)

## 作者简介

-   阿越，外科医生，R语言爱好者，长期分享R语言和医学统计学、临床预测模型、生信数据挖掘、R语言机器学习等知识。
-   公众号：**医学和生信笔记**
-   知乎：[医学和生信笔记](https://www.zhihu.com/people/li-xiao-yue-65-90)
-   CSDN：[医学和生信笔记](https://blog.csdn.net/Ayue0616)
-   哔哩哔哩：[阿越就是我](https://space.bilibili.com/42460432)
-   Github：[ayueme](https://github.com/ayueme)

## 本书目录

### 模型建立

-   [1  什么是临床预测模型？](https://ayueme.github.io/R_clinical_model/clinmodel-definition.html)

-   [2  logistic回归列线图绘制](https://ayueme.github.io/R_clinical_model/nomogram-logistic.html)

-   [3  Cox回归列线图绘制](https://ayueme.github.io/R_clinical_model/nomogram-cox.html)

-   [4  列线图的本质](https://ayueme.github.io/R_clinical_model/nomogram-essential.html)

-   [5  样条回归列线图绘制](https://ayueme.github.io/R_clinical_model/nomogram-rcs.html)

-   [6  竞争风险模型列线图绘制](https://ayueme.github.io/R_clinical_model/nomogram-compete-risk.html)

-   [7  lasso回归列线图绘制](https://ayueme.github.io/R_clinical_model/nomogram-lasso.html)

-   [8  列线图添加彩色风险分层](https://ayueme.github.io/R_clinical_model/nomogram-colorfulbar.html)

-   [9  计算列线图得分及危险分层](https://ayueme.github.io/R_clinical_model/nomogram-points.html)

### 变量筛选

-   [10  常见的变量选择方法](https://ayueme.github.io/R_clinical_model/feature-selection.html)

-   [11  变量筛选之先单后多](https://ayueme.github.io/R_clinical_model/feature-selection_unimulti.html)

-   [12  筛选变量逐步回归](https://ayueme.github.io/R_clinical_model/feature-selection_stepwise.html)

-   [13  变量筛选之最优子集](https://ayueme.github.io/R_clinical_model/feature-selection_bestsubset.html)

-   [14  lasso回归筛选变量](https://ayueme.github.io/R_clinical_model/feature-selection_lasso.html)

-   [15  变量筛选之随机森林](https://ayueme.github.io/R_clinical_model/feature-selection_randomforest.html)

### 模型评价

-   [16  临床预测模型的评价方法](https://ayueme.github.io/R_clinical_model/clinmodel-evalution.html)

-   [17  二分类资料ROC曲线绘制](https://ayueme.github.io/R_clinical_model/roc-binominal.html)

-   [18  生存资料ROC曲线绘制](https://ayueme.github.io/R_clinical_model/roc-survive.html)

-   [19  ROC曲线的最佳截点](https://ayueme.github.io/R_clinical_model/roc-bestcut.html)

-   [20  平滑ROC曲线](https://ayueme.github.io/R_clinical_model/roc-smooth.html)

-   [21  ROC曲线的显著性检验](https://ayueme.github.io/R_clinical_model/roc-compare.html)

-   [22  R语言计算AUC(ROC)注意事项](https://ayueme.github.io/R_clinical_model/roc-attention.html)

-   [23  多指标联合诊断的ROC曲线](https://ayueme.github.io/R_clinical_model/roc-many.html)

-   [24  bootstrap ROC/AUC](https://ayueme.github.io/R_clinical_model/roc-bootstrap.html)

-   [25  多分类数据的ROC曲线](https://ayueme.github.io/R_clinical_model/%E5%A4%9A%E5%88%86%E7%B1%BB%E6%95%B0%E6%8D%AE%E7%9A%84ROC%E6%9B%B2%E7%BA%BF.html)

-   [26  C-index的计算](https://ayueme.github.io/R_clinical_model/cindex.html)

-   [27  C-index的比较](https://ayueme.github.io/R_clinical_model/cindex-compare.html)

-   [28  三维混淆矩阵](https://ayueme.github.io/R_clinical_model/conf_matrix-3d.html)

-   [29  NRI净重新分类指数](https://ayueme.github.io/R_clinical_model/nri.html)

-   [30  IDI综合判别改善指数](https://ayueme.github.io/R_clinical_model/idi.html)

-   [31  logistic回归校准曲线绘制](https://ayueme.github.io/R_clinical_model/calibration-logistic.html)

-   [32  logistic回归测试集校准曲线的绘制](https://ayueme.github.io/R_clinical_model/calibration-logistic-test.html)

-   [33  Cox回归校准曲线绘制](https://ayueme.github.io/R_clinical_model/calibration-cox.html)

-   [34  Cox回归测试集校准曲线绘制](https://ayueme.github.io/R_clinical_model/calibration-cox-test.html)

-   [35  tidymodels手动绘制校准曲线](https://ayueme.github.io/R_clinical_model/calibration-tidymodels-man.html)

-   [36  tidymodels已原生支持校准曲线](https://ayueme.github.io/R_clinical_model/calibration-tidymodels.html)

-   [37  mlr3绘制校准曲线](https://ayueme.github.io/R_clinical_model/calibration-mlr3.html)

-   [38  竞争风险模型的校准曲线](https://ayueme.github.io/R_clinical_model/calibration-qhscrnomo.html)

-   [39  lasso回归列线图、校准曲线、内外部验证](https://ayueme.github.io/R_clinical_model/calibration-lasso.html)

-   [40  分类数据的决策曲线分析](https://ayueme.github.io/R_clinical_model/dca-logistic.html)

-   [41  生存数据的决策曲线分析](https://ayueme.github.io/R_clinical_model/dca-cox.html)

-   [42  适用于一切模型的决策曲线分析](https://ayueme.github.io/R_clinical_model/dca-diy.html)

-   [43  决策曲线添加彩色条带](https://ayueme.github.io/R_clinical_model/DCA%E5%BD%A9%E8%89%B2%E6%9D%A1%E5%B8%A6.html)

-   [44  测试集的决策曲线分析](https://ayueme.github.io/R_clinical_model/DCA%E6%B5%8B%E8%AF%95%E9%9B%86.html)

-   [45  校准曲线和决策曲线的概率](https://ayueme.github.io/R_clinical_model/%E6%A0%A1%E5%87%86%E6%9B%B2%E7%BA%BF%E5%92%8C%E5%86%B3%E7%AD%96%E6%9B%B2%E7%BA%BF%E7%9A%84%E6%A6%82%E7%8E%87.html)

### 模型比较

-   [46  tidymodels实现多模型比较](https://ayueme.github.io/R_clinical_model/model-compare_tidymodels.html)

-   [47  workflow实现多模型比较](https://ayueme.github.io/R_clinical_model/model-compare_workflow.html)

-   [48  mlr3实现多模型比较](https://ayueme.github.io/R_clinical_model/model-compare_mlr3.html)

-   [49  caret实现多模型比较](https://ayueme.github.io/R_clinical_model/model-compare_caret.html)

### 附录

-   [A  常见的数据预处理方法](https://ayueme.github.io/R_clinical_model/data-preprocess.html)

-   [B  常见的数据划分方法](https://ayueme.github.io/R_clinical_model/data-split.html)

-   [C  其他合集](https://ayueme.github.io/R_clinical_model/9999-appendix.html)
