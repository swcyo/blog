---
title: "使用blogdown搭建个人博客的一些经验"
authors: 欧阳松
tags: [R]
categories: [教程]
slug: bolgdown-exprience
date: 2021-08-16
---

使用blogdown搭建hugo博客，关于hugo网上教程很多，要安装好多好多软件,还要好多代码,这里只说R-studio,因为只要安装一个就够了,我现在说下可能会遇到的坑

## 经典教程：

**可以看这个**：<https://cosx.org/2018/01/build-blog-with-blogdown-hugo-netlify-github/>

## 我要说的：

### MacOS和Windows的区别

我的是MacOS,当然有一定的优势,但是一般人用的是windows,好在我装的双系统,所以两个都体验了,感觉上差不多,唯一区别可能是安装hugo的时候,由于国内对github的限制,所以很慢,但是windows可以下载好了可以有别的方法安装,后面再说,而mac没看到教程,好在我在Mac一次性安装hugo成功,但是在windows上却怎么都安装不了,最后用的密招.
结论:安装和使用都差不多

### 能不能打开Github和Netlify

用**blogdown**最大的问题就是要连接`Github`,包括后面用`Netlify`部署网页也需要连接`Github`,而且这两个网站打开都贼慢,基本上都要靠`VPN`,而VPN这个东西你懂的,我也不好说太多

-   [x] **Github:**
    我用的VPN有时候可以打开,但有时候也不好说,所以一定要有一个好的梯子,这个自理,不然所有都百搭。当然也有人说能不能用Gitee,确实Gitee网速很快,不过我发现还是不行,因为Gitee虽然可以导入Github仓库,但是安装不了hugo的主题,这里要注意用blogdown安装主题和Github上的主题其实是不一样的,因为blogdown好像做了二次处理,所以gitee安装不了,而且最大的问题是Netlify连接不了码云.

-   [x] **Netlify:**
    blogdown搭建的只是本地网站,如果要放在互联网上,就需要有东西来部署,Github的Page支持的是index.html,但是hugo却是index.md,我知道最简单的办法,就是先把本地的文件上传到Github,然后连接Netlify,Deploy以下就成功了...

### 流程是什么

      Rstudio安装blogdown-->
      安装hugo-->
      blogdown安装主题-->
      搭建本地网站-->
      上传至Github-->
      部署到Netlify并获得域名

### 安装hugo和pandoc

在RStudio下**不需要安装pandoc**，而安装`hugo`就要看网络，因为需要下载GitHub的数据，如果网速不好就可能下载不成功，网速有个教程，就是当windows系统安装不了的时候，那么就把错误信息中的链接复制到浏览器直接下载，把文件**解压**以后会发现里面就有一个**hugo.exe**文件，把解压好的`hugo.exe`文件放在`D:/`根目录下，然后输入代码本地安装hugo，数秒钟就可以安装好。

```{r}
install('blogdown')
blogdown::install_hugo() # 如果安装不成功就用上面的办法解压安装，代码如下
blogdown:::install_hugo_bin("D:/hugo.exe")# 注意这里是三个冒号
```

### 安装主题

需要先在Github上新建一个仓库,新建一个`.gitignore`文件即可,代码可以输,也不可以不输,不输的话就代表全部上传,输入了代表了某些不上传,主要是为了效率.

-   [x] 推荐用[wowchemy](https://wowchemy.com/)的主题,效果是真的好（**现在要收钱了**）

    ```{r}
    .Rproj.user
    .Rhistory
    .RData
    .Ruserdata
    public
    static/figures
    blogdown  # 有人说要写成/blogdown
    ```

### 网页的搭建

通过新建一个project安装后主题后，就需要先定义一些基本的元素，比如网站名，可见有个_config.toml（原本是yml，blogdown给变动了）的文件，可以直接用R-studio改内容，还有wowchemy主题一般都可以看到一个config的文件夹，里面有memu和params的文件，这个也是要改的，而且可以边改边看效果，当然建议是放在浏览器里面看，这里最好有两块屏幕最好，一个屏幕写代码，一个屏幕看效果

    ```{r}
    blogdown:::serve_site #预览网页效果
    ```

### 上传文件至Github 

需要先安装git，或者安装Github Desktop直接推送，不语。。。

### 关联Netlify

打开netlfy可以用Github账号登陆，然后新建site，设置都很简单，部署好了以后直接就Deploy了，一般情况下都是实时更新，你的Github更新了，这个网站差不多就更新，当然有时候也有时差，如果没有变化，可能就是部署失败。

如果是用Rmarkdown建页面，好像**生成的html文件需要删除**以后再上传才行
Netlify打开一点问题都没有，最大的问题是登陆，貌似只有梯子才能用，而且，我只能手机才能登陆。

电脑是完全打不开（至少我的电脑浏览器就没打开过）

### Netlify_CMS

非常好的东西,就是在netlify上有个identify,需要设置Gitway,需要天ftoken,我的理解就是安装了这个,就可以直接在后台更改Github的内容,然后同步更新,其实说白了还是需要修改Github的内容,Netlify就是一个中转站而已

### 如何登陆CMS

域名后面加一个/admin就可以了,然后需要设置用户和名字,这样就可以后台网页添加内容了,很是方便,但也有一些限制....

我发现CMS经常不能用,老是提示需要修改Gitway,但是有的站点可以,有的又不可以,不知道规律是什么....

### 白嫖一个rbind.io域名

有了netlify,直接去<https://github.com/rbind/support>上提申请就好了,一般一天就有回应....,然后在netlify上设置就行。
