---
title: 已经下架的R包和R版本太低安装不了的包怎么安装
author: 欧阳松
date: '2021-09-06'
slug: 已经下架的r包和r版本太低安装不了的包怎么安装
categories:
  - 教程
  - R
tags: []
---

# 如何安装CRAN已经下架的包

以前CRAN有个叫**export**的神包，因为可以把R语言生成的图片（甚至是表格），导出为ppt等各种格式，所以很有影响力。Y叔的公众号曾力推过，见[我不会用illustrator，只会用ppt](https://mp.weixin.qq.com/s/nHT3vkwNfQknr6CLF0cUDQ)！，很方便的导出为ppt二次修改。

~~然而当你直接输入安装代码时，却提示无法安装~~ (2023年测试已更新好，可以继续安装了)

```         
install.packages("export")
```

~~原因就是2020的某一天开始，这个包就被下架了，~~ 那么对于已经下架的R包，我们应该如何安装呢？

首先是CRAN官网去寻找这个R包，因为就算是已经下架了，但是还是会有存档的，你就要去网站寻找[Archive](https://cran.r-project.org/src/contrib/Archive)文档，因为任何一个包都会有升级和删除的可能，但每次的源文件都不会删除，所以如果你想安装以前的那个包的话，可以这样安装：

第一种办法就是下载压缩包安装，比如[export_0.2.2.tar.gz](https://cran.r-project.org/src/contrib/Archive/export/export_0.2.2.tar.gz)

```         
install.packages("~/Downloads/export_0.2.2.tar.gz", repos = NULL, type = "source")
```

第二种办法就是用**remotes**的`install_version()`函数安装以往版本，用这个办法可以安装存档的任何一个版本。

```         
remotes::install_version("export",version = "0.2.2")
```

这样这个包就安装好了，但是。。。。

在果子老师的文章[出图神器export目前不能用了，该如何是好？](https://mp.weixin.qq.com/s/bbiEjCYdTDqoKj6ntEraCg)里面，说的是这R包使用到的**`ph_with_vg_at`**函数现在没有了，已经被officer包里面的**`ph_with`**函数代替。原因是依赖的rvg包升级了，而作者又没有更修改，所以就算你安装好了，也是不能用的，所以果子老师给改了，不过只有一个本地版，具体的可以去果子学生信的微信公众号里看教程

> ('果子要出图', '链接：<https://pan.baidu.com/s/18UckIU2u3sIu70Z283UJBA> 提取码：gznh')  
> 这里下载修改版的export

当然其实还有一个叫**eoffice**的包可以替代**export**的，也是CRAN上的，安装很方便。

------------------------------------------------------------------------

# 如何安装比当前R版本支持更高的包

Y叔之前开发了一个shiny包叫[**MicrobiomeProfiler**](https://github.com/YuLab-SMU/MicrobiomeProfiler)，这个包可以交互式使用clusterProfiler。然而需要R4.1以上的版本才能用，而我的R版本太低，又不想更新怎么办。。。

本来我也不知道怎么办，后面在Y叔Github上提了[issue](https://github.com/YuLab-SMU/MicrobiomeProfiler/issues/1)，给出了解决方案很简单

| 改一下 DESCRIPTION 里面的版本即可 |
|-----------------------------------|

修改DESCRIPTION必须要有权限，所以可以Fork到自己的Git上，然后修改提交再安装。

我导入到了我的Gitee，然后修改为R (\>= 3.6.0)

```         
remotes::install_git('https://gitee.com/swcyo/MicrobiomeProfiler')
```
