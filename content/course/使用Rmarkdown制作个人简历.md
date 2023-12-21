---
title: 使用Rmarkdown制作个人简历
author: 欧阳松
Date: 2021-10-04
slgu: Rforresume
---

很早的时候，我给自己做过一个[个人简历](swcyo.gitee.io/cv)的网站并上传到Github上了，但是由于GitHub很不稳定，所以我又导到Gtiee码云上。


早期的个人简历其实也是基于Rmardown制作的，不过是基于[Y叔](https://mp.weixin.qq.com/s/Dz2fa83O_P5QPD8VLR7DRQ)的一个教程，而Y叔也是修改了https://github.com/nstrayer/cv这个代码源。至于为什么修改，是因为最初那个模板是需要链接Google的sheet，所以你懂的。。。

Y叔的教程的主要原理是把一个csv的表格的内容调用到网页进来，我们不需要修改太多的东西，只需要修改改改`index.Rmd`和`position.csv`的内容，然后用Rmarkdown渲染成生成html，最后上传到GitHub或者Gitee上进行访问。

然而人总是懒惰的，使用Y叔的教程还是有起点的，比如需要先clone源码，然后修改编译，有时候还可以需要安装很多包，费时费力，其实还可以有更简单的办法的。

## 需要的包

只需要安装一个**pagedown**的包即可，当然没有安装Rmarkdown的需要先安装**rmarkdown**，这个很简单，因为CRAN上就有，而且即使你没有安装，直接安装**pagedown**的时候也会提示你安装。

```R
install.packages('rmarkdown') 
install.packages('pagedown') 
```

## 加载模板

安装好**pagedown**之后，Rmardown会添加模板，我们这时新增一个R markdown文档（File-- New File-- R markdown），弹出菜单框选择**From Template**，然后找到**HTML Resume**（可以看到除了Resume外，其实还可以写Letter和Thesis等，另外安装**rticles**这个包的话还可以加载各种期刊论文的模板）。

## 修改基本参数

此时，会多一个Untitled的Rmd文件，内容如下:

```R
---
title: "Lijia Yu's resume"
author: Lijia Yu
date: "`r Sys.Date()`"
output:
  pagedown::html_resume:
    # set it to true for a self-contained HTML page but it'll take longer to render
    self_contained: false
# uncomment this line to produce HTML and PDF in RStudio:
#knit: pagedown::chrome_print
---

# Aside

![Lijia Yu](https://avatars1.githubusercontent.com/u/895125?s=400&v=4){width="80%"}
```

我们可以逐一修改标题、作者、输出样式等，其中如果设置self_contained: true，那么最终只会渲染成一个HTML的文件。

## 渲染效果

如果我们想看到效果，只需要点击一下**Knit**即可，这时保存到一个文件夹中，我们可以在Viewer看到效果，剩下的都是无脑操作

## 上传网页

全部修改完成后，需要再次Knit，然后将文件直接上传至GitHub或者Gitee上，然后生成网页即可访问。

比如，我的新个人简历是[https://swcyo.gitee.io/resume/](https://swcyo.gitee.io/resume/)

<iframe src="https://swcyo.gitee.io/resume/" width="800" height="600" frameborder="0" style="border:0">
</iframe>