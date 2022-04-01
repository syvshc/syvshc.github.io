---
title: 在终端中编译 LaTeX
tags:
  - - 帮助文档
  - - LaTeX
categories:
  - - 帮助文档
    - LaTeX
date: 2022-03-06 22:28:26
---


在交流使用 LaTeX 出现的问题的时候, 通常会听到"命令行编译"或"终端编译", 在这里简单的介绍一下. 如果有相应的命令行基础, 可以直接跳转到[使用命令行编译 LaTeX 文件](#compile-latex)

<!-- more -->

## 什么是命令行/终端

这里直接引用[Wiki](https://zh.wikipedia.org/wiki/%E7%B5%82%E7%AB%AF), 这里不详细区分"终端", "Shell" 与"命令行界面" [^shell-and-terminal]. Windows 下有"命令提示符", "powershell" (这两个严格来说不算是终端, Windows Terminal 才是); Linux 下有 "GNOME-terminal", "Konsole", macOS有……终端. 

[^shell-and-terminal]: [Windows 的 PowerShell 和 Linux 的 terminal 有啥区别？ - 知乎](https://www.zhihu.com/question/26860370)

## 如何打开命令行/终端

### Linux

Linux 通常可以通过 `ctrl`+`alt`+`T` 的快捷键组合打开终端.

### Windows

1. Windows10/11 可以点击状态栏中的"搜索"或快捷键`win`+`S`, 并在搜索栏里搜索 "cmd" 或 "powershell", 可以选择直接左键点击打开, 也可以选择右键`以管理员身份运行`, 以管理员身份运行的命令行的权限更高.[^permission]
  ![win+S](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-terminal-compiling/cmd-on-win%2Bs.gif)
2. 可以在"文件资源管理器"的地址栏中直接输入 "cmd" 或 "powershell" 并回车, 就可以在当前文件夹中打开命令行. 
  ![点击文件资源管理器](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-terminal-compiling/click.gif)
3. 可以在"文件资源管理器"的空白处按住`shift`再点击右键, 选择选项"在此处打开 PowerShell 窗口", 就可以在当前文件夹中打开 powershell. 当然 Windows11 用户或者安装了 Windows Terminal 的用户也可以通过右键后选择"在 Windows 终端中打开". 
  ![shift+右键](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-terminal-compiling/shift-right-click.gif)

### mac

我没用过

## 使用命令行/终端

#### Linux

(我认为 Linux 用户不熟悉基础的终端操作是不合格的, 这里还是贴一个 [archlinuxstudio 的链接](https://archlinuxstudio.github.io/ArchLinuxTutorial/#/advanced/beAdmin?id=%e5%bf%85%e9%a1%bb%e6%8e%8c%e6%8f%a1%e7%9a%84-linux-%e7%9f%a5%e8%af%86))

### Windows

打开命令控制符 (powershell 类似), 可以看到一些文字, 如:

```cmd
C:\Users\Admin> 
```

我们需要在 `>` 后面输入命令, 并按回车 `<enter>` 来执行命令. 

设我们所在的文件夹的结构如下 

```bash
D
└──folder
    ├── file.txt
    └── subfolder
        └── subfile.txt
```

我们在 D 盘的 `folder` 文件夹下,这个文件夹下有一个文件 `file.txt`, 一个文件夹 `subfolder`, `subfolder` 文件夹下还有一个文件为 `subfile.txt`, 下面的命令都可以点击跳转到微软的官方文档, 那里有更详细的解释和参数列表. 

* <span id="cd"> [`cd`](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/cd) </span> (**c**hange **d**irctory): 我们可以在命令行中运行 
  
  ```cmd
  D:\folder> cd subfolder
  ```

  注意我们输入的是 `cd subfolder`, 而不是前面的一串, 那些是在提示我们当前所处的文件夹, 按下回车后, 提示符变成了

  ```cmd
  D:\folder\subfolder>
  ```

  由于 Windows 有不同的盘符, 如 C 盘, D 盘, 当我们要切换到另一个盘的时候, 可以输入

  ```cmd
  D:\folder\subfolder> C:
  ```

  按下回车就可以切换到 C 盘的根目录. 如果要输入有空格的路径, 需要用引号包裹, 如

  ```cmd
  C:\> cd "Program Files"
  ```

  如果要返回上一级目录, 可以用 `..` 来代替, 比如我们处在 D 盘的 `subfile` 文件夹内, 运行

  ```cmd
  D:\folder\subfolder> cd ..
  ```

  按下回车就可以回到 `folder` 文件夹下, 只需要将 `..` 视做一个指向上一层目录的路径, 那么如下的内容就容易理解了: 在 `subfolder` 文件夹下就可以使用 `cd ../..` 回到 D 盘的根目录; 使用 `cd ../../"Program Files"` 就可以进入 D 盘根目录下的 `Program Files` 文件夹.

* [`dir`](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/dir) (**dir**ctory): 列出当前文件夹内的文件夹和文件:
  TODO

* [`mkdir`](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mkdir) (**m**a**k**e **dir**ctory): 建立新文件夹. 在 `folder` 文件夹下运行 
  
  ```cmd
  D:\folder> mkdir anothersubfolder
  ```
  就可以建立一个名为 `anothersubfolder` 的文件夹

* [`del`](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/del) (**del**ete): 删除文件. 比如我们可以在 `folder` 文件夹下运行 

  ```cmd
  D:\folder> del file.txt
  ```

  就可以删除 `file.txt` 文件, 如果想删除 `anothersubfolder` 文件夹及里面的所有文件, 可以运行

  ```cmd 
  D:\folder> del ./anothersubfolder
  ```

* [`copy`](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/del) 复制文件. 我们可以在 `subfolder` 文件夹下使用

  ```cmd
  D:\folder\subfolder> copy subfile.txt ../file.txt
  ```

  将 `subfile.txt` 文件复制到上一层目录 `..`, 也就是 `folder` 文件夹, 命名为 `file.txt`

* [`move`](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/move) 移动文件. 我们可以在 `folder` 文件夹下使用

  ```cmd
  D:\folder> mv file.txt ./subfolder/anothersubfile.txt
  ```

  将 `file.txt` 移动到 `subfolder` 文件夹, 名为 `anothersubfile.txt`, 其中 `.` 的意思是当前文件夹, 也就是 `folder` 文件夹. 

### mac

我没用过.

<h2 id="compile-latex">使用命令行编译 LaTeX 文件</h2>

LaTeX 在命令行下的程序有 `latex`, `pdflatex`, `xelatex`, `lualatex`, `latexmk` 等等, 下面我们以 `pdflatex` 为例来介绍一些编译的参数. 

### 第一次编译

这是一个 LaTeX 文件, 位于 `\home` 目录下的 `folder` 文件夹, 名为 `main.tex` [^linux]

[^linux]: 由于写这部分的时候我在 ArchLinux 下, 就以 Linux 的文件目录为例了, 原理几乎相同. 

```tex
% main.tex
\documentclass{article}
\begin{document}
  Hello \LaTeX!
\end{document}
```

我们使用命令行的 [`cd`](#cd) 命令来进入 `folder` 文件夹, 运行

```bash
$ pdflatex main.tex
```

其中 `$` 为 bash 的命令引导符, 不需要手动输入. 不出意外, 可以看到类似如下的输出

```bash
This is pdfTeX, Version 3.141592653-2.6-1.40.24 (TeX Live 2022) (preloaded format=pdflatex)
 restricted \write18 enabled.
entering extended mode
(./main.tex
LaTeX2e <2021-11-15> patch level 1
L3 programming layer <2022-02-24>
(/home/syvshclily/texlive/2022/texmf-dist/tex/latex/base/article.cls
Document Class: article 2021/10/04 v1.4n Standard LaTeX document class
(/home/syvshclily/texlive/2022/texmf-dist/tex/latex/base/size10.clo))
(/home/syvshclily/texlive/2022/texmf-dist/tex/latex/l3backend/l3backend-pdftex.
def) (./main.aux) [1{/home/syvshclily/texlive/2022/texmf-var/fonts/map/pdftex/u
pdmap/pdftex.map}] (./main.aux) )</home/syvshclily/texlive/2022/texmf-dist/font
s/type1/public/amsfonts/cm/cmr10.pfb>
Output written on main.pdf (1 page, 12962 bytes).
Transcript written on main.log.
```

同时查看 `folder` 文件夹, 可以发现多了几个文件

```
folder
  ├── main.aux
  ├── main.log
  ├── main.pdf
  └── main.tex
```

其中 

* `main.aux` 为辅助文件, 其中会存一些与交叉引用相关的内容, 以及在多次编译中需要用到的变量. 
* `main.log` 为日志文件, 其中存储这次编译用到的引擎, 宏包, 字体信息, 也包含了各种宏包输出的 message, 以及编译过程中的警告 (warning) 与错误 (error). 
* `main.pdf` 为本次编译输出的 PDF 文件, 如果没有使用 `-jobname` 等参数, 它的文件名默认与我们编译的文件 (`main.tex`) 同名. 

然后我们可以使用 PDF 阅读器打开 `main.pdf` 来查看我们编译生成的内容

![`main.pdf`](https://raw.githubusercontent.com/syvshc/image/master/latex-terminal-compiling/first-compiling.png)

### 遇到错误

我们将上面的 `main.tex` 文件改一下:

```tex
% main.tex
\documentclass{article}
\begin{document}
  Hello \Latex!
\end{document}
```

再使用命令行执行 `pdflatex main`, 可以发现并没有出现 `Output written...` 之类的语句, 而是停了下来, 显示

```text
This is pdfTeX, Version 3.141592653-2.6-1.40.24 (TeX Live 2022) (preloaded format=pdflatex)
 restricted \write18 enabled.
entering extended mode
(./main.tex
LaTeX2e <2021-11-15> patch level 1
L3 programming layer <2022-02-24>
(/home/syvshclily/texlive/2022/texmf-dist/tex/latex/base/article.cls
Document Class: article 2021/10/04 v1.4n Standard LaTeX document class
(/home/syvshclily/texlive/2022/texmf-dist/tex/latex/base/size10.clo))
(/home/syvshclily/texlive/2022/texmf-dist/tex/latex/l3backend/l3backend-pdftex.
def) (./main.aux)
! Undefined control sequence.
l.3   Hello \Latex
                  !
? 
```

出现了一个问号, 这时候我们可以按回车来让 `pdflatex` 跳过这个问题, 但是这显然是一个不好的做法, 因为我们并没有解决问题. 来仔细读一下后面一部分:

```text
! Undefined control sequence.
l.3   Hello \Latex
                  !
? 
```

提示说 "未定义的控制序列"[^undef-cs]. 如果下面显示的是我们 `.tex` 文件中的内容, 那说明我们拼写错误, 或者没有引用宏包就使用了命令等等, 总之是我们使用了一个没有定义的命令. 检查一下可以发现我们将 `\LaTeX` 错误地写成了 `\Latex`, 导致了错误. 这时我们可以输入 `X<enter>`[^enter], 结束这次编译, 修改源文件为 `\LaTeX`, 再进行编译, 便可以编译通过. 

当然在 `?` 出现的时候, 我们还可以输入 `H<enter>` 获得一些所谓的"帮助", 这些帮助……我感觉没有产生过太大的作用, 比如刚才如果我们输入 `H<enter>`, 可以获得

```text
This is pdfTeX, Version 3.141592653-2.6-1.40.24 (TeX Live 2022) (preloaded format=pdflatex)
 restricted \write18 enabled.
entering extended mode
(./main.tex
LaTeX2e <2021-11-15> patch level 1
L3 programming layer <2022-02-24>
(/home/syvshclily/texlive/2022/texmf-dist/tex/latex/base/article.cls
Document Class: article 2021/10/04 v1.4n Standard LaTeX document class
(/home/syvshclily/texlive/2022/texmf-dist/tex/latex/base/size10.clo))
(/home/syvshclily/texlive/2022/texmf-dist/tex/latex/l3backend/l3backend-pdftex.
def) (./main.aux)
! Undefined control sequence.
l.3   Hello \Latex
                  !
? H
The control sequence at the end of the top line
of your error message was never \def'ed. If you have
misspelled it (e.g., `\hobx'), type `I' and the correct
spelling (e.g., `I\hbox'). Otherwise just continue,
and I'll forget about whatever was undefined.

? 

```

这时候我们也可以输入 `I\LaTeX<enter>` 来输入正确的命令, 让程序继续编译, 注意, 我们输入 `I` 来更改的命令不会在源文件中也被改掉, 我们依然需要手动改掉 `main.tex` 中的错误. 

在遇到错误, 并使用 `X<enter>` 退出的时候, 遇到的错误会被写到 `main.log` 中, 在其中搜索 `error` 一般就可以看到错误. 

#### 处理错误的选项

当出现 `?` 时, 我们可以输入的内容有[^error-input]

[^error-input]: 这些输入不区分大小写. 

* `<enter>` 对于轻微错误, 系统将尽可能修复或跳过错误, 继续编译 (不建议使用).
* `S<enter>` 除非有找不到的文件, 否则在本次编译将不再停顿, 所有的错误都按输入了 `<enter>` 处理, 所有的错误信息都将在命令行中显示并记录到 `.log` 文件.
* `R<enter>` 比 `S` 更强, 即使找不到文件也不停顿.
* `Q<enter>` 比 `R` 更强, 它不会再显示后续的编译信息, 只是将这些内容写入 `.log` 文件, 这是一种不计后果的错误处理方式.
* `I<cs><enter>` 其中 `<cs>` 为要在断点后插入的控制序列 (control sequence), 按下回车后编译器将读入 `<cs>` 来替换出错的命令, 再读入断点后的内容继续编译. 如果输入 `I\stop<enter>`, 则停止编译, 仅显示断点之前的排版结果.
* `X<enter>` 退出, 系统将断点前的编译过程写入 `.log` 文件并将已经编译的页面输出, 然后退出编译, 断点后的文件内容不再处理. 
* `E<enter>` 类似 `X`, 它会在默认编辑器中高亮显示错误出现的位置. 
* `1-9<enter>` 可以输入一个小于 100 的正整数, 系统将在断点之后删除相同数量的半角字符, 然后等待下一步回应, 其中一个汉字等于两个半角字符, 一个 LaTeX 命令无论长短都算一个半角字符. 
* `H<enter>` 寻求帮助, 系统将给出更详细的错误说明, 并提出改正建议, 一些包的作者会额外书写输入 `H<enter>` 后的错误输出, 此时 `H` 就有了一些用处. 

在 [常见的报错命令](#common-error) 处列出了一些可能见到的错误, 如果有需要可以查阅.

### 编译选项

我们不只可以使用 `pdflatex main` 来编译, 也可以添加一些选项, 来达到额外的编译效果, 使用方法为

```bash
$ pdflatex -<option1> -<option2>=<string> main
```

其中 `<option1>, <option2>` 为选项的名字, `<string>` 对应了一些需要参数的选项, 下面介绍一些选项, 更详细的可用选项可以在命令行运行 `pdflatex --help`

* `halt-on-error` 会在第一个错误停下, 不使用 `?` 等待用户输入, 输出内容如:

  ```bash
  $ pdflatex -halt-on-error main          
  ...not important output...
  ! Undefined control sequence.
  l.3   Hello \Latex
                    !
  !  ==> Fatal error occurred, no output PDF file produced!
  Transcript written on main.log.
  ```

* `interaction=<string>` 其中 `<string>` 的值为[^interaction]
  * `errorstopmode` 这是默认值, 在遇到错误的时候停止编译并等待用户输入. 
  * `scrollmode` 在屏幕上输出编译信息, 会跳过错误, 但是会在找不到文件时停止编译并等待用户输入, 与上文的 `S` 类似. (比如找不到 `\input` 的文件的时候)
  * `batchmode` 不在命令行中输出任何有关编译的内容, 也不在任何时候停下等待用户输入. 
  * `nonstopmode` 不在任何时候停下, 直到最后显示出来错误的位置与内容. 

[^interaction]: 见 `latex2e.pdf sec 28.1` 或 [latexref](http://latexref.xyz/Command-line-options.html)

* `jobname=<string>` 设置输出文件的文件名, 如果使用

  ```bash
  $ pdflatex -jobname=test main.tex
  ```

  那么输出的文件将为

  ```
  folder
    ├── main.tex
    ├── test.aux
    ├── test.log
    └── test.pdf
  ```

* `output-directory=<string>` 将所有输出文件都输出到 `<string>` 文件夹内, 注意输出文件夹必须已经存在. 如果使用

  ```bash
  $ pdflatex -output-directory=subfolder main.tex
  ```

  那么输出文件将为

  ```bash
  folder
    ├── main.tex
    └── subfolder
        ├── main.aux
        ├── main.log
        └── main.pdf
  ```
  并且二次编译的时候会检测到输出文件夹, 并且使用输出文件夹中的辅助文件进行二次编译. 

* `-shell-escape` `-enable-write18` 让编译器可以调用外部程序进行编译, 如使用 `minted`, `tkz-fct` 等需要调用外部程序的宏包时, 需要启用, 其中 `-shell-escape` 是 TeX Live 的命令, `-enable-write18` 是 MikTeX 的命令. 事实上, 如果我们使用 `pdflatex main.tex` 来编译以下文件:

  ```tex
  % main.tex
  \documentclass{article}
  \usepackage{minted}
  \begin{document}
    Hello \LaTeX!
  \end{document}
  ```

  可以得到错误:

  ```text
  ! Package minted Error: You must invoke LaTeX with the -shell-escape flag.

  See the minted package documentation for explanation.
  Type  H <return>  for immediate help.
  ...                                              
                                                    
  l.3 \begin
            {document}
  ? H   
  Pass the -shell-escape flag to LaTeX. Refer to the minted.sty documentation for
  more information.
  ```

  根据提示我们应该使用 

  ```bash
  pdflatex -shell-escape main.tex
  ```

  来进行编译
* `-no-shell-escape` `-disable-write18` 与上面的选项相反, 强制不能使用外部程序. 

### 多次编译

#### 目录与交叉引用

通常含有交叉引用或者目录的文件需要编译两次, 我们使用以下的 `main.tex`:

```tex
% main.tex
\documentclass{article}
\begin{document}
\tableofcontents
\section{One}\label{sec:one}
  We are in section \ref{sec:one}
\end{document}
```

这里有一个目录和一个交叉引用, 让我们用 `pdflatex main` 编译一次, 然后来查看 `main.pdf`

![cross-ref](https://raw.githubusercontent.com/syvshc/image/master/latex-terminal-compiling/cross-ref.png)

我们发现既没有目录, 也没有引用, 只有 `??`, 让我们来看一下 `main.aux` 与 `main.toc

```aux
% main.aux
\relax 
\@writefile{toc}{\contentsline {section}{\numberline {1}One}{1}{}\protected@file@percent }
\newlabel{sec:one}{{1}{1}}
\gdef \@abspage@last{1}
```

```toc
% main.toc
\contentsline {section}{\numberline {1}One}{1}{}%
```

这两个文件中的内容将会为下一次的编译提供更多的命令, 比如 `\contentsline` 以及一个定义好的 `\newlabel`, 第二次编译中 `\tableofcontents` 会用到前者来输出目录, `\ref` 会用后者来输出引用的值. 

那我们再使用 `pdflatex main.tex` 来编译一次, 可以得到正确的输出:

![rigt-cross-ref](https://raw.githubusercontent.com/syvshc/image/master/latex-terminal-compiling/cross-ref-right.png)

#### 参考文献

当遇到参考文献的时候我们需要编译 4 次, 以常用的 `bibtex` 为例, 需要使用

```bash
pdflatex main.tex
bibtex main.aux
pdflatex main.tex
pdflatex main.tex
```

有了上面的铺垫, 来说一下每一次编译都做了什么

1. 第一次 `pdflatex` 将 `\cite` 的的参数 (也就是用到的 `.bib` 文件中的 `key`) 信息写入了 `main.aux` 文件.
2. `bibtex` 程序读取哪些 `key` 被用到了, 然后根据这些 `key` 生成 `main.bbl` 文件.
3. 第二次 `pdflatex` 读取 `main.bbl` 文件, 将 `\cite` 引用的正确的序号写进 `main.aux`.
4. 第三次 `pdflatex` 读取 `main.aux` 文件, 输出正确的 `\cite` 编号. 

注意: 如果使用了 `backend=biber` 参数的 `biblatex` 宏包 (这个参数是默认选项), 那么就需要在第二步使用 `biber main.bcf`. 

当然我们也可以都不写扩展名, 直接使用 `bibtex main` 或 `biber main`, 程序会自动识别出它们应该带的扩展名.

如果在没有 `\cite` 的情况下强行使用 `bibtex main`, 会得到报错:

```bash
This is BibTeX, Version 0.99d (TeX Live 2022)
The top-level auxiliary file: main.aux
I found no \citation commands---while reading file main.aux
I found no \bibdata command---while reading file main.aux
I found no \bibstyle command---while reading file main.aux
(There were 3 error messages)
```

如果强行使用 `biber main` 会得到:

```bash
INFO - This is Biber 2.17
INFO - Logfile is 'main.blg'
ERROR - Cannot find 'main.bcf'!
INFO - ERRORS: 1
```

这也是一些有自动编译功能的软件会直接甩出来的错误 (说的就是你 TeXstudio)

### 更强大的工具 `latexmk`

`latexmk` 是一个自动化的编译程序, 通常情况下它可以识别出编译后有哪些文件进行了更改, 并自动运行下一步, 比如我们使用如下的 `main.tex`, 注意清空除了 `main.tex` 以外的文件来查看更好的效果.[^xampl]

[^xampl]: 其中 `xampl.bib` 是 `mwe` 宏包自带的一个示例的 `.bib` 文件, 可以在命令行运行 `texdoc mwe` 来查找可用的键值. 

```tex
% main.tex
\documentclass{article}
\begin{document}
\tableofcontents
\section{One}\label{sec:one}
  We are in section \ref{sec:one}, 
  and we have a cite \cite{article-full}
  \bibliographystyle{plain}
  \bibliography{xampl.bib}
\end{document}
```

我们在命令行运行 (这里也可以省略扩展名 `.tex`)

```bash
$ latexmk -pdf main.tex
```

稍微注意一下可以看到编译的过程出现了

```bash
Latexmk: applying rule 'pdflatex'...
Rule 'pdflatex': File changes, etc:
   Changed files, or newly in use since previous run(s):
      'main.tex'
------------
Run number 1 of rule 'pdflatex'
------------
------------
Running 'pdflatex  -recorder  "main.tex"'
------------
...
Latexmk: applying rule 'bibtex main'...
Rule 'bibtex main': File changes, etc:
   Changed files, or newly in use since previous run(s):
      'main.aux'
------------
Run number 1 of rule 'bibtex main'
------------
------------
Running 'bibtex  "main.aux"'
------------
...
Latexmk: applying rule 'pdflatex'...
Rule 'pdflatex': File changes, etc:
   Changed files, or newly in use since previous run(s):
      'main.aux'
      'main.bbl'
      'main.toc'
------------
Run number 2 of rule 'pdflatex'
------------
------------
Running 'pdflatex  -recorder  "main.tex"'
------------
...
```

整个过程不需要我们动手, 同时 `latexmk` 也可以使用类似 `Makefile` 的 `latexmkrc` 文件来进行提前配置, 也可以像 `pdflatex` 一样为 `latexmk` 添加编译选项来增加功能. 详细的信息可以命令行运行 `texdoc latexmk` 来查看它的文档. 我也翻译了一部分文档, 但是后面鸽了, 见 {% post_link learn-latexmk "latexmk 的学习" %}.

## 其他的内容

### <a id="common-error"></a>常见的报错命令

* `Argument of \cs has an extra }.` 通常是某个带参数的命令只带了一个右括号, 如 `\section}`, `\cline 1-2}`. 输入 `H<enter>` 可以得到提示:

  ```text
  I've run across a `}' that doesn't seem to match anything.
  For example, `\def\a#1{...}' and `\a}' would produce
  this error. If you simply proceed now, the `\par' that
  I've just inserted will cause me to report a runaway
  argument that might be the root of the problem. But if
  your `}' was spurious, just type `2' and it will go away.
  ```

* `Bad math environment delimiter.` 通常是在数学模式中使用了 `\[` 或 `\(` 或在文本模式中时候了 `\]` 或 `\)`. 这都属于错误的数学定界符.[^wrong-dollar]

[^wrong-dollar]: 如果在数学模式输入了额外的 `$`, 那么报错会是 <code>! You can't use \spacefactor in math mode.</code>

* `\begin{env1} on input line .. ended by \end{env2}` 通常是环境 `env1` 没有写 `\end{env1}`, 或者 `\end{env1}` 写错了. 不过现在编辑器的配对功能很成熟, 一般不会见到了. 
* `Can be used only in preamble` 在源文件中的正文部分使用了只能在导言区 (preamble) 才能使用的命令, 如 `\usepackage{}`.
* `\caption outside float` 在浮动体环境外使用了 `\caption` 命令, 如果不想使用浮动体环境还想加标题, 可以使用 `caption` 宏包的 `\captionof{}{}` 命令, 具体的可以查看文档. 
* `Command \cs is alredy defined` 通常是用 `\newcommand` 等新建命令的命令定义了一个已经存在的命令. 
* `Command \cs invalid in math mode` 表示命令 `\cs` 不能在数学模式中使用. 
* `Double subscript` `Double superscipt` 在数学模式中不能连用两个下标或者上标, 需要用大括号来定界, 如 `$ a_b_c $` 会报 `Double subscript` 的错误, 需要使用 `$ a_{b_c} $` 或 `$ {a_b}_c $`. 
* `Environment env undefined` 环境 `env` 没有定义就使用了.
* `Extra alignment tab has been changed to \cr` 通常是在 `array` 或 `tabular` 一类需要分列的环境中, `&` 的数量比预设的列数 - 1 要多, 通常是忘了用 `\\` 换行 .
* `Extra }, or forgotten \right.` 在一个数学式中只有 `\left` 没有对应的 `\right`, 这一对命令必须成对使用, 且不能被 `&` 或 `\\` 分隔或者处于不同的组合. 
* <code>File `file' not found</code> 没找到需要的文件, 需要检查路径或文件名是否正确.
* `Runaway argument ... File ended while scanning use of \cs` 参数溢出, 通常情况下是忘了右侧的 `}`, 如 `\section{text`.
* <code>I can't write on file file.</code> 无法写入 `file` 文件, 如果无法写入 PDF 文件, 检查一下该 PDF 是否被一些阅读器锁定, 比如 PDF 文件在 Acrobat Reader 中打开了.
* `Illegal parameter number in definition of \cs` 在定义命令 `\cs` 的时候, 使用的参数数量超过了设定的参数数量.
* `Illegal unit of measure (pt inserted)` 设置了错误的长度单位, 可能是写错或忘写了单位, 注意, 设置长度为 0 的时候也需要写单位, 如 `0pt`. 
* `\include cannot be nested` `\include` 命令不能嵌套, 即不能在 `\include` 的文件中再使用 `\include`.
* `Misplaced alignment tab character &` 在表格或者 `array` 环境外使用了 `&`, 如果正文中没有使用, 可以检查参考文件中有没有出现单独的 `&`, 需要改为 `\&`.
* `Misplaced \noalign` 通常出现在 `\hline` 没有跟在 `\\` 后面, 也可能是在表格外用了 `\hline` 之类的命令.
* `Missing \begin{document}` 这是报错的重灾区, 因为通常不会有人真的不写 `\begin{document}`, 这条错误通常来自于在导言区输入了排版语言, 如一个文字, 或者一个有输出的命令, 关于这个错误的讨论详见 [Github](https://github.com/latex3/latex2e/issues/185)
* `Missing \endcsname inserted` 通常是错误地将命令名作为了环境名或计数器名, 如 `\setcounter{\section}{2}`, 或 `\begin{\equation}` 
* `Missing $ inserted` 可能是数学命令没有用在数学模式中, 或者是没有使用 `\$` 来输出 `$`. 
* `Missing number, treated as zero` 没有给出或者给出了错误的参数, 比如 `\parbox` 命令的宽度被置空或者遗漏. 再比如如果要输出当前页码, 应该用 `\thepage` 而不是 `\value{page}`, 后者只能作为其他命令的参数. 
* `No \title given` 没有写 `\title` 但是写了 `\maketitle`. 
* `Option Clash for package ...` 通常发生在一个宏包被调用了多次, 而这几次调用中使用了冲突的参数. 并且有一些宏包 `pkg` 会自动调用带参数的其他宏包 `subpkg`, 要向这些 `subpkg` 宏包传递参数的时候, 可以使用 `\PassOptionsToPackage{option}{subpkg}`.
* `Someing's wrong--perhaps a missing \item` 列表环境或者参考文件都必须以 `\item` 或者 `\bibitem` 作为前导, 否则将会产生该错误.
* `There's no line here to end` 在两个段落之间无意义地时候换行命令, 比如连用两个 `\\`, 如果要在段落中产生空白, 应该使用 `\vspace` 命令, 更进一步, 如果要添加的空白属于通常的"段落间隔", 那么应该在导言区进行格式设置, 如设置 `\parskip` 的值等等.
* `This file needs format version1 but this is version2` 所用的文档类或宏包与档版本的 LaTeX 系统不兼容, 通常所需的版本 `version1` 要高于当前版本 `version2`. 
* `Too deeply nested` 列表环境嵌套太多, 通常不应超过 4 层, 否则可以使用 `enumitem` 宏包来进一步设置. 
* `Too many }'s` 正文里有过多的 `}`, 如 `\section{text}}`.
* `\cs undefined` 使用 `\renewcommand` 定义的命令并没有定义过. 
* `Undefined control sequence` 所使用的命令没有定义, 需要检查是否没有加载宏包或命令拼写是否正确.
* `Unknown option opt for pkg` 在 `\usepackage` 中使用了并没有在 `pkg` 宏包中定义的选项 `opt`. 
* `\verb illegal in command argument` 不能在其他命令的参数中使用 `\verb`. 
* `! Package fontspec Error: The font "fontname" cannot be found.` 表示没有找到名字为 "fontname" 的字体, 请检查是否安装了该字体, 是否输入了正确的字体文件名/字族名. [^macfont]

[^permission]: 如果在更新宏包等时候出现了 `Permission denied` 等字样, 就需要考虑使用管理员权限打开 cmd
[^undef-cs]: 其中 "控制序列" 可以理解为 LaTeX 中的命令, 即以 `\` 开头的一串字符.
[^enter]: 这里的 `<enter>` 为按下回车, 而不是依次输入这几个字符. 
[^macfont]: 注意在 MacOS 系统上不经过特殊操作只能使用字体文件名来调用发行版字体, 如想设置正文为 Fandol 宋体, 在 Windows 与 Linux 下均可以使用: `\setCJKmainfont{FandolSong}` 与 `\setCJKmainfont{FandolSong-Regular.otf}`, 但是 MacOS 下只能使用后者. 如果想折腾或者想获得更多信息, 可以看[慕子的知乎专栏](https://zhuanlan.zhihu.com/p/59774395)