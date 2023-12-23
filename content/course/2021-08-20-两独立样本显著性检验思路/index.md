---
title: 两独立样本显著性检验思路
author: 欧阳松
date: '2021-08-20'
slug: 两独立样本显著性检验思路
categories:
  - R
  - 统计学
tags:
  - Tutorial
---

# 两独立样本显著性检验

## 正态性检验

### 正态分布

-   方差齐性检验

    -   方差具有齐性

        -   student-t检验

            -   双尾假设

            -   单尾假设（less或greater）

    -   方差不具有齐性

        -   近似t检验（t）

            -   双尾假设

            -   单尾假设（less或greater）

### 非正态分布

-   普通情况

    -   非参数检验（Wilcoxon）

        -   双尾假设

        -   单尾假设（less或greater）

-   经数据log转换后呈正态分布

    -   可酌情使用t检验或t

        -   双尾假设

        -   单尾假设（less或greater）
