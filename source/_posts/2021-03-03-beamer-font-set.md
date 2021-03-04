---
title: beamer 的字体设置
date: 2021-03-03 19:37:00
categories: 
    - [LaTeX, 字体]
tags:
    - [字体设置]
    - [LaTeX]
    - [示例]
---

在使用 `beamer` 文档类的时候, 可能会有需要更换字体. 

<!-- more -->

如果按照 `article` 的方式设置正文字体:

```latex
\documentclass{beamer}
\usepackage{amsmath, fontspec}
\begin{document}
\begin{frame}
    Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Utpurus elit, vestibulum ut, placerat ac, adipiscing vitae, felis.
    
    \setmainfont{Fira Sans}
    Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Utpurus elit, vestibulum ut, placerat ac, adipiscing vitae, felis.
\end{frame}
\end{document}
```

会发现字体并没有发生变化

![`\setmainfont`](https://pic4.zhimg.com/80/v2-a79642cc1fef73b11effa52b21dc02ca.png)



查阅 `texdoc beamer` 的 18.1 节会发现如果想使用其他字体, 需要设置 `\usefonttheme{professionalfonts}`, 但是添加了该命令后修改依旧失败. 

在群里咨询后得知, `\setmainfont{Fira Sans}` 命令修改的字体为 `rmfamily`, 默认为衬线体. 所以要先将字体设置为衬线体 `\usefonttheme{serif}`, 再进行修改:

```latex
\documentclass{beamer}
\usepackage{amsmath, fontspec}
\usefonttheme{serif}
\begin{document}
\begin{frame}
    Lorem ipsum dolor sit amet, consectetuer adipiscing elit. 
    Utpurus elit, vestibulum ut, placerat ac, adipiscing vitae, felis.
    
    \setmainfont{Fira Sans}
    Lorem ipsum dolor sit amet, consectetuer adipiscing elit. 
    Utpurus elit, vestibulum ut, placerat ac, adipiscing vitae, felis.
\end{frame}
\end{document}
```

![Image](https://pic4.zhimg.com/80/v2-2078e554f7891d8ae674b0dd7d87a97b.png)

另外, 因为 `beamer` 文档类默认正文使用无衬线体, 于是也可以偷鸡地设置 `\setsansfont{Fira Sans}` 但是这样做会导致数学模式的 `\mathrm` 变成衬线体

```latex
\documentclass{beamer}
\usepackage{amsmath}
\usepackage{fontspec}
\setsansfont{Fira Sans}
\begin{document}
\begin{frame}
    Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Utpurus elit, vestibulum ut, placerat ac, adipiscing vitae, felis.
    \[
        \int_{0}^{\pi}\sin x\,\mathrm{d}x=2 
    \]
\end{frame}
\end{document}
```

![Image](https://pic4.zhimg.com/80/v2-94edf62ee34ec47955526aabbba5a438.png)

在 `texdoc fontspec` 中查找原因, 可以在 3.2 节 Maths fonts adjustments 中找到

![Image](https://pic4.zhimg.com/80/v2-6ded00e03780caf7534fb7a3e5f7a9a9.png)

即在默认设置下, `fontspec` 宏包会将数学字体设置成 `Computer Modern`, 于是我们需要用 `no-math` 选项来阻止它, 同时 `\mathrm{d}` 也会调用 `rmfamily` 的字体, 我们将它改为 `\mathsf{d}`:

```latex
\documentclass{beamer}
\usepackage{amsmath}
\usepackage[no-math]{fontspec}
\setsansfont{Fira Sans}
\begin{document}
\begin{frame}
    Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Utpurus elit, vestibulum ut, placerat ac, adipiscing vitae, felis.
    \[
        \int_{0}^{\pi}\sin x\,\mathsf{d}x=2
    \]
\end{frame}
\end{document}
```
这个暂时不知道是什么原因导致的. 如果想正文和数学公式都使用 Fira 系列的字体, 同时保留衬线字体, 可以如下设置:
```latex
\documentclass{beamer}
% \usepackage[no-math]{fontspec}
\usepackage{unicode-math}
\usefonttheme{serif}
% \setsansfont{Fira Sans}
\setmainfont{Fira Sans}
\setsansfont{CMU Serif}
\setmathfont{Fira Math}
\begin{document}
    \begin{frame}
        Handgloves, $\sin$, $\mathrm{Handgloves}$

        \textsf{this is CMU Serif}
    \end{frame}
\end{document}
```
![Image](https://pic4.zhimg.com/80/v2-84d784c2f5396f6b2e529e0d7506b38d.png)

如果设置中文字体, 使用 `ctexbeamer` 文档类并设置 `\setCJK(main, sans, mono)font`, 类比上文即可. 