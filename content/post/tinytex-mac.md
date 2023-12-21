---
title: Mac系统下如何使用Tinytex本地安装简易latex
author: 欧阳松
date: '2021-09-24'
slug: tinytex-mac
categories:
  - tinytex
tags:
  - tinytex
enableToc: yes
---

昨天写了[Tinytex如何本地安装简易latex](/zh/course/tinytex/)，可是那是用的**Windows**系统进行操作，今天试着用**Mac**系统同法安装，还是出了一点小问题，主要的问题是安装时提示如下：

> tinytex::install_tinytex()
>
> The directory /usr/local/bin is not writable. I recommend that you make it writable. See <https://github.com/yihui/tinytex/issues/24> for more info.

意思是`/usr/local/bin`这个文件夹不可读，然后给了一个Github上的解决方案，如果GitHub不好登陆的话，可以使用国内镜像<https://hub.fastgit.org/yihui//tinytex/issues/24>

在这里有人提问，然后yihui大神就给了解决方案：


<table><thead><tr class="header"><th><p>That is odd. <code>/usr/local/bin</code> should be writable by default on (at least recent versions of) macOS. Anyway, see <a href="https://stackoverflow.com/a/26647594/559676">https://stackoverflow.com/a/26647594/559676</a> for how to regain the write permission. In short, run this in your terminal:</p><pre><code>sudo chown -R `whoami`:admin /usr/local/bin</code></pre><p>Then this in your terminal (<strong>do not</strong> use <code>sudo</code> here!):</p><pre><code>~/Library/TinyTeX/bin/*/tlmgr path add</code></pre></th></tr></thead><tbody></tbody></table>


------------------------------------------------------------------------

简单的说，就是打开Mac上的**终端**，首先复制这段代码，然后回车，这时候输入你的电脑密码就可以修改为可读了

```         
sudo chown -R `whoami`:admin /usr/local/bin
```

然后再输入下面这个代码

```         
~/Library/TinyTeX/bin/*/tlmgr path add
```

------------------------------------------------------------------------

然后就可以用Windows系统的办法进行本地安装了，这里要注意windows系统下载的是zip文件，而mac系统下载的则是tgz文件，我还是建议下载各完整的TinyTeX.tgz。

官方下载列表

|                               | **Windows**                                                      | **macOS**                                                         | **Linux**                                                               |
|----------------|------------------|------------------|--------------------|
| TinyTeX-0                     | [TinyTeX-0.zip](https://yihui.org/tinytex/TinyTeX-0.zip) (27 MB) | [TinyTeX-0.tgz](https://yihui.org/tinytex/TinyTeX-0.tgz) (1.4 MB) | [TinyTeX-0.tar.gz](https://yihui.org/tinytex/TinyTeX-0.tar.gz) (670 KB) |
| TinyTeX-1                     | [TinyTeX-1.zip](https://yihui.org/tinytex/TinyTeX-1.zip) (99 MB) | [TinyTeX-1.tgz](https://yihui.org/tinytex/TinyTeX-1.tgz) (83 MB)  | [TinyTeX-1.tar.gz](https://yihui.org/tinytex/TinyTeX-1.tar.gz) (64 MB)  |
| TinyTeX                       | [TinyTeX.zip](https://yihui.org/tinytex/TinyTeX.zip) (200+ MB)   | [TinyTeX.tgz](https://yihui.org/tinytex/TinyTeX.tgz) (188+ MB)    | [TinyTeX.tar.gz](https://yihui.org/tinytex/TinyTeX.tar.gz) (160+ MB)    |
| tinitex binary (experimental) | [tinitex.zip](https://yihui.org/tinytex/tinitex.zip) (24 MB)     | [tinitex.tgz](https://yihui.org/tinytex/tinitex.tgz) (28 MB)      | [tinitex.tar.gz](https://yihui.org/tinytex/tinitex.tar.gz) (31 MB)      |

然后使用下列代码即可

``` r
tinytex:::install_prebuilt('/Users/mac/Downloads/TinyTeX.tgz') ##记得这里是三个:
```

检测是否安装成功

``` r
tinytex::is_tinytex()
```

如果出现**TRUE**，那么恭喜你，安装成功了。。。
