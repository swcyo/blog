---
title: 那么多的'down'都是什么？
author: 欧阳松
date: '2021-09-10'
slug: downs
categories:
  - bookdown
  - blodgown
  - pkgdown
tags:
  - R Markdown
from_Rmd: yes
---

经常会看到许多以"down"结尾的单词，其中最重要的当属markdown，但作为R语言的学习者，开发了各种各样的down，我大致梭罗了一些，做一介绍，


## markdown

一款轻量级语句工具，基础中的基础，码字之王。

这个基本就不用介绍了，Markdown 是一个 Web 上使用的文本到HTML的转换工具，可以通过简单、易读易写的文本格式生成结构化的HTML文档。目前 github、Stackoverflow 等网站均支持这种格式，格式是.md。

<http://www.markdown.cn/>


## R Markdown

Rstudio中高度集成的工具，可以使用markdown语句，几乎是后续所有down都离不开它，源自谢益辉，其中集成的Knit可以渲染各种效果。

The rmarkdown package (Allaire, Xie, McPherson, et al. 2021) was first created in early 2014. During the past four years, it has steadily evolved into a relatively complete ecosystem for authoring documents, so it is a good time for us to provide a definitive guide to this ecosystem now. At this point, there are a large number of tasks that you could do with R Markdown:

Compile a single R Markdown document to a report in different formats, such as PDF, HTML, or Word.

Create notebooks in which you can directly run code chunks interactively.

Make slides for presentations (HTML5, LaTeX Beamer, or PowerPoint).

Produce dashboards with flexible, interactive, and attractive layouts.

Build interactive applications based on Shiny.

Write journal articles.

Author books of multiple chapters.

Generate websites and blogs.

<https://bookdown.org/yihui/rmarkdown/>


## bookdown

同样是谢大神的杰作，基于Rmarkdown的电子书生成工具，可以生成gitbook、pdf、word和epub格式的电子书，也是后续各种down的基础

The bookdown package is an open-source R package that facilitates writing books and long-form articles/reports with R Markdown. Features include:

Generate printer-ready books and ebooks from R Markdown documents.

A markup language easier to learn than LaTeX, and to write elements such as section headers, lists, quotes, figures, tables, and citations.

Multiple choices of output formats: PDF, LaTeX, HTML, EPUB, and Word.

Possibility of including dynamic graphics and interactive applications (HTML widgets and Shiny apps).

Support a wide range of languages: R, C/C++, Python, Fortran, Julia, Shell scripts, and SQL, etc.

LaTeX equations, theorems, and proofs work for all output formats.

Can be published to GitHub, bookdown.org, and any web servers.

Integrated with the RStudio IDE.

One-click publishing to <https://bookdown.org>.


## blogdown

还是谢大神的巨作，使用Rmarkdown创建网站，使用的是hugo主题，包括我这个站也是基于此。

The goal of the blogdown package is to provide a powerful and customizable website output format for R Markdown. Use dynamic R Markdown documents to build webpages featuring:

R code (or other programming languages that knitr supports),

automatically rendered output such as graphics, tables, analysis results, and HTML widgets, and

technical writing elements such as citations, footnotes, and LaTeX math, enabled by the bookdown package.

By default, blogdown uses Hugo, a popular open-source static website generator, which provides a fast and flexible way to build your site content to be shared online. Other website generators like Jekyll and Hexo are also supported.

A useful feature of blogdown sites, compared to other R Markdown-based websites, is that you may organize your website content (including R Markdown files) within subdirectories. This makes blogdown a good solution not just for blogging or sites about R --- it can also be used to create general-purpose websites to communicate about data science, statistics, data visualization, programming, or education.

<https://bookdown.org/yihui/blogdown/>


## pagedown

使用R标记和CSS创建漂亮的PDF，再安装**rticles**这个包以后可以集成多种模版，包括个人简历、期刊杂志、论文等等，也是后续期刊杂志的基础。

The traditional way to beautiful PDFs is often through LaTeX or Word, but have you ever thought of printing a web page to PDF? Web technologies (HTML/CSS/JavaScript) are becoming more and more amazing. It is entirely possible to create high-quality PDFs through Google Chrome or Chromium now. Web pages are usually single-page documents, but they can be paginated thanks to the JavaScript library Paged.js, so that you can have elements like headers, footers, and page margins for the printing purpose. In this talk, we introduce a new R package, pagedown (<https://github.com/rstudio/pagedown>), to create PDF documents based on R Markdown and Paged.js. Applications of pagedown includes, but not limited to, books, articles, posters, resumes, letters, and business cards. With the power of CSS and JavaScript, you can typeset your documents with amazing elegance (e.g., a single line of CSS, "tr:nth-child(even) { background: #eee; }", will give you a striped table, and "border-radius: 50%;" gives you a circular element) and power (e.g., HTML Widgets).

<https://github.com/rstudio/pagedown>


## pkgdown

一看为R包快速轻松地构建网站的包，建立网站介绍你的包

pkgdown is designed to make it quick and easy to build a website for your package. You can see pkgdown in action at [\<https://pkgdown.r-lib.org\>](https://pkgdown.r-lib.org){.uri}: this is the output of pkgdown applied to the latest version of pkgdown. Learn more in vignette("pkgdown") or ?build_site.

<https://pkgdown.r-lib.org/>


## officedown

可以集成生成word和ppt的R包，基于的是R Markdown 。

Word

The package facilitates the formatting of Microsoft Word documents produced by R Markdown documents by providing a range of features:

PowerPoint

The package also enhances PowerPoint productions with R Markdown by providing a mechanism for placing results according to the slide template contained in the PowerPoint document used as "reference_doc". It becomes easy to add several contents in the same slide.

<https://github.com/davidgohel/officedown>

<https://ardata-fr.github.io/officeverse/officedown-for-word.html>


## ElegantBookdown

黄湘云制作的一款将[ElegantBook](https://github.com/ElegantLaTeX/ElegantBook)制作成bookdown格式的模板，可以生成很好看的word

### 在线预览

-   <https://bookdown.org/xiangyun/elegantbookdown/> (stable)

-   <https://xiangyunhuang.github.io/ElegantBookdown/> (latest)

源码地址：<https://github.com/XiangyunHuang/ElegantBookdown>

## thesisdown

使用bookdown软件包更新的R Markdown论文模板的包，是今后写毕业论文的方向，目前衍生了若干高效的'down'，下面有各种例子，有兴趣的话，你也可以自己制作一个。

This project was inspired by the bookdown package and is an updated version of my Senior Thesis template in the reedtemplates package here. It was originally designed to only work with the Reed College LaTeX template, but has since been adapted to work with many different institutions by many different individuals. Check out the Customizing thesisdown to your institution section below for examples.

Currently, the PDF and gitbook versions are fully-functional. The word and epub versions are developmental, have no templates behind them, and are essentially calls to the appropriate functions in bookdown.

[\<https://github.com/ismayc/thesisdown\>](https://github.com/ismayc/thesisdown)

| **College/University**                                      | **Repository**                                                                                | **Based on**                                                    |
|--------------------|-------------------------------|---------------------|
| American University                                         | [SimonHeuberger/eagledown](https://github.com/SimonHeuberger/eagledown)                       | [benmarwick/huskydown](https://github.com/benmarwick/huskydown) |
| Brock University                                            | [brentthorne/brockdown](https://github.com/brentthorne/brockdown)                             | [zkamvar/beaverdown](https://github.com/zkamvar/beaverdown)     |
| École Doctorale de Mathématiques Hadamard                   | [abichat/hadamardown](https://github.com/abichat/hadamardown)                                 | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Drexel University                                           | [tbradley1013/dragondown](https://github.com/tbradley1013/dragondown)                         | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Duke University                                             | [mine-cetinkaya-rundel/thesisdowndss](https://github.com/mine-cetinkaya-rundel/thesisdowndss) | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Graduate Institute of International and Development Studies | [jhollway/iheiddown](https://github.com/jhollway/iheiddown)                                   | [ulyngs/oxforddown](https://github.com/ulyngs/oxforddown)       |
| Heidelberg University, Faculty of Biosciences               | [nkurzaw/heididown](https://github.com/nkurzaw/heididown)                                     | [phister/huwiwidown](https://github.com/phister/huwiwidown)     |
| Humboldt University of Berlin                               | [phister/huwiwidown](https://github.com/phister/huwiwidown)                                   | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Kansas State University                                     | [emraher/wildcatdown](https://github.com/emraher/wildcatdown)                                 | [benmarwick/huskydown](https://github.com/benmarwick/huskydown) |
| Massachusetts Institute of Technology                       | [ratatstats/manusdown](https://github.com/ratatstats/manusdown)                               | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Oregon State University                                     | [zkamvar/beaverdown](https://github.com/zkamvar/beaverdown)                                   | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Oxford University                                           | [davidplans/oxdown](https://github.com/davidplans/oxdown)                                     | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Queen's University                                          | [eugenesit/gaelsdown](https://github.com/eugenesit/gaelsdown)                                 | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Smith College                                               | [SmithCollege-SDS/pioneerdown](https://github.com/SmithCollege-SDS/pioneerdown)               | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Southampton University                                      | [dr-harper/sotonthesis](https://github.com/dr-harper/sotonthesis)                             | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Stanford University                                         | [mhtess/treedown](https://github.com/mhtess/treedown)                                         | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Universidade Federal do Rio de Janeiro                      | [COPPE-UFRJ/coppedown](https://github.com/COPPE-UFRJ/coppedown)                               | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Université Paris-Saclay                                     | [abichat/hadamardown](https://github.com/abichat/hadamardown)                                 | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| University College London                                   | [benyohaiphysics/thesisdownUCL](https://github.com/benyohaiphysics/thesisdownUCL)             | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| University of Arizona                                       | [kelseygonzalez/beardown](https://github.com/kelseygonzalez/beardown)                         | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| University of California, Davis                             | [ryanpeek/aggiedown](https://github.com/ryanpeek/aggiedown)                                   | [DanOvando/gauchodown](https://github.com/DanOvando/gauchodown) |
| University of California, Santa Barbara                     | [DanOvando/gauchodown](https://github.com/DanOvando/gauchodown)                               | [benmarwick/huskydown](https://github.com/benmarwick/huskydown) |
| University of Florida                                       | [ksauby/thesisdownufl](https://github.com/ksauby/thesisdownufl)                               | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| University of Freiburg                                      | [vivekbhr/doctorRbite](https://github.com/vivekbhr/doctorRbite)                               | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| University of Kansas                                        | [wjakethompson/jayhawkdown](https://github.com/wjakethompson/jayhawkdown)                     | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| University of Manchester                                    | [juliov/uomthesisdown](https://github.com/JulioV/uomthesisdown)                               | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| University of Minnesota                                     | [zief0002/qmedown](https://github.com/zief0002/qmedown)                                       | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| University of New South Wales                               | [rensa/unswthesisdown](https://github.com/rensa/unswthesisdown)                               | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| University of Salzburg                                      | [irmingard/salzburgthesisdown](https://github.com/irmingard/salzburgthesisdown)               | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| University of Toronto                                       | [mattwarkentin/torontodown](https://github.com/mattwarkentin/torontodown)                     | [zkamvar/beaverdown](https://github.com/zkamvar/beaverdown)     |
| University of Washington                                    | [benmarwick/huskydown](https://github.com/benmarwick/huskydown)                               | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| TU Wien                                                     | [ben-schwen/robotdown](https://github.com/ben-schwen/robotdown)                               | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| University of Bristol                                       | [mattlee821/bristolthesis](https://github.com/mattlee821/bristolthesis)                       | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Universidade Federal de Santa Catarina                      | [lfpdroubi/ufscdown](https://github.com/lfpdroubi/ufscdown)                                   | [ismayc/thesisdown](https://github.com/ismayc/thesisdown)       |
| Universiteit van Amsterdam                                  | [lcreteig/amsterdown](https://github.com/lcreteig/amsterdown)                                 | [benmarwick/huskydown](https://github.com/benmarwick/huskydown) |
