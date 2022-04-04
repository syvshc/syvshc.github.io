---
title: 滚动升级 TeXLive
date: 2022-04-04 16:38:13
tags:
    - [LaTeX]
    - [帮助文档]
    - [tlmgr]
categories:
    - [帮助文档, LaTeX]
---

这里说一下如何滚动升级 TeXLive, 而不卸载安装. 由于我也是第一次这么做, 不知道有没有什么可能产生的后果. 

<!-- more -->

首先, 愿意看官方文档的小伙伴可以直接看[官网](https://www.tug.org/texlive/upgrade.html)

## Windows

我暂时只在 windows 系统下进行了尝试, Linux 在尝试后再更新. 

首先我们如果直接使用 `tlmgr update -self -all` 会得到