---
title: 填充NA值为上一个数值
author: 欧阳松
date: '2022-02-26'
slug: fillna
categories:
  - 教程
tags:
  - NA
  - 填充
from_Rmd: yes
---

有时候处理Excel数据时会出现很多NA值，比如将多行数据合并的时候，导入R里面就会发现NA值，对于NA值的填充有很多办法，这里介绍自动填充为上一行的数值，使用的**tidyverse**包和**zoo**包分别演示，但我认为方便的是**zoo**包。

先构建一个数据


```r
td <- data.frame(State = c("NY", "", "", "OH", "", ""), Your = c(101:106), Name = c(5:6,
  "", 8:9, ""))
```


|State | Your|Name |
|:-----|----:|:----|
|NY    |  101|5    |
|      |  102|6    |
|      |  103|     |
|OH    |  104|8    |
|      |  105|9    |
|      |  106|     |

首先是**tidyverse**包，演示填充State列的NA值为上一个数据


```r
library(tidyverse)
td %>%
  mutate(State = na_if(State, "")) %>%
  fill(State)
```

```
##   State Your Name
## 1    NY  101    5
## 2    NY  102    6
## 3    NY  103     
## 4    OH  104    8
## 5    OH  105    9
## 6    OH  106
```

可以发现，State列的NA值已经填充为上一行的数据，但是Name列还差点意思，这时候我们需要用到**zoo**包，首先我们要将""替换成NA，然后使用**zoo**一步替换为上一数据（这里要注意，如果不将''替换成NA，将会填充不完全）

我们分别演示一下，首先是不替换NA，直接运行函数


```r
zoo::na.locf(td)
```

```
##   State Your Name
## 1    NY  101    5
## 2        102    6
## 3        103     
## 4    OH  104    8
## 5        105    9
## 6        106
```

可以发现一点都没有替换，接下来将''替换成NA


```r
td[td == ""] = NA
zoo::na.locf(td)
```

```
##   State Your Name
## 1    NY  101    5
## 2    NY  102    6
## 3    NY  103    6
## 4    OH  104    8
## 5    OH  105    9
## 6    OH  106    9
```

完美解决所有列的NA值
