---
title: 用 LaTeX3 实现一些日期相关的功能
date: 2022-02-16 22:31:45
categories: 
    - [LaTeX, LaTeX3]
tags:
    - [LaTeX3]
    - [LaTeX]
    - [示例]
---

在给家教学生出题的过程中想实现节标题的自动化, 由于题目是每天都有, 所以我想交替用 `\examsection` 和 `\answersection` 来输出节标题, 并且设置页眉和目录, 目标效果如下:

```tex
\begin{document}
\setdate[2022, 2, 14]
\examsection
\answersection
\examsection*
\answersection*
\end{document}
```

![目标效果](https://raw.githubusercontent.com/syvshc/image/master/postimg/date-function-with-l3/goal.png)

<!-- more -->

**我刚刚开始学习 LaTeX3, 对一些函数的使用可能不太得当, 欢迎指出.**

## 实现思路

首先我们定义一些变量:

```tex
% 定义年, 月, 日, 后文注释中用 year, month, day 来指代
\int_new:N \g__dailyexam_year_int
\int_new:N \g__dailyexam_month_int
\int_new:N \g__dailyexam_day_int
% 定义最大的月数, 每月的最大天数, 以及特殊的二月的天数
\int_new:N \c__dailyexam_month_max_int
\int_set:Nn \c__dailyexam_month_max_int { 12 }
\int_new:N \l__dailyexam_day_max_int
\int_new:N \l__dailyexam_day_feb_max_int
% 定义用来接受 \setdate 的参数的 clist
\clist_new:N \l__dailyexam_date_clist
```

然后定义一个 `prop`, 来存储每个月的名称, 在这里用来输出报错信息: 

```tex
\prop_const_from_keyval:Nn \c__dailyexam_month_prop
  {
    { 1 } = { January },
    { 2 } = { February },
    { 3 } = { March }, 
    { 4 } = { April }, 
    { 5 } = { May }, 
    { 6 } = { June }, 
    { 7 } = { July },
    { 8 } = { August },
    { 9 } = { September },
    { 10 } = { October }, 
    { 11 } = { November }, 
    { 12 } = { December}
  }
```

再定义一些信息, 分别为不合法的年, 月, 日, 因为闰年是从 1582 年的格里高利历开始的记法, 所以这里年要大于 1582 年. 

```tex
\msg_new:nnn { dailyexam } { year~is~illegal } { "year"~must~later~than~1582 }
\msg_new:nnn { dailyexam } { month~is~illegal } { "month"~must~be~an~integer~among~1,~2,~3,...,~12 }
\msg_new:nnn { dailyexam } { day~is~illegal } { #1~of~#2~has~only~#3~days, \\
                                                "day"~must~be~an~integer~among~1,~2,~3,...,~#3 }
% 示例输出为
% ! Package dailyexam Error: February of 2022 has only 28 days,
% (dailyexam)                "day" must be an integer among 1, 2, 3,..., 28
```

然后我们来根据月份来设置最大天数 `\l_dailyexam_day_max_int` :

```tex
% 设置 month 月份下最大的天数,
\cs_new:Nn \__dailyexam_set_max_day: 
  {
    % 首先根据大小月规则来设置除二月外的最大天数
    \int_case:nnT { \g__dailyexam_month_int }
      {
        { 1 } { }
        { 3 } { }
        { 5 } { }
        { 7 } { }
        { 8 } { }
        { 10 } { }
        { 12 } { }
      }
      {
        \int_set:Nn \l__dailyexam_day_max_int { 31 }
      }

    \int_case:nnT { \g__dailyexam_month_int }
      {
        { 4 } { }
        { 6 } { }
        { 9 } { }
        { 11 } { }
      }
      {
        \int_set:Nn \l__dailyexam_day_max_int { 30 }
      }
      \int_compare:nNnTF { \g__dailyexam_year_int } < { 1582 }
      {
        % 如果被判断的年在 1582 年之前, 则输出报错信息
        \msg_error:nn { dailyexam } { year~is~illegal }
      }
      {
        % 如果被判断的年在 1582 年之后, 则需要判断该年是否是闰年
        % 规则为: 如果年份能被 100 整除, 则它需要整除 400;
        %         如果年份不能被 100 整除, 则它需要整除 4;
        %         以上两种情况为闰年, 二月有 29 天
        %         否则该年为平年, 二月有 28 天
        \bool_if:nTF 
          {
            (
              \int_compare_p:nNn { \int_mod:nn { \g__dailyexam_year_int } { 100 } } = { 0 } && 
              \int_compare_p:nNn { \int_mod:nn { \g__dailyexam_year_int } { 400 } } = { 0 }
            ) 
            ||
            (
              ! \int_compare_p:nNn { \int_mod:nn { \g__dailyexam_year_int } { 100 } } = { 0 } &&
              \int_compare_p:nNn { \int_mod:nn { \g__dailyexam_year_int } { 4 } } = { 0 }
            )
          }
          {
            \int_set:Nn \l__dailyexam_day_feb_max_int { 29 }
          }
          {
            \int_set:Nn \l__dailyexam_day_feb_max_int { 28 }
          }
      }
    % 判断该月是否为二月, 如果是, 就把最大天数设置为二月最大天数
    \int_compare:nNnT { \g__dailyexam_month_int } = { 2 }
      {
        \int_set_eq:NN \l__dailyexam_day_max_int \l__dailyexam_day_feb_max_int
      }
  }
```

下面是关键的一个函数, 将给定的一个日期向后移动一天:

```tex
% 将日期改为下一天, 注意这里的日期更改都需要是全局的, 否则在 \section 的参数中表现为局部变化 
\cs_new:Nn \__dailyexam_next_day:
  {
    % 判断 day 是否等于当月的 day_max
    \int_compare:nNnTF { \g__dailyexam_day_int } = { \l__dailyexam_day_max_int }
      {
        % 如果日期等于 day_max, 那么进入了新的一个月, 将 day 重设为 1
        \int_gset:Nn \g__dailyexam_day_int { 1 }
        \int_compare:nNnTF { \g__dailyexam_month_int } = { \c__dailyexam_month_max_int }
          {
            % 如果这时是十二月, 即该天为 12.31, 
            % 那么进入了新的一年, 将 month 重设为 1, 并将 year 加 1 
            \int_gset:Nn \g__dailyexam_month_int { 1 }
            \int_gincr:N \g__dailyexam_year_int
          }
          {
            % 如果不是十二月, 那么只需要将 month 加 1
            \int_gincr:N \g__dailyexam_month_int
          }
        % 只要进入了新的一个月, 就需要重新设置 day_max
        \__dailyexam_set_max_day: 
      }
      {
        % 如果 day 不等于 day_max, 那么只需要将 day 加 1
        \int_gincr:N \g__dailyexam_day_int
      }
  }
```

由于我们要使用 `\setdate` 来设置起始日期, 那么就需要防止用户输入不合法的日期, 如 13 月 32 日:

```tex
% 判断 \setdate 所接受的日期是否合法
\cs_new:Nn \__dailyexam_if_legal:
  {
    % 首先是对 year 的判断, 同上
    \int_compare:nNnT { \g__dailyexam_year_int } < { 1582 }
      {
        \msg_error:nn { dailyexam } { year~is~illegal }
      }
    % 判断 month 是否是在 1 -- 12 之间
    \int_compare:nTF
      {
        0 < \g__dailyexam_month_int <= \c__dailyexam_month_max_int
      }
      {
        % 如果 month 合法, 那么就可以设置当月的 day_max, 用来对 day 进行判断
        \__dailyexam_set_max_day:
      }
      {
        % 如果 month 不合法, 输出报错信息
        \msg_error:nn { dailyexam } { month~is~illegal }
      }
    % 判断 day 是否在 1 -- day_max 之间
    \int_compare:nF
      {
        0 < \g__dailyexam_day_int <= \l__dailyexam_day_max_int
      }
      {
        % 如果 day 不合法, 输出报错信息, 报错信息的示例在 \mas_new 部分
        \exp_args:NNx \prop_get:NnN \c__dailyexam_month_prop 
          { \int_use:N \g__dailyexam_month_int } 
          \l_tmpb_tl
        \msg_error:nnxxx { dailyexam } { day~is~illegal } 
          { \tl_use:N \l_tmpb_tl } 
          { \int_use:N \g__dailyexam_year_int } 
          { \int_use:N \l__dailyexam_day_max_int }
      }
  }
```

下面来进行 `\setdate` 的内部函数的编写:

```tex
% 这里设置另一个重要的函数, 设置起始日期
% 我们规定输入的参数为一个逗号分隔列表 clist
\cs_new:Npn \__dailyexam_setdate:n #1
  {
    % 将参数传入 clist, 参数是否合法在赋值后判断
    \clist_set:Nn \l__dailyexam_date_clist { #1 }
    \int_case:nnTF { \clist_count:N \l__dailyexam_date_clist }
      {
        % 如果参数列表里有三项, 也就是对应着 year, month, day,
        % 那么将第一项设置为 year, 再将第一项 pop 到一个不再使用的临时变量中,
        % 来保证现在的 clist 中剩余 month 和 day
        % 注意, 这里我们不能直接用 \l_tmpa_tl 来对 year 赋值, 因为数据类型不同
        { 3 } 
        { 
          \exp_args:NNx \int_gset:Nn \g__dailyexam_year_int 
            { \clist_item:Nn \l__dailyexam_date_clist { 1 } }
          \clist_pop:NN \l__dailyexam_date_clist \l_tmpa_tl 
        }
        % 如果参数列表中有两项, 对应着 month, day,
        % 这时候将 year 设置为编译时的系统年份
        { 2 } { \int_gset_eq:NN \g__dailyexam_year_int \c_sys_year_int }
      }
      {
        % 经过上两步的筛选, 可以保证此时 clist 中只有两项: month 和 day
        % 再将它们赋值到 month 和 day
        \exp_args:NNx \int_gset:Nn \g__dailyexam_month_int 
          { \clist_item:Nn \l__dailyexam_date_clist { 1 } }
        \exp_args:NNx \int_gset:Nn \g__dailyexam_day_int 
          { \clist_item:Nn \l__dailyexam_date_clist { 2 } }
        % 至此 year, month 和 day 都已经赋值完毕, 进行合法性检验
        \__dailyexam_if_legal:
      }
      {
        % 如果参数数量不是 2 或 3 个, 那么视为输入格式错误
        \msg_new:nnn { dailyexam } { date~type~error } 
          { You~must~input~"year,month,day"~or~"month,day"~type~argument. }
        \msg_error:nn { dailyexam } { date~type~error }
      }
  }
```

下面是输出当前日期的函数:

```tex
% 用来显示当前的日期, 并用 year 参数来决定是否输出 year, 格式为 year.month.day
\cs_set:Npn \__dailyexam_current_date:n #1
  {
    \str_if_eq:nnT { #1 } { year }
      {
        \int_use:N \g__dailyexam_year_int .
      }
    \int_use:N \g__dailyexam_month_int .
    \int_use:N \g__dailyexam_day_int
  }
```

接下来是用户层的命令:

```tex
% 输出当前日期的函数 \currentdate
\NewDocumentCommand { \currentdate } { o } { \__dailyexam_current_date:n { #1 } }

% 设置起始日期的函数 \setdate, 以编译时的系统年月日作为默认值
\NewDocumentCommand { \setdate } { O{ \int_use:N \c_sys_year_int, \int_use:N \c_sys_month_int, \int_use:N \c_sys_day_int } }
  {
    \__dailyexam_setdate:n { #1 }
  }

% 日期加 1 的函数 \nextdate
\NewDocumentCommand { \nextdate } { }
  {
    \__dailyexam_next_day:
  }
```

由于我们还有页眉的要求, 并需要控制展开的顺序, 所以先将 `\fancyhead` 与 `\section*` 设置为 LaTeX3 格式的函数, 同时给 `\section*` 加入了目录

```tex
% 将 \fancyhead, \section* 设置为 l3 格式的函数
\cs_new:Npn \fancyhdr_fancyhead:nn #1#2
  {
    \fancyhead[#1]{#2}
  }
\cs_new:Npn \section_star:n #1 
  {
    \section*{ #1 }
    \addcontentsline{toc}{section}{#1}
  }
```

最后是目标中的两个函数 `\examsection` 和 `\answersection`:

```tex
% 用来输出题目的节标题, 不带节序号, 带星号的版本会输出 year
% \answersection 同理
\NewDocumentCommand { \examsection } { s } 
  { 
    \clearpage 
    % 将由星号控制的日期格式存入临时 tl 变量
    \tl_set:Nn \l_tmpa_tl 
      { 
        \IfBooleanTF { #1 } 
          { \__dailyexam_current_date:n { year } } 
          { \__dailyexam_current_date:n { } } 
      }
    % 控制宏展开来输出节标题与设置页眉
    \exp_args:Nx \section_star:n { Exercise~of~\tl_use:N \l_tmpa_tl } 
    \exp_args:Nnx \fancyhdr_fancyhead:nn { L } { Exercise~of~\tl_use:N \l_tmpa_tl }
  }

\NewDocumentCommand { \answersection } { s } 
  { 
    \clearpage 
    \tl_set:Nn \l_tmpa_tl 
    { \IfBooleanTF { #1 } 
        { \__dailyexam_current_date:n { year } } 
        { \__dailyexam_current_date:n { } } 
    }
    \exp_args:Nx \section_star:n { Answer~of~\tl_use:N \l_tmpa_tl } 
    % 这里是要控制宏展开的关键, 页眉会在函数结束后再输出, 
    % 由于 \__dailyexam_next_date: 的作用, 在输出页眉的时候日期已经到达下一天
    % 所以要提前展开 \l_tmpa_tl
    \exp_args:Nnx \fancyhdr_fancyhead:nn { L } { Answer~of~\tl_use:N \l_tmpa_tl }
    % 一天的答案写完后将日期推进一天, 来为下一次的 \examsection 做准备
    \__dailyexam_next_date:
  }
```

## 完整代码

读者可自行更改 `\setdate` 的参数来查看 `\msg_error` 的效果. 为了节约空间, 输出省略了大片的空白

```tex
\documentclass{article}
\usepackage{fancyhdr}
\pagestyle{fancy}
\fancyhf{}
\fancyhead[R]{\thepage}

\ExplSyntaxOn
% 将 \fancyhead, \section* 设置为 l3 格式的函数
\cs_new:Npn \fancyhdr_fancyhead:nn #1#2
  {
    \fancyhead[#1]{#2}
  }
\cs_new:Npn \section_star:n #1 
  {
    \section*{ #1 }
    \addcontentsline{toc}{section}{#1}
  }

% 定义年, 月, 日, 后文注释中用 year, month, day 来指代
\int_new:N \g__dailyexam_year_int
\int_new:N \g__dailyexam_month_int
\int_new:N \g__dailyexam_day_int
% 定义最大的月数, 每月的最大天数, 以及特殊的二月的天数
\int_new:N \c__dailyexam_month_max_int
\int_set:Nn \c__dailyexam_month_max_int { 12 }
\int_new:N \l__dailyexam_day_max_int
\int_new:N \l__dailyexam_day_feb_max_int
% 定义用来接受 \setdate 的参数的 clist
\clist_new:N \l__dailyexam_date_clist

\prop_const_from_keyval:Nn \c__dailyexam_month_prop
  {
    { 1 } = { January },
    { 2 } = { February },
    { 3 } = { March }, 
    { 4 } = { April }, 
    { 5 } = { May }, 
    { 6 } = { June }, 
    { 7 } = { July },
    { 8 } = { August },
    { 9 } = { September },
    { 10 } = { October }, 
    { 11 } = { November }, 
    { 12 } = { December}
  }

\msg_new:nnn { dailyexam } { year~is~illegal } { "year"~must~later~than~1582 }
\msg_new:nnn { dailyexam } { month~is~illegal } { "month"~must~be~integer~among~1,~2,~3,...,~12 }
\msg_new:nnn { dailyexam } { day~is~illegal } { #1~of~#2~has~only~#3~days, \\
                                                "day"~must~be~integer~among~1,~2,~3,...,~#3 }
% 示例输出为
% ! Package dailyexam Error: February of 2022 has only 28 days,
% (dailyexam)                "day" must be integer among 1, 2, 3,..., 28

% 设置 month 月份下最大的天数 day_max,
\cs_new:Nn \__dailyexam_set_max_day: 
  {
    % 首先根据大小月规则来设置除二月外的 day_max
    \int_case:nnT { \g__dailyexam_month_int }
      {
        { 1 } { }
        { 3 } { }
        { 5 } { }
        { 7 } { }
        { 8 } { }
        { 10 } { }
        { 12 } { }
      }
      {
        \int_set:Nn \l__dailyexam_day_max_int { 31 }
      }

    \int_case:nnT { \g__dailyexam_month_int }
      {
        { 4 } { }
        { 6 } { }
        { 9 } { }
        { 11 } { }
      }
      {
        \int_set:Nn \l__dailyexam_day_max_int { 30 }
      }
      \int_compare:nNnTF { \g__dailyexam_year_int } < { 1582 }
      {
        % 如果被判断的年在 1582 年之前, 则输出报错信息
        \msg_error:nn { dailyexam } { year~is~illegal }
      }
      {
        % 如果被判断的年在 1582 年之后, 则需要判断该年是否是闰年
        % 规则为: 如果年份能被 100 整除, 则它需要整除 400;
        %         如果年份不能被 100 整除, 则它需要整除 4;
        %         以上两种情况为闰年, 二月有 29 天
        %         否则该年为平年, 二月有 28 天
        \bool_if:nTF 
          {
            (
              \int_compare_p:nNn { \int_mod:nn { \g__dailyexam_year_int } { 100 } } = { 0 } && 
              \int_compare_p:nNn { \int_mod:nn { \g__dailyexam_year_int } { 400 } } = { 0 }
            ) 
            ||
            (
              ! \int_compare_p:nNn { \int_mod:nn { \g__dailyexam_year_int } { 100 } } = { 0 } &&
              \int_compare_p:nNn { \int_mod:nn { \g__dailyexam_year_int } { 4 } } = { 0 }
            )
          }
          {
            \int_set:Nn \l__dailyexam_day_feb_max_int { 29 }
          }
          {
            \int_set:Nn \l__dailyexam_day_feb_max_int { 28 }
          }
      }
    % 判断该月是否为二月, 如果是, 就把 day_max设置为二月 day_max
    \int_compare:nNnT { \g__dailyexam_month_int } = { 2 }
      {
        \int_set_eq:NN \l__dailyexam_day_max_int \l__dailyexam_day_feb_max_int
      }
  }

% 将日期改为下一天, 注意这里的日期更改都需要是全局的, 否则在 \section 的参数中表现为局部变化 
\cs_new:Nn \__dailyexam_next_date:
  {
    % 判断 day 是否等于当月的 day_max
    \int_compare:nNnTF { \g__dailyexam_day_int } = { \l__dailyexam_day_max_int }
      {
        % 如果日期等于 day_max, 那么进入了新的一个月, 将 day 重设为 1
        \int_gset:Nn \g__dailyexam_day_int { 1 }
        \int_compare:nNnTF { \g__dailyexam_month_int } = { \c__dailyexam_month_max_int }
          {
            % 如果这时是十二月, 即该天为 12.31, 
            % 那么进入了新的一年, 将 month 重设为 1, 并将 year 加 1 
            \int_gset:Nn \g__dailyexam_month_int { 1 }
            \int_gincr:N \g__dailyexam_year_int
          }
          {
            % 如果不是十二月, 那么只需要将 month 加 1
            \int_gincr:N \g__dailyexam_month_int
          }
        % 只要进入了新的一个月, 就需要重新设置 day_max
        \__dailyexam_set_max_day: 
      }
      {
        % 如果 day 不等于 day_max, 那么只需要将 day 加 1
        \int_gincr:N \g__dailyexam_day_int
      }
  }

% 判断 \setdate 所接受的日期是否合法
\cs_new:Nn \__dailyexam_if_legal:
  {
    % 首先是对 year 的判断, 同上
    \int_compare:nNnT { \g__dailyexam_year_int } < { 1582 }
      {
        \msg_error:nn { dailyexam } { year~is~illegal }
      }
    % 判断 month 是否是在 1 -- 12 之间
    \int_compare:nTF
      {
        0 < \g__dailyexam_month_int <= \c__dailyexam_month_max_int
      }
      {
        % 如果 month 合法, 那么就可以设置当月的 day_max, 用来对 day 进行判断
        \__dailyexam_set_max_day:
      }
      {
        % 如果 month 不合法, 输出报错信息
        \msg_error:nn { dailyexam } { month~is~illegal }
      }
    % 判断 day 是否在 1 -- day_max 之间
    \int_compare:nF
      {
        0 < \g__dailyexam_day_int <= \l__dailyexam_day_max_int
      }
      {
        % 如果 day 不合法, 输出报错信息, 报错信息的示例在 \mas_new 部分
        \exp_args:NNx \prop_get:NnN \c__dailyexam_month_prop 
          { \int_use:N \g__dailyexam_month_int } 
          \l_tmpb_tl
        \msg_error:nnxxx { dailyexam } { day~is~illegal } 
          { \tl_use:N \l_tmpb_tl } 
          { \int_use:N \g__dailyexam_year_int } 
          { \int_use:N \l__dailyexam_day_max_int }
      }
  }

% 这里设置另一个重要的函数, 设置起始日期
% 我们规定输入的参数为一个逗号分隔列表 clist
\cs_new:Npn \__dailyexam_setdate:n #1
  {
    % 将参数传入 clist, 参数是否合法在赋值后判断
    \clist_set:Nn \l__dailyexam_date_clist { #1 }
    \int_case:nnTF { \clist_count:N \l__dailyexam_date_clist }
      {
        % 如果参数列表里有三项, 也就是对应着 year, month, day,
        % 那么将第一项设置为 year, 再将第一项 pop 到一个不再使用的临时变量中,
        % 来保证现在的 clist 中剩余 month 和 day
        % 注意, 这里我们不能直接用 \l_tmpa_tl 来对 year 赋值, 因为数据类型不同
        { 3 } 
        { 
          \exp_args:NNx \int_gset:Nn \g__dailyexam_year_int 
            { \clist_item:Nn \l__dailyexam_date_clist { 1 } }
          \clist_pop:NN \l__dailyexam_date_clist \l_tmpa_tl 
        }
        % 如果参数列表中有两项, 对应着 month, day,
        % 这时候将 year 设置为编译时的系统年份
        { 2 } { \int_gset_eq:NN \g__dailyexam_year_int \c_sys_year_int }
      }
      {
        % 经过上两步的筛选, 可以保证此时 clist 中只有两项: month 和 day
        % 再将它们赋值到 month 和 day
        \exp_args:NNx \int_gset:Nn \g__dailyexam_month_int 
          { \clist_item:Nn \l__dailyexam_date_clist { 1 } }
        \exp_args:NNx \int_gset:Nn \g__dailyexam_day_int 
          { \clist_item:Nn \l__dailyexam_date_clist { 2 } }
        % 至此 year, month 和 day 都已经赋值完毕, 进行合法性检验
        \__dailyexam_if_legal:
      }
      {
        % 如果参数数量不是 2 或 3 个, 那么视为输入格式错误
        \msg_new:nnn { dailyexam } { date~type~error } 
          { You~must~input~"year,month,day"~or~"month,day"~type~argument. }
        \msg_error:nn { dailyexam } { date~type~error }
      }
  }

% 用来显示当前的日期, 并用 year 参数来决定是否输出 year, 格式为 year.month.day
\cs_set:Npn \__dailyexam_current_date:n #1
  {
    \str_if_eq:nnT { #1 } { year }
      {
        \int_use:N \g__dailyexam_year_int .
      }
    \int_use:N \g__dailyexam_month_int .
    \int_use:N \g__dailyexam_day_int
  }

%%% 以下是用户层命令

% 输出当前日期的函数 \currentdate
\NewDocumentCommand { \currentdate } { o } { \__dailyexam_current_date:n { #1 } }

% 设置起始日期的函数 \setdate, 以编译时的系统年月日作为默认值
\NewDocumentCommand { \setdate } { O{ \int_use:N \c_sys_year_int, \int_use:N \c_sys_month_int, \int_use:N \c_sys_day_int } }
  {
    \__dailyexam_setdate:n { #1 }
  }

% 日期加 1 的函数 \nextdate
\NewDocumentCommand { \nextdate } { }
  {
    \__dailyexam_next_date:
  }

% 用来输出题目的节标题, 不带节序号, 带星号的版本会输出 year
% \answersection 同理
\NewDocumentCommand { \examsection } { s } 
  { 
    \clearpage 
    % 将由星号控制的日期格式存入临时 tl 变量
    \tl_set:Nn \l_tmpa_tl 
      { 
        \IfBooleanTF { #1 } 
          { \__dailyexam_current_date:n { year } } 
          { \__dailyexam_current_date:n { } } 
      }
    % 控制宏展开来输出节标题与设置页眉
    \exp_args:Nx \section_star:n { Exercise~of~\tl_use:N \l_tmpa_tl } 
    \exp_args:Nnx \fancyhdr_fancyhead:nn { L } { Exercise~of~\tl_use:N \l_tmpa_tl }
  }

\NewDocumentCommand { \answersection } { s } 
  { 
    \clearpage 
    \tl_set:Nn \l_tmpa_tl 
    { \IfBooleanTF { #1 } 
        { \__dailyexam_current_date:n { year } } 
        { \__dailyexam_current_date:n { } } 
    }
    \exp_args:Nx \section_star:n { Answer~of~\tl_use:N \l_tmpa_tl } 
    % 这里是要控制宏展开的关键, 页眉会在函数结束后再输出, 
    % 由于 \__dailyexam_next_date: 的作用, 在输出页眉的时候日期已经到达下一天
    % 所以要提前展开 \l_tmpa_tl
    \exp_args:Nnx \fancyhdr_fancyhead:nn { L } { Answer~of~\tl_use:N \l_tmpa_tl }
    % 一天的答案写完后将日期推进一天, 来为下一次的 \examsection 做准备
    \__dailyexam_next_date:
  }
\ExplSyntaxOff
\begin{document}
\tableofcontents
\setdate[2022, 2, 28]
\examsection
\answersection
\examsection*
\answersection*
\end{document}
```

![成品](https://raw.githubusercontent.com/syvshc/image/master/postimg/date-function-with-l3/finish.png)

## 小结

这是我第一次使用 LaTeX3 做一个稍微像样的东西, 感觉到了比 LaTeX2e 更方便的宏展开, 以及各种规范定义带来的时间上的便利, 最后非常感谢 [夏大鱼羊](https://xkwxdyy.github.io/) 在我学习 LaTeX3 的过程中给我提供的帮助.