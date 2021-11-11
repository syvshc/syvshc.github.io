---
title: 统计一节中的列表项数量
date: 2021-11-10 11:47:24
categories: 
    - [LaTeX, 列表相关]
tags:
    - [列表相关]
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

## 具体实现

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

3. 在一节中题目结束的位置使用 `\exerend` 命令来将此时的 `totalitems` 的值输出到辅助文件, 写入为形如 `\gdef\totalenumi@i{4}` 的全局命令

    ```tex
    \def\exerend{
        \immediate\write\@mainaux{%
            \string\gdef\string\totalenumi@\romannumeral%
            \csname c@section\endcsname
            {\number\csname c@totalitems\endcsname}
        }%
    }
    ```

4. 在每个 `\section` 开始的时候都将 `totalitems` 的值重置为 0

    ```tex
    \let\s@ction\section
    \def\section{
        \setcounter{totalitems}{0}\s@ction
    }
    ```

5. 定义输出总题数的命令 `\exer@sectoin{}`, 注意到在第一次编译的时候 `\totalenumi@i` 是没有定义的, 需要进行判断

    ```tex
    \def\exer@section#1{\@ifundefined{totalenumi@\romannumeral #1}%
        {0}%
        {\csname totalenumi@\romannumeral #1\endcsname}%
    }
    ```

6. 定义输出总题数的命令:

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

## 实例

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

## 已知的问题

1. ~~当一节中出现带 `[resume]` 选项的 `enumerate` 环境的时候, `totalitems` 会将两个环境结束时的 `enumi` 值相加, 导致计数错误.~~ 已经支持添加 `[resume]`, `[start=]` 等可选参数

2. ~~不支持嵌套~~, 已使用 `\enit@depth` 命令来判断层级, 嵌套的问题被解决. 

## 后续

我在 `tex.se` 上还看到到了一种实现方式, 直接使用 `\label` 来进行辅助文件的输出, 这里是回答的[地址](https://tex.stackexchange.com/a/74617/180617).