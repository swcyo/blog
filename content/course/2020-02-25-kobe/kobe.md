---
title: 科比投篮数据分析
date: '2020-02-25'
slug: kobe
categories:
  - kobe
  - R
tags:
  - R
from_Rmd: yes
---

> 原文来自 <https://www.kaggle.com/xvivancos/kobe-bryant-shot-selection/report>
>
> **科比-布拉恩特** (Kobe Bryant，1978年8月23日-2020年1月26日)是我们80后每个篮球爱好者喜爱的球员，当年没少追，可惜英年早逝，只能用R来分析一生数据，以此纪念！

<center><img src="http://i.imgur.com/xrOu1.png"/></center>

<p style="font-family: times, serif; font-size:18pt; font-style:italic">
“Everything negative – pressure, challenges – is all an opportunity for me to rise.”
</p>
<div style="text-align: right"> **Kobe Bryant** </div>

# **介绍**

我是一个篮球迷，在这个项目中，我们将分析来自 Kaggle的一个数据集，其中包含科比-布莱恩特在其 20 年职业生涯中尝试的每个外线投篮的位置和情况。我们将使用 [tidyverse](https://cran.r-project.org/web/packages/tidyverse/index.html) 软件包进行数据操作、探索和可视化。

> 科比-布莱恩特（Kobe Bean Bryant，生于1978年8月23日），美国前职业篮球运动员。他在美国国家篮球协会（NBA）的洛杉矶湖人队效力了整整20年。他从高中毕业后直接进入 NBA，并为湖人队赢得了5次 NBA 总冠军。布莱恩特18次入选全明星，15次入选全美NBA阵容，12次入选全美防守阵容。他曾两个赛季领跑 NBA 总得分榜，在联盟常规赛总得分榜上排名第三，在季后赛总得分榜上排名第四。他保持着在整个职业生涯中为一家特许经营公司效力赛季数最多的NBA纪录，被公认为史上最伟大的篮球运动员之一。布莱恩特是NBA历史上第一位至少打了20个赛季的后卫。

顺便说一句，如果你喜欢篮球，还可以看看其他的内容：

-   [Michael Jordan vs Kobe Bryant vs Lebron James](https://www.kaggle.com/xvivancos/michael-jordan-vs-kobe-bryant-vs-lebron-james)

-   [EDA - Tweets during Cleveland Cavaliers vs Golden State Warriors](https://www.kaggle.com/xvivancos/eda-tweets-during-cavaliers-vs-warriors)

-   [How good is Luka Doncic?](https://www.kaggle.com/xvivancos/how-good-is-luka-doncic)

# **加载数据** 


```r
# 加载需要的包
library(tidyverse)
library(gridExtra)
library(knitr)
library(ggplot2)
library(ggpubr)
```

# 阅读统计数据

```         
shots <- read.csv("../input/kobe-bryant-shot-selection/data.csv")
```



让我们了解一下我们需要开始的工作。

## 数据结构


```r
# 数据结构
str(shots)
```

```
## 'data.frame':	30697 obs. of  25 variables:
##  $ action_type       : chr  "Jump Shot" "Jump Shot" "Jump Shot" "Jump Shot" ...
##  $ combined_shot_type: chr  "Jump Shot" "Jump Shot" "Jump Shot" "Jump Shot" ...
##  $ game_event_id     : int  10 12 35 43 155 244 251 254 265 294 ...
##  $ game_id           : int  20000012 20000012 20000012 20000012 20000012 20000012 20000012 20000012 20000012 20000012 ...
##  $ lat               : num  34 34 33.9 33.9 34 ...
##  $ loc_x             : int  167 -157 -101 138 0 -145 0 1 -65 -33 ...
##  $ loc_y             : int  72 0 135 175 0 -11 0 28 108 125 ...
##  $ lon               : num  -118 -118 -118 -118 -118 ...
##  $ minutes_remaining : int  10 10 7 6 6 9 8 8 6 3 ...
##  $ period            : int  1 1 1 1 2 3 3 3 3 3 ...
##  $ playoffs          : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ season            : chr  "2000-01" "2000-01" "2000-01" "2000-01" ...
##  $ seconds_remaining : int  27 22 45 52 19 32 52 5 12 36 ...
##  $ shot_distance     : int  18 15 16 22 0 14 0 2 12 12 ...
##  $ shot_made_flag    : int  NA 0 1 0 1 0 1 NA 1 0 ...
##  $ shot_type         : chr  "2PT Field Goal" "2PT Field Goal" "2PT Field Goal" "2PT Field Goal" ...
##  $ shot_zone_area    : chr  "Right Side(R)" "Left Side(L)" "Left Side Center(LC)" "Right Side Center(RC)" ...
##  $ shot_zone_basic   : chr  "Mid-Range" "Mid-Range" "Mid-Range" "Mid-Range" ...
##  $ shot_zone_range   : chr  "16-24 ft." "8-16 ft." "16-24 ft." "16-24 ft." ...
##  $ team_id           : int  1610612747 1610612747 1610612747 1610612747 1610612747 1610612747 1610612747 1610612747 1610612747 1610612747 ...
##  $ team_name         : chr  "Los Angeles Lakers" "Los Angeles Lakers" "Los Angeles Lakers" "Los Angeles Lakers" ...
##  $ game_date         : chr  "2000-10-31" "2000-10-31" "2000-10-31" "2000-10-31" ...
##  $ matchup           : chr  "LAL @ POR" "LAL @ POR" "LAL @ POR" "LAL @ POR" ...
##  $ opponent          : chr  "POR" "POR" "POR" "POR" ...
##  $ shot_id           : int  1 2 3 4 5 6 7 8 9 10 ...
```

## 数据总结


```r
# 数据总结
summary(shots)
```

```
##  action_type        combined_shot_type game_event_id    game_id        
##  Length:30697       Length:30697       Min.   :  2   Min.   :20000012  
##  Class :character   Class :character   1st Qu.:110   1st Qu.:20500077  
##  Mode  :character   Mode  :character   Median :253   Median :20900354  
##                                        Mean   :249   Mean   :24764066  
##                                        3rd Qu.:368   3rd Qu.:29600474  
##                                        Max.   :659   Max.   :49900088  
##                                                                        
##       lat           loc_x             loc_y            lon      
##  Min.   :33.2   Min.   :-250.00   Min.   :-44.0   Min.   :-119  
##  1st Qu.:33.9   1st Qu.: -68.00   1st Qu.:  4.0   1st Qu.:-118  
##  Median :34.0   Median :   0.00   Median : 74.0   Median :-118  
##  Mean   :34.0   Mean   :   7.11   Mean   : 91.1   Mean   :-118  
##  3rd Qu.:34.0   3rd Qu.:  95.00   3rd Qu.:160.0   3rd Qu.:-118  
##  Max.   :34.1   Max.   : 248.00   Max.   :791.0   Max.   :-118  
##                                                                 
##  minutes_remaining     period        playoffs        season         
##  Min.   : 0.00     Min.   :1.00   Min.   :0.000   Length:30697      
##  1st Qu.: 2.00     1st Qu.:1.00   1st Qu.:0.000   Class :character  
##  Median : 5.00     Median :3.00   Median :0.000   Mode  :character  
##  Mean   : 4.89     Mean   :2.52   Mean   :0.147                     
##  3rd Qu.: 8.00     3rd Qu.:3.00   3rd Qu.:0.000                     
##  Max.   :11.00     Max.   :7.00   Max.   :1.000                     
##                                                                     
##  seconds_remaining shot_distance  shot_made_flag  shot_type        
##  Min.   : 0.0      Min.   : 0.0   Min.   :0      Length:30697      
##  1st Qu.:13.0      1st Qu.: 5.0   1st Qu.:0      Class :character  
##  Median :28.0      Median :15.0   Median :0      Mode  :character  
##  Mean   :28.4      Mean   :13.4   Mean   :0                        
##  3rd Qu.:43.0      3rd Qu.:21.0   3rd Qu.:1                        
##  Max.   :59.0      Max.   :79.0   Max.   :1                        
##                                   NA's   :5000                     
##  shot_zone_area     shot_zone_basic    shot_zone_range       team_id        
##  Length:30697       Length:30697       Length:30697       Min.   :1.61e+09  
##  Class :character   Class :character   Class :character   1st Qu.:1.61e+09  
##  Mode  :character   Mode  :character   Mode  :character   Median :1.61e+09  
##                                                           Mean   :1.61e+09  
##                                                           3rd Qu.:1.61e+09  
##                                                           Max.   :1.61e+09  
##                                                                             
##   team_name          game_date           matchup            opponent        
##  Length:30697       Length:30697       Length:30697       Length:30697      
##  Class :character   Class :character   Class :character   Class :character  
##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
##                                                                             
##                                                                             
##                                                                             
##                                                                             
##     shot_id     
##  Min.   :    1  
##  1st Qu.: 7675  
##  Median :15349  
##  Mean   :15349  
##  3rd Qu.:23023  
##  Max.   :30697  
## 
```

## 数据头


```r
# 查看前 6 行
head(shots)
```

```
##         action_type combined_shot_type game_event_id  game_id   lat loc_x loc_y
## 1         Jump Shot          Jump Shot            10 20000012 33.97   167    72
## 2         Jump Shot          Jump Shot            12 20000012 34.04  -157     0
## 3         Jump Shot          Jump Shot            35 20000012 33.91  -101   135
## 4         Jump Shot          Jump Shot            43 20000012 33.87   138   175
## 5 Driving Dunk Shot               Dunk           155 20000012 34.04     0     0
## 6         Jump Shot          Jump Shot           244 20000012 34.06  -145   -11
##      lon minutes_remaining period playoffs  season seconds_remaining
## 1 -118.1                10      1        0 2000-01                27
## 2 -118.4                10      1        0 2000-01                22
## 3 -118.4                 7      1        0 2000-01                45
## 4 -118.1                 6      1        0 2000-01                52
## 5 -118.3                 6      2        0 2000-01                19
## 6 -118.4                 9      3        0 2000-01                32
##   shot_distance shot_made_flag      shot_type        shot_zone_area
## 1            18             NA 2PT Field Goal         Right Side(R)
## 2            15              0 2PT Field Goal          Left Side(L)
## 3            16              1 2PT Field Goal  Left Side Center(LC)
## 4            22              0 2PT Field Goal Right Side Center(RC)
## 5             0              1 2PT Field Goal             Center(C)
## 6            14              0 2PT Field Goal          Left Side(L)
##   shot_zone_basic shot_zone_range    team_id          team_name  game_date
## 1       Mid-Range       16-24 ft. 1610612747 Los Angeles Lakers 2000-10-31
## 2       Mid-Range        8-16 ft. 1610612747 Los Angeles Lakers 2000-10-31
## 3       Mid-Range       16-24 ft. 1610612747 Los Angeles Lakers 2000-10-31
## 4       Mid-Range       16-24 ft. 1610612747 Los Angeles Lakers 2000-10-31
## 5 Restricted Area Less Than 8 ft. 1610612747 Los Angeles Lakers 2000-10-31
## 6       Mid-Range        8-16 ft. 1610612747 Los Angeles Lakers 2000-10-31
##     matchup opponent shot_id
## 1 LAL @ POR      POR       1
## 2 LAL @ POR      POR       2
## 3 LAL @ POR      POR       3
## 4 LAL @ POR      POR       4
## 5 LAL @ POR      POR       5
## 6 LAL @ POR      POR       6
```

## 数据尾


```r
# 查看后 6 行
tail(shots)
```

```
##              action_type combined_shot_type game_event_id  game_id   lat loc_x
## 30692 Driving Layup Shot              Layup           382 49900088 34.04     0
## 30693          Jump Shot          Jump Shot           397 49900088 34.00     1
## 30694           Tip Shot           Tip Shot           398 49900088 34.04     0
## 30695  Running Jump Shot          Jump Shot           426 49900088 33.88  -134
## 30696          Jump Shot          Jump Shot           448 49900088 33.78    31
## 30697          Jump Shot          Jump Shot           471 49900088 33.97     1
##       loc_y    lon minutes_remaining period playoffs  season seconds_remaining
## 30692     0 -118.3                 7      4        1 1999-00                 4
## 30693    48 -118.3                 6      4        1 1999-00                 5
## 30694     0 -118.3                 6      4        1 1999-00                 5
## 30695   166 -118.4                 3      4        1 1999-00                28
## 30696   267 -118.2                 2      4        1 1999-00                10
## 30697    72 -118.3                 0      4        1 1999-00                39
##       shot_distance shot_made_flag      shot_type       shot_zone_area
## 30692             0              0 2PT Field Goal            Center(C)
## 30693             4              0 2PT Field Goal            Center(C)
## 30694             0             NA 2PT Field Goal            Center(C)
## 30695            21              1 2PT Field Goal Left Side Center(LC)
## 30696            26              0 3PT Field Goal            Center(C)
## 30697             7              0 2PT Field Goal            Center(C)
##             shot_zone_basic shot_zone_range    team_id          team_name
## 30692       Restricted Area Less Than 8 ft. 1610612747 Los Angeles Lakers
## 30693 In The Paint (Non-RA) Less Than 8 ft. 1610612747 Los Angeles Lakers
## 30694       Restricted Area Less Than 8 ft. 1610612747 Los Angeles Lakers
## 30695             Mid-Range       16-24 ft. 1610612747 Los Angeles Lakers
## 30696     Above the Break 3         24+ ft. 1610612747 Los Angeles Lakers
## 30697 In The Paint (Non-RA) Less Than 8 ft. 1610612747 Los Angeles Lakers
##        game_date     matchup opponent shot_id
## 30692 2000-06-19 LAL vs. IND      IND   30692
## 30693 2000-06-19 LAL vs. IND      IND   30693
## 30694 2000-06-19 LAL vs. IND      IND   30694
## 30695 2000-06-19 LAL vs. IND      IND   30695
## 30696 2000-06-19 LAL vs. IND      IND   30696
## 30697 2000-06-19 LAL vs. IND      IND   30697
```

在 `shot_made_flag` 列中有一些 NA。我们可以使用 `na.omit()` 函数删除所有有缺失值的行。


```r
# 删除有 NA 的行
shots <- na.omit(shots)
```

# **数据分析**

## 投篮类型


```r
# 投篮类型
ggplot() + geom_point(data = shots %>%
  filter(combined_shot_type == "Jump Shot"), aes(x = lon, y = lat), colour = "grey",
  alpha = 0.3) + geom_point(data = shots %>%
  filter(combined_shot_type != "Jump Shot"), aes(x = lon, y = lat, colour = combined_shot_type),
  alpha = 0.8) + labs(title = "Shot type") + ylim(c(33.7, 34.0883)) + theme_void() +
  theme(legend.title = element_blank(), plot.title = element_text(hjust = 0.5))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-8-1.png" alt="plot of chunk unnamed-chunk-8"  />
<p class="caption">plot of chunk unnamed-chunk-8</p>
</div>

我们可以看到，可视化中的大多数点都与跳投（jump shots）相关。

## 投篮区域


```r
# 投篮区域
p1 <- ggplot(shots, aes(x = lon, y = lat)) + geom_point(aes(color = shot_zone_range)) +
  labs(title = "Shot zone range") + ylim(c(33.7, 34.0883)) + theme_void() + theme(legend.position = "none",
  plot.title = element_text(hjust = 0.5))

# 各射区范围的频率
p2 <- ggplot(shots, aes(x = fct_infreq(shot_zone_range))) + geom_bar(aes(fill = shot_zone_range)) +
  labs(y = "Frequency") + theme_bw() + theme(axis.title.x = element_blank(), legend.position = "none")

# 拼图
grid.arrange(p1, p2, layout_matrix = cbind(c(1, 2)))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-9-1.png" alt="plot of chunk unnamed-chunk-9"  />
<p class="caption">plot of chunk unnamed-chunk-9</p>
</div>


```r
# 投篮区域面积
p3 <- ggplot(shots, aes(x = lon, y = lat)) + geom_point(aes(colour = shot_zone_area)) +
  labs(title = "Shot zone area") + ylim(c(33.7, 34.0883)) + theme_void() + theme(legend.position = "none",
  plot.title = element_text(hjust = 0.5))

# 每个射区的频率
p4 <- ggplot(shots, aes(x = fct_infreq(shot_zone_area))) + geom_bar(aes(fill = shot_zone_area)) +
  labs(y = "Frequency") + theme_bw() + theme(axis.text.x = element_text(size = 7),
  axis.title.x = element_blank(), legend.position = "none")

# 拼图
grid.arrange(p3, p4, layout_matrix = cbind(c(1, 2)))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-10-1.png" alt="plot of chunk unnamed-chunk-10"  />
<p class="caption">plot of chunk unnamed-chunk-10</p>
</div>


```r
# Shot zone basic
p5 <- ggplot(shots, aes(x = lon, y = lat)) + geom_point(aes(color = shot_zone_basic)) +
  labs(title = "Shot zone basic") + ylim(c(33.7, 34.0883)) + theme_void() + theme(legend.position = "none",
  plot.title = element_text(hjust = 0.5))

# Frequency for each shot zone basic
p6 <- ggplot(shots, aes(x = fct_infreq(shot_zone_basic))) + geom_bar(aes(fill = shot_zone_basic)) +
  labs(y = "Frequency") + theme_bw() + theme(axis.text.x = element_text(size = 6.3),
  axis.title.x = element_blank(), legend.position = "none")

# Subplot
grid.arrange(p5, p6, layout_matrix = cbind(c(1, 2)))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-11-1.png" alt="plot of chunk unnamed-chunk-11"  />
<p class="caption">plot of chunk unnamed-chunk-11</p>
</div>

## 投篮准确率


```r
# 按投篮类型的准确率
shots %>%
  group_by(action_type) %>%
  summarise(Accuracy = mean(shot_made_flag), counts = n()) %>%
  filter(counts > 20) %>%
  ggplot(aes(x = reorder(action_type, Accuracy), y = Accuracy)) + geom_point(aes(colour = Accuracy),
  size = 3) + scale_colour_gradient(low = "orangered", high = "chartreuse3") +
  labs(title = "Accuracy by shot type") + theme_bw() + theme(axis.title.y = element_blank(),
  legend.position = "none", plot.title = element_text(hjust = 0.5)) + coord_flip()
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-12-1.png" alt="plot of chunk unnamed-chunk-12"  />
<p class="caption">plot of chunk unnamed-chunk-12</p>
</div>


```r
# 按赛季的准确率
shots %>%
  group_by(season) %>%
  summarise(Accuracy = mean(shot_made_flag)) %>%
  ggplot(aes(x = season, y = Accuracy, group = 1)) + geom_line(aes(colour = Accuracy)) +
  geom_point(aes(colour = Accuracy), size = 3) + scale_colour_gradient(low = "orangered",
  high = "chartreuse3") + labs(title = "Accuracy by season", x = "Season") + theme_bw() +
  theme(legend.position = "none", axis.text.x = element_text(angle = 45, hjust = 1),
    plot.title = element_text(hjust = 0.5))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-13-1.png" alt="plot of chunk unnamed-chunk-13"  />
<p class="caption">plot of chunk unnamed-chunk-13</p>
</div>

As we see, the accuracy begins to decrease badly from the 2013-14 season. Why didn't you retire before, Kobe?


```r
# 季后赛和常规赛各赛季的准确率
shots %>%
  group_by(season) %>%
  summarise(Playoff = mean(shot_made_flag[playoffs == 1]), RegularSeason = mean(shot_made_flag[playoffs ==
    0])) %>%
  ggplot(aes(x = season, group = 1)) + geom_line(aes(y = Playoff, colour = "Playoff")) +
  geom_line(aes(y = RegularSeason, colour = "RegularSeason")) + geom_point(aes(y = Playoff,
  colour = "Playoff"), size = 3) + geom_point(aes(y = RegularSeason, colour = "RegularSeason"),
  size = 3) + labs(title = "Accuracy by season", subtitle = "Playoff and Regular Season",
  x = "Season", y = "Accuracy") + theme_bw() + theme(legend.title = element_blank(),
  legend.position = "bottom", axis.text.x = element_text(angle = 45, hjust = 1),
  plot.title = element_text(hjust = 0.5), plot.subtitle = element_text(hjust = 0.5))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-14-1.png" alt="plot of chunk unnamed-chunk-14"  />
<p class="caption">plot of chunk unnamed-chunk-14</p>
</div>

请注意，红线是不连续的，因为在某些赛季，洛杉矶湖人队没有进入 NBA 季后赛。


```r
# 各赛季两分球和三分球命中率
shots %>%
  group_by(season) %>%
  summarise(TwoPoint = mean(shot_made_flag[shot_type == "2PT Field Goal"]), ThreePoint = mean(shot_made_flag[shot_type ==
    "3PT Field Goal"])) %>%
  ggplot(aes(x = season, group = 1)) + geom_line(aes(y = TwoPoint, colour = "TwoPoint")) +
  geom_line(aes(y = ThreePoint, colour = "ThreePoint")) + geom_point(aes(y = TwoPoint,
  colour = "TwoPoint"), size = 3) + geom_point(aes(y = ThreePoint, colour = "ThreePoint"),
  size = 3) + labs(title = "Accuracy by season", subtitle = "2PT Field Goal and 3PT Field Goal",
  x = "Season", y = "Accuracy") + theme_bw() + theme(legend.title = element_blank(),
  legend.position = "bottom", axis.text.x = element_text(angle = 45, hjust = 1),
  plot.title = element_text(hjust = 0.5), plot.subtitle = element_text(hjust = 0.5))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-15-1.png" alt="plot of chunk unnamed-chunk-15"  />
<p class="caption">plot of chunk unnamed-chunk-15</p>
</div>

2013-2014 赛季到底发生了什么？三分球命中率太低了！


```r
# 射击距离精度
shots %>%
  group_by(shot_distance) %>%
  summarise(Accuracy = mean(shot_made_flag)) %>%
  ggplot(aes(x = shot_distance, y = Accuracy)) + geom_line(aes(colour = Accuracy)) +
  geom_point(aes(colour = Accuracy), size = 2) + scale_colour_gradient(low = "orangered",
  high = "chartreuse3") + labs(title = "Accuracy by shot distance", x = "Shot distance (ft.)") +
  xlim(c(0, 45)) + theme_bw() + theme(legend.position = "none", plot.title = element_text(hjust = 0.5))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-16-1.png" alt="plot of chunk unnamed-chunk-16"  />
<p class="caption">plot of chunk unnamed-chunk-16</p>
</div>


```r
# 按射区范围划分的精度
p7 <- shots %>%
  select(lat, lon, shot_zone_range, shot_made_flag) %>%
  group_by(shot_zone_range) %>%
  mutate(Accuracy = mean(shot_made_flag)) %>%
  ggplot(aes(x = lon, y = lat)) + geom_point(aes(colour = Accuracy)) + scale_colour_gradient(low = "red",
  high = "lightgreen") + labs(title = "Accuracy by shot zone range") + ylim(c(33.7,
  34.0883)) + theme_void() + theme(plot.title = element_text(hjust = 0.5))

# 按射区面积计算的精度
p8 <- shots %>%
  select(lat, lon, shot_zone_area, shot_made_flag) %>%
  group_by(shot_zone_area) %>%
  mutate(Accuracy = mean(shot_made_flag)) %>%
  ggplot(aes(x = lon, y = lat)) + geom_point(aes(colour = Accuracy)) + scale_colour_gradient(low = "red",
  high = "lightgreen") + labs(title = "Accuracy by shot zone area") + ylim(c(33.7,
  34.0883)) + theme_void() + theme(legend.position = "none", plot.title = element_text(hjust = 0.5))

# Accuracy by shot zone basic
p9 <- shots %>%
  select(lat, lon, shot_zone_basic, shot_made_flag) %>%
  group_by(shot_zone_basic) %>%
  mutate(Accuracy = mean(shot_made_flag)) %>%
  ggplot(aes(x = lon, y = lat)) + geom_point(aes(colour = Accuracy)) + scale_colour_gradient(low = "red",
  high = "lightgreen") + labs(title = "Accuracy by shot zone basic") + ylim(c(33.7,
  34.0883)) + theme_void() + theme(legend.position = "none", plot.title = element_text(hjust = 0.5))

# Subplots
grid.arrange(p7, p8, p9, layout_matrix = cbind(c(1, 2), c(1, 3)))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-17-1.png" alt="plot of chunk unnamed-chunk-17"  />
<p class="caption">plot of chunk unnamed-chunk-17</p>
</div>


```r
# 按剩余时间计算的精确度
shots %>%
  group_by(minutes_remaining) %>%
  summarise(Accuracy = mean(shot_made_flag)) %>%
  ggplot(aes(x = minutes_remaining, y = Accuracy)) + geom_bar(aes(fill = Accuracy),
  stat = "identity") + scale_fill_gradient(low = "orangered", high = "chartreuse3") +
  labs(title = "Accuracy by minutes remaining", x = "Minutes remaining") + theme_bw() +
  theme(legend.position = "none", plot.title = element_text(hjust = 0.5))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-18-1.png" alt="plot of chunk unnamed-chunk-18"  />
<p class="caption">plot of chunk unnamed-chunk-18</p>
</div>


```r
# 按剩余秒数计算精确度
shots %>%
  group_by(seconds_remaining) %>%
  summarise(Accuracy = mean(shot_made_flag)) %>%
  ggplot(aes(x = seconds_remaining, y = Accuracy)) + geom_bar(aes(fill = Accuracy),
  stat = "identity") + scale_fill_gradient(low = "orangered", high = "chartreuse3") +
  labs(title = "Accuracy by seconds remaining", x = "Seconds remaining") + theme_bw() +
  theme(legend.position = "none", plot.title = element_text(hjust = 0.5))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-19-1.png" alt="plot of chunk unnamed-chunk-19"  />
<p class="caption">plot of chunk unnamed-chunk-19</p>
</div>


```r
# Accuracy by opponent
shots %>%
  group_by(opponent) %>%
  summarise(Accuracy = mean(shot_made_flag)) %>%
  mutate(Conference = c("Eastern", "Eastern", "Eastern", "Eastern", "Eastern",
    "Eastern", "Western", "Western", "Eastern", "Western", "Western", "Eastern",
    "Western", "Western", "Eastern", "Eastern", "Western", "Eastern", "Western",
    "Western", "Eastern", "Western", "Eastern", "Eastern", "Western", "Western",
    "Western", "Western", "Western", "Eastern", "Western", "Western", "Eastern")) %>%
  ggplot(aes(x = reorder(opponent, -Accuracy), y = Accuracy)) + geom_bar(aes(fill = Conference),
  stat = "identity") + labs(title = "Accuracy by opponent", x = "Opponent") + theme_bw() +
  theme(legend.position = "bottom", legend.title = element_blank(), axis.text.x = element_text(angle = 45,
    hjust = 1), plot.title = element_text(hjust = 0.5))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-20-1.png" alt="plot of chunk unnamed-chunk-20"  />
<p class="caption">plot of chunk unnamed-chunk-20</p>
</div>


```r
# Accuracy by opponent in 2PT Field Goal and 3PT Field Goal
shots %>%
  group_by(opponent) %>%
  summarise(TwoPoint = mean(shot_made_flag[shot_type == "2PT Field Goal"]), ThreePoint = mean(shot_made_flag[shot_type ==
    "3PT Field Goal"])) %>%
  ggplot(aes(x = opponent, group = 1)) + geom_line(aes(y = TwoPoint, colour = "TwoPoint")) +
  geom_line(aes(y = ThreePoint, colour = "ThreePoint")) + geom_point(aes(y = TwoPoint,
  colour = "TwoPoint"), size = 3) + geom_point(aes(y = ThreePoint, colour = "ThreePoint"),
  size = 3) + labs(title = "Accuracy by opponent", subtitle = "2PT Field Goal and 3PT Field Goal",
  x = "Opponent", y = "Accuracy") + theme_bw() + theme(legend.title = element_blank(),
  legend.position = "bottom", axis.text.x = element_text(angle = 45, hjust = 1),
  plot.title = element_text(hjust = 0.5), plot.subtitle = element_text(hjust = 0.5))
```

<div class="figure" style="text-align: center">
<img src="/figures/course/2020-02-25-kobe/kobe/unnamed-chunk-21-1.png" alt="plot of chunk unnamed-chunk-21"  />
<p class="caption">plot of chunk unnamed-chunk-21</p>
</div>
