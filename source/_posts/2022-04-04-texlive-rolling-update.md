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

```bash
tlmgr.pl: Local TeX Live (2021) is older than remote repository (2022).
Cross release updates are only supported with
  update-tlmgr-latest(.sh/.exe) --update
See https://tug.org/texlive/upgrade.html for details.
```

它说让去 [https://tug.org/texlive/upgrade.html](https://tug.org/texlive/upgrade.html) 查看详细信息, 但是 Windows 只给了一句

> There is no comparable upgrade procedure for Windows. Doing a new installation is necessary.

甚至没有给提供 `update-tlmgr-latest.exe`, 那我们去 [`tlmgr` 的官网](https://www.tug.org/texlive/tlmgr.html) 看一下有没有, 果然发现了 

![`tlmgr` 官网](https://raw.githubusercontent.com/syvshc/image/master/postimg/texlive-rolling-update/tlmgr-latest.png)

先别急, 我们在 TeXLive 的安装目录里复制复制一份 `2021` 文件夹, 命名为 `2022`, 当然也可以直接改名, 但是如果这样做, 一旦 TeXLive 2022 有问题, 那么我们将没有办法回到之前可行的工作环境. 如果觉得文件夹很大, 可以选择不复制 `/tlpkg/backups/` 文件夹. 

并且将环境变量中的 `texlive/2021` 改为 `texlive/2022`.

然后我们下载 `update-tlmgr-latest.exe`, 安装, 等到 "Close" 亮起, 点击 "Close" 来关闭安装窗口, 重启命令行. 这时我们再使用 `tlmgr update -list`, 可以看到

```bash
tlmgr.pl: package repository https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet (not verified: gpg unavailable)
===============================================================================
tlmgr itself needs to be updated.
Please do this via either
  tlmgr update --self
or by getting the latest updater for Unix-ish systems:
  https://mirror.ctan.org/systems/texlive/tlnet/update-tlmgr-latest.sh
and/or Windows systems:
  https://mirror.ctan.org/systems/texlive/tlnet/update-tlmgr-latest.exe
Then continue with other updates as usual.
===============================================================================
tlmgr.pl: would save backups to C:/texlive/2021/tlpkg/backups
keep:     academicons              : local:    62623, source:    62622
keep:     altsubsup                : local:    62739, source:    62738
keep:     arara                    : local:    62339, source:    62298
keep:     asmeconf                 : local:    62716, source:    62713
keep:     babel                    : local:    62203, source:    62202
keep:     babel-polish             : local:    62681, source:    62680
...
```

这样的输出, 这时再使用 `tlmgr update -self -all` 即可完成升级

**注意** 由于新版的 `newtx` 宏包的问题依然没有修复, 升级的时候可以选择使用 `tlmgr update -self -all -exclude newtx` 来忽略它的升级. 

升级后使用 `tex -v` 来测试版本

```log
TeX 3.141592653 (TeX Live 2022)
kpathsea version 6.3.4
Copyright 2022 D.E. Knuth.
There is NO warranty.  Redistribution of this software is
covered by the terms of both the TeX copyright and
the Lesser GNU General Public License.
For more information about these matters, see the file
named COPYING and the TeX source.
Primary author of TeX: D.E. Knuth.
```

## Linux

我现在去测试