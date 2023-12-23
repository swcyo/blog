---
title: GitHub安装包包的新办法
author: 欧阳松
date: '2023-12-19'
slug: github2023
categories:
  - Github
tags:
  - Github
---

Github本来是很好的东西，可是一直都是 GFW。

我们很多优秀的开发者的包包都是托管在Github，但是访问你总是我的痛，Gitee之前也挺好的，可是最近好像关键词也限制的多，我之前写的关于[2021年石河子大学毕业论文格式](/publication/2021年石河子大学学位论文书写规范仅供参考版/)的电子书都渲染不出来了，其他的托管平台也不是说不能用，但总是麻烦啊。

我之前也写过很多关于Github的教程，但其实好多也不能用了，这几天看了"[从 GitHub 上下载文件的一点经验 - 楚新元 \| All in R (rbind.io)](https://cxy.rbind.io/post/2023/10/13/github/)"的文章，分享一下使用`install_github()`函数安装R包包的办法，其实原理就是*通过镜像网站，修改函数*实现，简单粗暴。

首先看下面几个镜像网站能不能登进去，能进去就可以改函数。这个取决于你家ip的网：

-   <https://hub.fastgit.org/> （以前可用）

-   <https://hub.fgit.cf/> （目前可用）

-   <https://ghproxy.com/> （暂时可用，但最好是下面的）

-   <https://mirror.ghproxy.com/>

## 直接嵌入hub镜像

```{r}
install_github = function(pkg) {
  url = paste0("https://hub.fgit.cf/", pkg, ".git")
  remotes::install_git(url) 
}
```

设置好上述函数后，只要输入`install_github(xx/yy)`就可以直接安装了

## ghproxy镜像

```{r}
install_github = function(
    repo,
    subdir = NULL,
    ref = NULL,
    upgrade = c("default", "ask", "always", "never"),
    force = FALSE,
    quiet = FALSE,
    build = TRUE
) {
  # Alternate proxy address
  proxy = c(
    "https://mirror.ghproxy.com/",
    "https://gh-proxy.com/"
  )
  # Determine a proxy address
  if (conn_test(proxy[1]) == "ok") {
    proxy_url = paste0(proxy[1], "https://github.com/", repo, ".git")
  } else {
    proxy_url = paste0(proxy[2], "https://github.com/", repo, ".git")
  }
  # Install GitHub package
  remotes::install_git(
    url = proxy_url,
    subdir = subdir,
    ref = ref,
    upgrade = upgrade,
    force = force,
    quiet = quiet,
    build = build
  ) 
}

```

## ipkg包

楚博士已经开发了一个包专门下载并安装Github的包，目前已被cran收录，可以直接安装使用，不过不是托管在GitHub上，而是托管在Gitlab上。

```{r}
## 直接安装
install.packages('ip')
## Gitlab安装
remotes::install_git('https://gitlab.com/chuxinyuan/ipkg/')
```

目前是1.1.1版，里面只有三个函数：

> `conn_test()  ##测试网站是否可用`
>
> `download_file() ##下载github数据`
>
> `install_github() ##安装github包`
