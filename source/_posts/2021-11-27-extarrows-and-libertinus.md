---
title: libertinus 字体与 extarrows 不兼容的处理
date: 2021-11-27 13:33:25
categories: 
    - [LaTeX, unicode-math]
tags:
    - [unicode-math]
    - [LaTeX]
    - [示例]
---

与群友交流的过程中发现如果将数学字体设置为 `Libertinus Math` 的时候, 使用 `extarrow` 宏包的 `\xLongrightarrow` 会有奇怪的现象:

```tex
\documentclass{article}
\usepackage{amsmath}
\usepackage{extarrows}
\usepackage{unicode-math}
\setmathfont{Libertinus Math}
\begin{document}
    \begin{align*}
        \xLongrightarrow{a^2+b^2=c^2} 
    \end{align*}
\end{document}
```

![错误输出](https://raw.githubusercontent.com/syvshc/image/master/postimg/extarrows-and-libertinus/wrongoutput.png)

<!-- more -->

开始我以为是 `unicode-math` 与 `extarrows` 不兼容, 但是当我将字体设置成 `STIX Two Math` 或者默认的 `Latin Modern Math` 的时候发现并不是 `unicode-math` 的问题:

![对比](https://raw.githubusercontent.com/syvshc/image/master/postimg/extarrows-and-libertinus/compare.png)

那就需要看看源码, 找一下有没有能改的地方. 

首先找了一下 `extarrow.sty` 的源码, 看不太懂

```tex
\RequirePackage{amsmath}
...
\def\xLongrightarrowfill@{\arrowfill@\Relbar\Relbar\Longrightarrow}
\providecommand{\xLongrightarrow}[2][]{%
	\ext@arrow 0099\xLongrightarrowfill@{#1}{#2}}
```

但是发现了两个没有定义就直接使用的命令: `\arrowfill@` 与 `\Relbar`, 那就应该是 `amsmath` 定义的命令, 顺藤摸瓜再去找一下. 

在 `amsmath.sty` 中发现了

```tex
\ams@def\relbar{\mathrel{\mathpalette\mathsm@sh\std@minus}}
\ams@def\Relbar{\mathrel\std@equal}
\def\arrowfill@#1#2#3#4{%
  $\m@th\thickmuskip0mu\medmuskip\thickmuskip\thinmuskip\thickmuskip
   \relax#4#1\mkern-7mu%
   \cleaders\hbox{$#4\mkern-2mu#2\mkern-2mu$}\hfill
   \mkern-7mu#3$%
}
```

依然看不懂, 再顺着 `\Relbar` 定义中的 `\std@equal` 寻找, 找到了这两行

```tex
\@ifundefined{Umathcode}
  {%
    \mathchardef\std@minus\mathcode`\-\relax
    \mathchardef\std@equal\mathcode`\=\relax
  }
  {%
   \Umathcharnumdef\std@minus\Umathcodenum`\-\relax
   \Umathcharnumdef\std@equal\Umathcodenum`\=\relax
  }
```

那就可以知道 `extarrows` 与 `\overline` 等命令的实现方法了: 用 `\longright/leftarrow` 与 `\Longright/leftarrow` 与减号 `-` 或者等号 `=` 进行拼接. 

猜出了原理, 用 `Libertinus Math` 进行一下验证:

![= 与 `\Longrightarrow`](https://raw.githubusercontent.com/syvshc/image/master/postimg/extarrows-and-libertinus/equalandarrow.png)

确实等号与 `\Longrightarrow` 两横之间的间距不同, 也就导致了奇怪的效果. 

知道了问题的话就容易解决了, 直接用其他的字体来替换箭头与等号就可以了[^1] .

[^1]: 不知道直接画一个与箭头的间距相等的等号可不可行. 

```tex
\documentclass{article}
\usepackage{amsmath}
\usepackage{extarrows}
\usepackage{unicode-math}
\setmathfont{Libertinus Math}
\setmathfont{STIX Two Math}[range = {\Longleftarrow, \Longrightarrow, \equal}]
\begin{document}
\begin{align*}
    \xLongleftrightarrow{a^2-b^2=c^2}
\end{align*}
\end{document}
```

![](https://raw.githubusercontent.com/syvshc/image/master/postimg/extarrows-and-libertinus/right.png)

`\Longrightarrow` 的问题解决了, 但是出现了新的问题, 在 `Latin Modern Math` 与 `STIX Two Math` 中的减号 `-` 与箭头 `\longrightarrow` 的粗细或者垂直位置不一致, 这会导致 `\xlong` 系列的箭头变得奇怪, 所以只是这么处理的话会导致箭头风格不统一. 如果不重制 `\Relbar` 与 `\relbar` 的话, 那就祈祷文章种不要同时出现两种箭头吧……