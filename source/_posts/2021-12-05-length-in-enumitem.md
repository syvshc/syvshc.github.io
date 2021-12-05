---
title: enumitem 宏包中的长度设置
date: 2021-12-05 10:42:12
categories: 
    - [LaTeX, 列表]
tags:
    - [列表]
    - [LaTeX]
    - [示例]
---

在 [`enumitem` 的宏包手册](http://mirrors.ctan.org/macros/latex/contrib/enumitem/enumitem.pdf) 中有 5 个长度命令 `\leftmargin`, `\itemindent`, `\labelindent`, `\labelwidth` 和 `\labelsep`. 这些长度关系以及互相的制约让人头疼, 而 `enumitem` 的作者也晓得这一点:

> New 3.6 If you find these parameters baffling, you are not alone. You can visualize them
> by writing `\DrawEnumitemLabel` just before the first item (or in first), which draws 4 rules
> from top to bottom, `labelindent`, `labelwidth`, `labelsep`, `itemindent` (thin if positive, thick
> if negative); the `leftmargin` is marked with two vertical rules.

下面来探究一下这些关系应该如何设置.

<!-- more -->

首先声明: **以下内容我没有阅读源码, 只是通过阅读文档以及 [TEX.SE 上的回答](https://tex.stackexchange.com/a/490880/180617) 而进行的个人总结**

## 简介

在 `\leftmargin`, `\itemindent`, `\labelindent`, `\labelwidth` 和 `\labelsep` 这 5 个长度中, 只有 `\labelindent` 是 `enumitem` 宏包提供的, 其余都是 TeX 原生的列表环境的长度, `enumitem` 的作者想让用户可以直接设置左侧到 label 的距离:  `\labelindent` 的数值, 而不是进行一些运算. 而 `\labelindent` 满足如下数量关系:

    \leftmargin + \itemindent = \labelindent + \labelwidth + \labelsep

这是这篇博客共用的外层代码 :

```tex
\documentclass{article}

\usepackage{enumitem}

\newcommand*\printvalue[1]{\texttt{\string #1} : \the #1}
\begin{document}

\end{document}
```

并且我会在每个列表环境中都加入 `\DrawEnumitemLabel` 来指示各长度的关系. 同时我会加入一些手绘的竖线来更清晰的表示各元素的起止位置.

在不进行额外设置的情况下, 可以看到各项的默认值

```tex
\begin{enumerate}
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
\end{enumerate}
```

![默认长度](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/default.png)

## `labelindent`

这个长度命令是用来表示文章或者上一级列表的左侧边缘到 label 盒子右侧边缘的距离, 默认值是 0.0pt. 注意: 这个长度命令通常不会起作用, 除非其他的长度命令被设置为了 `!` 或 `*` (见后文). `labelindent` 会根据其他的长度来进行计算, 哪怕你对它进行了设置, 比如 `labelindent=10pt`.

```tex
\begin{enumerate}[labelindent=10pt]
    \DrawEnumitemLabel
    \item \printvalue\labelindent
\end{enumerate}
```

![单独设置 `labelindent`](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/signle-labelindent.png)

可以看到, 即使我们设置了 `labelindent=10pt`, 这个长度设置依旧被忽略了.

## `itemindent`

`itemindent` 设置的是从 `leftmargin` 右侧到第一行列表内容左侧的长度:

```tex
\begin{enumerate}[itemindent=10pt]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
    \item Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
```

![`itemindent`](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/itemindent.png)

这里第一次出现了 4 条横线, 还是来说明一下它们是什么: 从上到下依次是: `labelindent`, `labelwidth`, `labelsep`, `itemindent`, 其中两条竖线对应的是 `leftmargin` 的两端. 可以看到图中两条红线之间的距离即为 `itemindent` 的长度

## `leftmargin`

`leftmargin` 设置的是从正文左侧到每个 item 中换行后的内容的起始位置之间的距离, 这里为了区分, 同时设置了 `itemindent` 的长度:

```tex
\begin{enumerate}[itemindent=10pt, leftmargin=20pt]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
    \item Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
```

![`leftmargin`](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/leftmargin.png)

可以看到 `leftmargin` 的右侧线正好对应的换行后内容的开始, 其中两条竖线之间的长度为 `leftmargin` 的长度.

## `labelwidth`

`labelwidth` 设置的是各个 `label` 的宽度, 通常来说这一项不需要手动控制, 可以使用 `widest` 参数控制:

```tex
\begin{enumerate}[labelwidth=10pt]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
```

![`labelwidth`](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/labelwidth.png)

## `labelsep`

`labelsep` 设置的是 `labelwidth` 右侧到列表内容左侧之间的距离:

```tex
\begin{enumerate}[labelsep=10pt]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
```

![`labelsep`](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/labelsep.png)

注意到在 `\DrawEnumitemLabel` 中第一条线变粗了, 这是因为通过计算, `labelindent` 的值为负数, 故用粗的线来表示负的 `labelindent`.

## `!`

因为给出的 5 个长度关系构成了一个方程, 所以总有一个参数可以通过其他参数进行计算. 我们可以将这 5 个长度的其中一个设置为 `!`, 来表示该长度是最后通过其他长度计算得到, 在默认情况下为 `labelindent=!`, 这也解释了为什么就算设置了 `labelindent=10pt` 也不会起作用.

```tex
\begin{enumerate}[leftmargin=!, itemindent=10pt, labelindent=\parindent]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
```

![`!`](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/leftmargin!.png)

可以看到, 设置了 `leftmargin=!` 之后, 再对 `labelindent` 进行设置就起了作用.

## `*`

在默认的长度中 `labelindent` 是 20.00003pt, 非常宽, 通常来说不符合我们的需要, 这时候可以手动指定 `labelwidth`, 或者用将某一个长度设置为 `*`, 来直接按着标签的类型 (`\arabic*`, `\roman*` 等) 来设置宽度[^1], 除次之外 `*` 与 `!` 起相同的作用:

[^1]: 如果设置的是 `label=(\Alph*)`, 那么会将 `(M)` 整体的宽度设置为 `labelwidth`.

| `\arabic*` | `\roman*`  | `\alph*` | `\Roman*`  | `\Alph*` |
|:----------:|:----------:|:--------:|:----------:|:--------:|
|    0       |    viii    |     m    |    VIII    |     M    |

```tex
\begin{enumerate}[labelindent=*]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
\end{enumerate}
\begin{enumerate}[labelindent=!]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
\end{enumerate}
```

![`*` and `!`](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/exclamation-and-ast.png)

图中用红线标出了两个 `labelwidth` 的宽度. 从图可以看出, 虽然显示效果相同, 但是设置 `labelindent=!` (也就是默认) 时, `labelwidth` 为默认的 20.00003pt, 而设置 `labelindent=*` 时, `labelwidth` 缩小到了 `0.` 的宽度: 7.7778pt. 如果使用 `align=left` 的话就会看到明显的显示差距.


## `left`

`left` 不是一个长度单位, 是对 `labelindent` 和 `leftmargin` 的简化设置, 它有两种格式:

```tex
left = <labelindent>
left = <labelindent> .. <leftmargin>
```

其中 `left = <labelindent>` 会设置 `leftmargin = *`, `left = <labelindent> .. <leftmargin>` 会设置 `labelsep = *`, 这里为了对比明显, 增加了 `itemindent=10pt`

```tex
\begin{enumerate}[left=\parindent, itemindent=10pt]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item \verb|left=\parindent|
\end{enumerate}
\begin{enumerate}[left=\parindent .. 2\parindent, itemindent=10pt]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item \verb|left=\parindent .. 2\parindent|
\end{enumerate}
```

![left](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/left.png)

可以看到只设置 `labelindent` 时, `labelsep` 为默认的 5pt, `leftmargin` 经过计算, 其值为 17.7778 pt. 在同时设置 `labelindent` 与 `leftmargin` 时, `labelsep` 经过计算, 其值为 17.2222pt.

## `widest` & `align`

### `widest`

可以使用 `widest` 来控制 `labelwidth` 的长度, 这里我们用了 `leftmargin=*`, 后文再说.

```tex
\begin{enumerate}[widest = 10, start=8, leftmargin=*]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
\end{enumerate}
```

![widest](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/widest.png)

### `align`

可以通过 `align=left` 或 `align=right` (默认) 来控制 `label` 的宽度与 `labelwidth` 不同时的对齐方式. 如上图所示, 所有标签都是向右对齐, 当然我们可以设置 `align=left`:

```tex
\begin{enumerate}[widest = 10, start=8, leftmargin=*, align=left]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
\end{enumerate}
```

如果我们不设置 `widest`, 那么它会按着 `*` 节中的表格长度来自动设置, 这时候对于固定的 `labelsep` 会产生不一致的间距:

```tex
\begin{enumerate}[start=8, leftmargin=*, align=left]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
    \item \verb|align=left|
\end{enumerate}
\begin{enumerate}[start=8, leftmargin=*, align=right]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
    \item \verb|align=right| (default)
\end{enumerate}
\begin{enumerate}[start=8, leftmargin=*, widest=10, align=right]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
    \item \verb|align=right| (default)
\end{enumerate}
```

![no `widest`](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/no-widest.png)

图中用红线标出了 `labelwidth` 的长度, 可以看到当没有设置 `widest` 的时候, `label` 会只按照 `0.` 的宽度进行设置, 导致 `label` 溢出. 所以在 `label` 长度不一致的时候推荐使用 `widest` 来干预 `labelwidth` 的长度.

## 嵌套

在嵌套的过程中, 下一层级的 `leftmargin` 的起始位置就是上一层级的 `leftmargin` 的结束位置, 并且要根据层级不同而导致的 `labelwidth` 不同来设置不同的 `itemindent`

```tex
\begin{enumerate}[labelindent=.5\parindent,leftmargin=\parindent, itemindent=5pt, labelsep = *]
    \DrawEnumitemLabel
    \item \printvalue\leftmargin
    \item \printvalue\itemindent
    \item \printvalue\labelsep
    \item \printvalue\labelwidth
    \item \printvalue\labelindent
    \begin{enumerate}[labelindent=.5\parindent,leftmargin=\parindent, itemindent=13pt, labelsep = *]
        \DrawEnumitemLabel
        \item \printvalue\leftmargin
        \item \printvalue\itemindent
        \item \printvalue\labelsep
        \item \printvalue\labelwidth
        \item \printvalue\labelindent
    \end{enumerate}
```

![nested](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/nest.png)

## 一些实例

**以下实例都可以根据 `label` 的宽度来添加 `widest=` 与 `align=`**

### 实例 1

```tex
En un lugar de la Mancha, de cuyo nombre no quiero acordarme,
no ha mucho tiempo que viv\'{\i}a un hidalgo de los de
\begin{enumerate}[labelindent=\parindent,leftmargin=*]
    \DrawEnumitemLabel
    \item lanza en astillero,
    \item adarna antigua,
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
Una olla de algo m\'{a}s vaca que carnero, salpic\'{o}n las m\'{a}snoches, duelos y quebrantos los s\'{a}bados...
```

![Sample 1](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/sample1.png)

可以看到, 由于 `itemindent=0pt`, 换行后的内容与第一行的列表内容是对齐的.

### 实例 2

```tex
En un lugar de la Mancha, de cuyo nombre no quiero acordarme,
no ha mucho tiempo que viv\'{\i}a un hidalgo de los de
\begin{enumerate}[leftmargin=*] % labelindent=0pt by default.
    \DrawEnumitemLabel
    \item lanza en astillero,
    \item adarna antigua,
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
Una olla de algo m\'{a}s vaca que carnero, salpic\'{o}n las m\'{a}snoches, duelos y quebrantos los s\'{a}bados...
```

![Sample 2](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/sample2.png)

与上一例的区别是没有设置 `labelindent`, 这样每个 `label` 都是从 `leftmargin` 的起始位置开始.

### 实例 3

```tex
En un lugar de la Mancha, de cuyo nombre no quiero acordarme,
no ha mucho tiempo que viv\'{\i}a un hidalgo de los de
\begin{enumerate}[leftmargin=\parindent]
    \DrawEnumitemLabel
    \item lanza en astillero,
    \item adarna antigua,
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
Una olla de algo m\'{a}s vaca que carnero, salpic\'{o}n las m\'{a}snoches, duelos y quebrantos los s\'{a}bados...
```

![Sample 3](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/sample3.png)

这一例固定了列表的内容是从正文的缩进位置开始, 而不管序号的位置.

### 实例 4

```tex
En un lugar de la Mancha, de cuyo nombre no quiero acordarme,
no ha mucho tiempo que viv\'{\i}a un hidalgo de los de
\begin{enumerate}[labelindent=\parindent, leftmargin=*, label=\Roman*., widest=III,  align=left]
    \DrawEnumitemLabel
    \item lanza en astillero,
    \item adarna antigua,
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
Una olla de algo m\'{a}s vaca que carnero, salpic\'{o}n las m\'{a}snoches, duelos y quebrantos los s\'{a}bados...
```

![Sample 4](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/sample4.png)

### 实例 5

```tex
En un lugar de la Mancha, de cuyo nombre no quiero acordarme,
no ha mucho tiempo que viv\'{\i}a un hidalgo de los de
\begin{enumerate}[labelindent=\parindent, leftmargin=\parindent, widest=0, itemindent=*]
    \DrawEnumitemLabel
    \item lanza en astillero,
    \item adarna antigua,
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
Una olla de algo m\'{a}s vaca que carnero, salpic\'{o}n las m\'{a}snoches, duelos y quebrantos los s\'{a}bados...
```

![Sample 5](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/sample5.png)

可以看到我们将 `leftmargin` 与 `labelindent` 均设置为了 `\parindent`, 这样可以保证换行后的内容与 `label` 是左对齐的, 同时用 `widest` 来限制了 `labelwidth` 的长度.

### 实例 6

```tex
En un lugar de la Mancha, de cuyo nombre no quiero acordarme,
no ha mucho tiempo que viv\'{\i}a un hidalgo de los de
\begin{enumerate}[labelindent=\parindent, leftmargin=0pt, widest=0, itemindent=*]
    \DrawEnumitemLabel
    \item lanza en astillero,
    \item adarna antigua,
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
Una olla de algo m\'{a}s vaca que carnero, salpic\'{o}n las m\'{a}snoches, duelos y quebrantos los s\'{a}bados...
```

![Sample 6](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/sample6.png)

该例将 `leftmargin` 设置为 0, 来让换行后的内容与正文部分的左侧对齐.

### 实例 7

```tex
En un lugar de la Mancha, de cuyo nombre no quiero acordarme,
no ha mucho tiempo que viv\'{\i}a un hidalgo de los de
\begin{enumerate}[leftmargin=0pt, widest=0, itemindent=0pt,  labelindent=*, align=left]
    \DrawEnumitemLabel
    \item lanza en astillero,
    \item adarna antigua,
    \item Lorem ipsum dolor sit amet, consectetuer adipisc\\ing elit. Ut purus elit, vestibulum ut, placerat ac, adipiscing itae, felis.
    \item Lorem ipsum
\end{enumerate}
Una olla de algo m\'{a}s vaca que carnero, salpic\'{o}n las m\'{a}snoches, duelos y quebrantos los s\'{a}bados...
```

![Sample 7](https://raw.githubusercontent.com/syvshc/image/master/postimg/length-in-enumitem/sample7.png)
