---
title: 全国大学生数学建模竞赛模板的 `\maketitle` 处报错
date: 2021-08-04 15:51:49
tags:
    - [LaTeX]
    - [日经问题]
    - [模板]

categories:
    - [LaTeX, 模板]
---

在各种地方获取的全国大学生数学建模竞赛的模板 CUMCMThesis, 使用 `xelatex` 编译后会有如下的报错

```
! Extra }, or forgotten \endgroup.
\UL@stop ...alty \ifnum \lastkern =\thr@@ \egroup 
                                                  \egroup \ifdim \wd \UL@box...
l.22  \maketitle
                
?
```
<!--more-->

### 解决方法
先说解决方法: 在 [LaTeXStudio 的 Github](https://github.com/latexstudio/CUMCMThesis) 上的 `Code` 处进行模板的下载或者 `clone`. 并搭配 `TeXLive 2020/2021`, 使用 `xelatex` 编译即可. 

![](https://raw.githubusercontent.com/syvshc/image/master/postimg/cumcmthesis-maketitle/cumcm.jpg)

### 产生问题的原因

产生问题的原因可以看这个 [Pull Request](https://github.com/latexstudio/CUMCMThesis/pull/17), 这里再拿出来说一下

参考 [TeX.SE](https://tex.stackexchange.com/questions/568732/uline-does-not-work-with-hspace) 与 [问答站](https://wenda.latexstudio.net/q-5448.html) 可以知道
```tex
\documentclass{article}
\usepackage{ulem}
\begin{document}
    \uline{\hspace{2em}}
    % \uline{\mbox{\hspace{2em}}} % 为正确写法
\end{document}
```

会导致错误
```tex 
! Extra }, or forgotten \endgroup.
\UL@stop ...alty \ifnum \lastkern =\thr@@ \egroup
                                                  \egroup \ifdim \wd \UL@box...
l.4     \uline{\hspace{2em}}
```

于是将 `cumcmthesis.cls` 中 346--384 行 (行数可能不对, 需要自行确认) 中的 `\hspace{1em}` 改为 `\mbox{\hspace{1em}}` 即可.