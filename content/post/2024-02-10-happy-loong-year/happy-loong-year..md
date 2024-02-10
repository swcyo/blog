---
title: "祝龙\U0001F432年龙行龘龘"
author: 欧阳松
date: '2024-02-10'
slug: happy-loong-year
from_Rmd: yes
---

- 从今年起，龙就不再翻译成*Dragon*了，要翻译成**Loong**了哦！

> 画条龙龙祝新年快乐

```
library(ggplot2)
library(emojifont)
ggplot()+geom_emoji('dragon',color='gold')+
  theme_void()+
  theme(plot.background = element_rect(fill = 'firebrick'))+
  labs(title = "Happy Loong Year", caption  = "2024-02-10")+
  theme(plot.title = element_text(color = 'gold',size = 22,
                                  face = 'bold',hjust = 0.5))
```


![plot of chunk unnamed-chunk-1](/figures/post/2024-02-10-happy-loong-year/happy-loong-year./unnamed-chunk-1-1.png)
