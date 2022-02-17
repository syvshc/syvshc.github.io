---
title: 统计一节中的列表项数量
date: 2021-11-10 11:47:24
categories: 
    - [LaTeX, 列表]
tags:
    - [列表]
    - [LaTeX]
    - [示例]
---

在群里遇到一个需求: 使用 `ctexart` 文档类排版试卷, 使用 `enumerate` 环境生成题目并对题目计数, 希望在每个 `\section` 中显示"本节共xx题", 并在目录中显示, 其中 xx 为该节中的一级列表环境中的 item 数量.

下面来进行实现.

<!-- more -->

根据我在 [tex.se](tex.stackexchange.com) 上的 [提问](https://tex.stackexchange.com/q/622028/180617) 进行修改:

## 实现思路

1. 使用计数器 `totalitems` 来统计每一节的 `\item` 的数量之和
2. 在一节的末尾使用命令将这一节的 `totalitems` 的值输出到辅助文件 `main.aux`
3. 第二次编译的时候再在 `section` 中调用该计数器来显示题目的数量

## 更新

我又傻了, 不需要写两个 `enumerate` 的判断, 直接在 `\item` 内部让它判断自己在不在第一层就好了, 修改重定义 `\item` 的部分为

```tex
  \RenewDocumentCommand{\item}{ s o }{
        \IfNoValueTF{#2}{\it@m}{\it@m[#2]}
        \ifnum\enit@depth=\@ne
            \IfBooleanT{#1}{\addtocounter{totalitems}{-1}}%
            \stepcounter{totalitems}
        \fi
    }
```

然后注释掉 

```tex
\newif\ifenum
...
\LetLtxMacro{\@numerate}{\enumerate}
\LetLtxMacro{\end@numerate}{\endenumerate}
...
\renewcommand{\enumerate}[1][]{%
        \@numerate[#1]
        \ifnum\enit@depth=\@ne
            \enumtrue
        \else
            \enumfalse
        \fi
    }
    \renewcommand{\endenumerate}{
        \ifnum\enit@depth=\@ne 
            \enumfalse
        \else
            \enumtrue
        \fi
        \end@numerate%
    }
```

即可

## 具体实现 (新)

在发现了可以判断层级的命令后, 思路就可以打开了, 这次我们直接修改 `\item` 命令, 让第一层级内的 `\item` 进行记数, 然后再处理嵌套以及 `\item` 的参数问题. 我更喜欢这种方式.

1. 添加判断命令 `\ifenum` 来判断是否处于第一层级

    ```tex
    \newif\ifenum
    ```

2. 这次我们只需要定义一个计数器

    ```tex
    \newcounter{totalitems}
    ```

3. 存储旧的 `enumerate` 环境以及 `\item` 命令

    ```tex
    \usepackage{letltxmacro}
    \LetLtxMacro{\@numerate}{\enumerate}
    \LetLtxMacro{\end@numerate}{\endenumerate}
    \LetLtxMacro{\it@m}{\item}
    ```

4. 重定义 `\item` 命令, 使它具有以下功能: 如果使用 `\item*` 则不进行计数, 同时可以使用 `\item[para]` 来进行自定义序号, 如可以写 `\item[附加题]` 等等, 该功能默认计数, 如果不想计数也可以使用 `\item*[para]` 来取消计数. **更新: 需要将 `\IfBooleanT` 放在 `\ifenum` 内部, 防止在非第一层级中误用 `\item*` 造成的计数错误**:

    ```tex
    \DeclareDocumentCommand{\item}{ s o }{
        \IfNoValueTF{#2}{\it@m}{\it@m[#2]}
        \ifenum
            \IfBooleanT{#1}{\addtocounter{totalitems}{-1}}%
            \stepcounter{totalitems}
        \fi
    }
    ```

5. 然后来确定什么时候打开和关闭判断的开关: 由于嵌套的存在, 我们需要在非第一层的环境开始时关闭 `\ifenum`, 并在非第一层的环境结束时开启 `\ifenum`. 如果出现了多层嵌套或者连续嵌套也不会影响, 因为 `\ifenum` 总会在非第一层环境的开始时被关闭:

    ```tex
    \renewcommand{\enumerate}[1][]{%
        \@numerate[#1]
        \ifnum\enit@depth=\@ne
            \enumtrue
        \else
            \enumfalse
        \fi
    }
    \renewcommand{\endenumerate}{
        \ifnum\enit@depth=\@ne 
            \enumfalse
        \else
            \enumtrue
        \fi
        \end@numerate%
    }
    ```

6. 在一节中题目结束的位置使用 `\exerend` 命令来将此时的 `totalitems` 的值输出到辅助文件, 写入为形如 `\gdef\totalenumi@i{4}` 的全局命令

    ```tex
    \def\exerend{
        \immediate\write\@mainaux{%
            \string\gdef\string\totalenumi@\romannumeral%
            \csname c@section\endcsname
            {\number\csname c@totalitems\endcsname}
        }%
    }
    ```

7. 在每个 `\section` 开始的时候都将 `totalitems` 的值重置为 0

    ```tex
    \let\s@ction\section
    \def\section{
        \setcounter{totalitems}{0}\s@ction
    }
    ```

8. 定义输出总题数的命令 `\exer@sectoin{}`, 注意到在第一次编译的时候 `\totalenumi@i` 是没有定义的, 需要进行判断

    ```tex
    \def\exer@section#1{\@ifundefined{totalenumi@\romannumeral #1}%
        {0}%
        {\csname totalenumi@\romannumeral #1\endcsname}%
    }
    ```

9. 定义输出总题数的命令:

    ```tex
    \def\outputexer{
        (There \ifnum \exer@section{\c@section}=0%
            is ??{} exercise%
        \else%
            \ifnum \exer@section{\c@section}=1%
                is 1 exercise%
            \else%
                are \exer@section{\c@section} exercises%
            \fi%
        \fi.)
    }
    ```

    由于英文中有 is 和 are 以及单复数的关系, 就需要多一层判断. 如果是中文的话, 连带着上一个命令可以直接写为

    ```tex
    \def\exer@sectionzh#1{%
    \@ifundefined{totalenumi@\romannumeral #1}%
        {??}%
        {\csname totalenumi@\romannumeral #1\endcsname}%
    }
    \def\outputexerzh{%
        (本节共有 \exer@sectionzh{\c@section}题.)
    }
    ```

## 实例 (新)

```tex
\documentclass{ctexart}
\usepackage{enumitem}
\usepackage[margin=2.5cm]{geometry}
\makeatletter
\newif\ifenum

\newcounter{totalitems}
\usepackage{letltxmacro}
\LetLtxMacro{\@numerate}{\enumerate}
\LetLtxMacro{\end@numerate}{\endenumerate}
\LetLtxMacro{\it@m}{\item}
\DeclareDocumentCommand{\item}{ s o }{
    \IfNoValueTF{#2}{\it@m}{\it@m[#2]}
    \ifenum
        \IfBooleanT{#1}{\addtocounter{totalitems}{-1}}%
        \stepcounter{totalitems}
    \fi
}
\renewcommand{\enumerate}[1][]{%
    \@numerate[#1]
    \ifnum\enit@depth=\@ne
        \enumtrue
    \else
        \enumfalse
    \fi
}
\renewcommand{\endenumerate}{
    \ifnum\enit@depth=\@ne 
        \enumfalse
    \else
        \enumtrue
    \fi
    \end@numerate%
}

\def\exerend{
    \immediate\write\@mainaux{%
        \string\gdef\string\totalenumi@\romannumeral%
        \csname c@section\endcsname
        {\number\csname c@totalitems\endcsname}
    }%
}

\let\s@ction\section
\def\section{
    \setcounter{totalitems}{0}\s@ction
}

\def\exer@section#1{\@ifundefined{totalenumi@\romannumeral #1}%
    {0}%
    {\csname totalenumi@\romannumeral #1\endcsname}%
}

\def\outputexer{
    (There \ifnum \exer@section{\c@section}=0%
        is ??{} exercise%
    \else%
        \ifnum \exer@section{\c@section}=1%
            is 1 exercise%
        \else%
            are \exer@section{\c@section} exercises%
        \fi%
    \fi.)
}
\def\exer@sectionzh#1{%
\@ifundefined{totalenumi@\romannumeral #1}%
    {??}%
    {\csname totalenumi@\romannumeral #1\endcsname}%
}
\def\outputexerzh{%
    (本节共有 \exer@sectionzh{\c@section}题.)
}
\makeatother

\begin{document}
\tableofcontents
\section{Optional, nested, and resume\outputexer}
\begin{enumerate}
    \item test
    \begin{enumerate}
        \item this is the second level of enumerate
        \begin{enumerate}
            \item this is the third level of enumerate
        \end{enumerate}
        \item* this is a star verion in the second level, will do nothing
    \end{enumerate}
    \item[option] this is an optional paramater without star.
\end{enumerate}

\begin{enumerate}[resume]
    \item this is a resume version
\end{enumerate}
\exerend

\section{带星号以及可选参数\outputexerzh}
\begin{enumerate}
    \item test
    \item*[option] this is a star version with an optional paramater.
\end{enumerate}

\begin{enumerate}
    \item test
    \item* this is a star version
\end{enumerate}
\exerend

\section{A regular enumerate} 
\begin{enumerate}
    \item test
\end{enumerate}

\section{A single item\outputexer}
\begin{enumerate}
    \item just one item
\end{enumerate}
\exerend
\end{document}
```

使用 `xelatex` 编译三次后可以输出

![new output](https://raw.githubusercontent.com/syvshc/image/master/postimg/compute-total-items/new.png)

## 具体实现 (旧)

1. 新定义计数器 `totalitems`, `itemsinenum`, `beginitems`, `enditems`. 分别用来存放: 一节中的所有 item 的数量, 一个 `enumerate` 环境中的 item 数量, `enumerate` 环境开始以及结束时的 `enumi` 的值.

    ```tex
    \newcounter{totalitems}
    \newcounter{itemsinenum}
    \newcounter{beginitems}
    \newcounter{enditems}
    ```

2. 重新定义 `enumerate` 环境, 使其可以添加可选参数, 并且在开始与结束时记录 `enumi` 的值, 使用 `itemsinenum` 计数器来存储二者之差, 也就是当前环境中 item 的数量, 然后把这个数量添加到 `totalitems` 计数器中. 这里使用了 `\enit@depth` 命令来判断是否处于最高级的 `enumerate` 环境中

    ```tex
    \usepackage{letltxmacro}
    \LetLtxMacro{\@numerate}{\enumerate}
    \LetLtxMacro{\end@numerate}{\endenumerate}

    \renewcommand{\enumerate}[1][]{%
        \@numerate[#1]
        \ifnum\enit@depth=\@ne 
            \setcounter{beginitems}{\arabic{enumi}}
        \fi
    }
    \renewcommand{\endenumerate}{
        \ifnum\enit@depth=\@ne 
            \setcounter{enditems}{\arabic{enumi}}
            \setcounter{itemsinenum}{\numexpr \c@enditems - \c@beginitems \relax}
            \addtocounter{totalitems}{\arabic{itemsinenum}}
        \fi
        \end@numerate%
    }
    ```

3. 接下来就是记录题数, 输出题数的过程, 与上文相同, 这里不多赘述.

## 实例 (旧)

```tex
\documentclass{ctexart}
\usepackage{enumitem}
\makeatletter
\newcounter{totalitems}
\newcounter{itemsinenum}
\newcounter{beginitems}
\newcounter{enditems}

\usepackage{letltxmacro}
\LetLtxMacro{\@numerate}{\enumerate}
\LetLtxMacro{\end@numerate}{\endenumerate}

\renewcommand{\enumerate}[1][]{%
    \@numerate[#1]
    \ifnum\enit@depth=\@ne 
        \setcounter{beginitems}{\arabic{enumi}}
    \fi
}
\renewcommand{\endenumerate}{
    \ifnum\enit@depth=\@ne 
        \setcounter{enditems}{\arabic{enumi}}
        \setcounter{itemsinenum}{\numexpr \c@enditems - \c@beginitems \relax}
        \addtocounter{totalitems}{\arabic{itemsinenum}}
    \fi
    \end@numerate%
}

\def\exerend{
    \immediate\write\@mainaux{%
        \string\gdef\string\totalenumi@\romannumeral%
        \csname c@section\endcsname
        {\number\csname c@totalitems\endcsname}
    }%
}

\let\s@ction\section
\def\section{
    \setcounter{totalitems}{0}\s@ction
}

\def\exer@section#1{\@ifundefined{totalenumi@\romannumeral #1}%
    {0}%
    {\csname totalenumi@\romannumeral #1\endcsname}%
}

\def\outputexer{
    (There \ifnum \exer@section{\c@section}=0%
        is ??{} exercise%
    \else%
        \ifnum \exer@section{\c@section}=1%
            is 1 exercise%
        \else%
            are \exer@section{\c@section} exercises%
        \fi%
    \fi.)
}

\def\exer@sectionzh#1{%
\@ifundefined{totalenumi@\romannumeral #1}%
    {??}%
    {\csname totalenumi@\romannumeral #1\endcsname}%
}
\def\outputexerzh{%
    (本节共有 \exer@sectionzh{\c@section}题.)
}
\makeatother
\begin{document}
\tableofcontents
\section{One\outputexer}
\begin{enumerate}
    \item test
    \begin{enumerate}
        \item this is the second level of enumerate
    \end{enumerate}
    \item test
\end{enumerate}

\begin{enumerate}[resume]
    \item test
\end{enumerate}
\exerend

\section{Two\outputexerzh}
\begin{enumerate}[start=5]
    \item test
    \item test
\end{enumerate}

\begin{enumerate}
    \item test
\end{enumerate}
\exerend

\section{No output} 
\begin{enumerate}
    \item test
\end{enumerate}

\section{Three\outputexer}
\begin{enumerate}
    \item test
\end{enumerate}
\exerend
\end{document}
```

三次 `xelatex` 编译后显示效果为

![output](https://raw.githubusercontent.com/syvshc/image/master/postimg/compute-total-items/20211111094748.png)

## ~~已知的问题~~

1. ~~当一节中出现带 `[resume]` 选项的 `enumerate` 环境的时候, `totalitems` 会将两个环境结束时的 `enumi` 值相加, 导致计数错误.~~ 已经支持添加 `[resume]`, `[start=]` 等可选参数

2. ~~不支持嵌套~~, 已使用 `\enit@depth` 命令来判断层级, 嵌套的问题被解决.

## 后续

我在 `tex.se` 上还看到到了一种实现方式, 直接使用 `\label` 来进行辅助文件的输出, 这里是回答的[地址](https://tex.stackexchange.com/a/74617/180617), 这个方法局限性较大, 不采用

这个需求应该是结束了, 欢迎测试和评论 :>
