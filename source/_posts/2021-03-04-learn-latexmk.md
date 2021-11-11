---
title: latexmk 的学习
date: 2021-03-04 21:54:41
categories:
    - [帮助文档, LaTeX]
tags:
    - [帮助文档]
    - [LaTeX]
    - [latexmk]
---

其实是把翻译给咕掉了, 先把实用的东西写了, 方便查阅, 剩下的嘛, 考完研再说吧, 也没准哪天心血来潮就写了

先把这个东西写了, 随用随查, 然后也说一下遇到的坑什么的(如果有的话)

<!-- more -->

## 命令行中的 `latexmk`

`latexmk` 的通用格式为
```
latexmk [options] [file]
```

其中 `file` 可以直接写文件名, 如果直接写 `foo` 的话, `latexmk` 会按照 `foo.tex` 来处理. 有一些符号在文件名中是不能用的: `$`, `%`, `\`,\`(这个东西不能放在代码块里……), `&`这个符号不能作为文件名的开头, 还有一些控制字符也不可以, 不过我估计没人会闲的用控制字符来做 `.tex` 的文件名...

### 几个常用的 `option`

#### `-auxdir=FOO` 或者 `-aux-directory=FOO`
设置存放辅助文件的文件夹, 但是这个命令只能在 `MiKTeX` 版本的 `latex`中使用, 我这里是 `TeX Live`, 没有办法做测试. 

#### `-bibtex` 
当源文件需要用 `.bbl` 文件作为参考文献的时候, 运行 `bibtex` 或者 `biber` 来更新 `.bbl` 文件. 这个选项可以将 `latexmkrc` 中的 `$bibtex_use` 的值设置为 `2` 来实现.

#### `-bibtex-`
从不运行 `bibtex` 或者 `biber`. 同时将 `.bbl` 文件看做珍贵的 ( 原文为 precious ), 也就是在执行清理命令的时候不会删除 `.bbl` 文件. 如果我们接受到的文件中只有 `.bbl` 而没有 `.bib` 的时候会启用这个选项. 这个选项可以将`latexmkrc` 中的 `$bibtex_use` 的值设置为 `0` 来实现.

#### `bibtex-cond`
如果源文件中存在 `.bib` 文件, 则运行 `bibtex` 或 `biber` 来重写 `.bbl` 文件, 如果没有 `.bib` 文件, 则不运行 `bibtex` 或 `biber`, 这个选项也会将 `.bbl` 文件看做珍贵的. 这个选项可以将`latexmkrc` 中的 `$bibtex_use` 的值设置为 `1` 来实现.

#### `-command`
 列出 `latexmk` 在处理文件时可以使用的命令, 然后退出. 

#### `-c`
清理所有由 `latex` , `bibtex`, 以及 `biber` 生成的可再生的文件, 除了 `.dvi`, `postscript`, 和 `.pdf` 文件. 会删除的文件有 `.log`, `.aux`, `latexmk` 创建的数据文件, 还有 `@generated_exts` 中指定扩展名的文件, 同时还有 `$clean_ext` 指定的文件. 

#### `-C`
清理 `-c` 选项中的所有文件, 以及 `.pdf`, `.dvi`, `postscript` 文件, 以及 `$clean_full_ext` 指定的文件. 

#### `-cd`
在进行处理之前切换到有源程序的文件夹, 并将所有的文件生成在这个文件夹里. 举例: 如果你的文件夹结构为 `parent/subfolder/main.tex`, 你在 `parent` 文件夹下, 命令行执行 
  
```
latexmk -cd -xelatex subfolder/main
```

后, 会进入 `subfolder` 文件夹进行编译, 并将辅助文件和输出文件生成在 `subfolder` 文件夹下. 这里注意路径的分隔符要使用 `/` , 哪怕是 `Windows`.

#### `-f`
强制执行 `latexmk` 哪怕遇到了 `error`. 一般情况下, 当 `latexmk` 遇到了 `latex` 或者其他程序在**接下来的运行中**无法处理的问题的时候, 将不会给出处理结果.

**接下来的运行**指的是 如果没有 `error` 出现, 运行其他程序或者重新运行`latex` 的时候会编译完成 ( 这段其实我没看懂, 原文是 "Further processing" means the running of other Programs or the rerunning of latex (etc) that would be done if no errors had occurred. ) 额外地, 如果你想让 `latex` 程序遇到 `error` 之后不出现给用户处理 `error` 的暂停 , 你需要使用一些可以传递给程序的选项, 比如 `-interaction=nonstopmode`. 举例: 当文档里有一个严重的错误, 比如没有定义的命令, 使用 `-f` 的话

```
latexmk -f -xelatex main
```
 
在遇到错误的时候依旧会暂停来等待用户处理, 而使用 `-interaction=nonstopmode` 的话

```
latexmk -interction=nonstopmode -xelatex main
```
 `latexmk` 会一气呵成运行到最后, 哪怕没有文件输出. 

 #### `-g`
强制完整地运行一遍 `latexmk`, 哪怕在 `latexmk` 觉得源文件自从上次编译之后没有改动

#### `-h`, `-help`
打印 `latexmk` 的帮助信息.

#### `-jobname=STRING`
把输出文件中的文件名设置为 `STRING`, 而不是源文件名, 这个 `STRING` 中不能带空格. 如果要使用占位符 `%A` 的话也可以, 比如

```
latexmk -xelatex -jobname=%A-xelatex main
```

这样输出文件名就变成了 `main-xelatex.***`.

#### `-outdir=FOO` 或者 `-output-directory=FOO`
设置存放所有输出文件的文件夹.

#### `-pdf` 
当使用 `pdflatex` 的时候输出 `.pdf` 文件.

#### `-pdflatex` 
使用 `pdflatex` 编译, 并且关闭 `.dvi` 和 `.ps` 文件的生成. 

#### `-pv` 
运行预览程序, 因为编译中文文档用的 `xelatex` 不会生成 `.dvi` 和 `.ps` 文件, 所以也不存在预览哪个文件的问题, 直接就是运行 `.pdf` 文件的预览. 

#### `-r <rcfile>`
按 `<rcfile>` 中的内容执行 `latexmk`. 但是要注意调用顺序, `latexmk` 总是会先调用标准初始的 `latexmkrc` 文件, 如果不存在初始的 `latexmkrc` 文件的情况下再去调用 `<rcfile>`. 但是如果 `<rcfile>` 中指定了一个初始的 `latexmkrc` 文件, 那么这个文件会在所有的标准初始的 `latexmkrc` 文件之前运行. 

#### `-v`, `-version`
打印 `latexmk` 的版本号.

#### `-xelatex` 
使用 `xelatex` 编译程序, 同时关闭 `.dvi` 和 `.ps` 的生成. 

以上是我在**阅读文档**的过程中觉得我会用的到的选项, 如果有更多的话会再补充

## `RC` 文件中的 `latexmk`

### 如何在 `RC` 文件中设置变量
变量的格式应如下

设置一个值为**字符串**的变量:

```
$bibtex='bibtex %O %B';
```

设置一个值为**数字**的变量:

```
$preview_mode=1;
```

设置一个值为**数组**的变量:

```
@default_files=('paper', 'paper1');
```

也可以用下面的语句来给数组变量添加值:

```
push @default_files, 'paper2';
```

可以注意到简单的**标量**变量的名字的开头都是 `$`, 而数组变量的名字的开头是 `@`, 每个语句都以分号 `;` 结尾. 字符串应该用**单引号**括起来, 不建议使用双引号. 

### 命令规定的格式

一些变量会设置 `latexmk` 用于工作的命令, 比如通过 `.tex` 文件生成 `.dvi` 文件, 或者去浏览一个 `.ps` 文件. 

#### 占位符

如果你想让 `latexmk` 在通常是 `latex` 的位置使用 `elatex` 命令, 而且想让它使用 `--shell-escape` 选项, 要像下面这么写

```
latex='elatex --shell-escape %O %S';
```

那两个前面带 `%` 的就是**占位符**, 在运行命令之前他们会被替换为适当的值. 因此 `%S` 会被替换为 `elatex` 应用的源文件名, `%O` 会被替换为任何 `latexmk` 将作用在这个命令上的选项, 比如如果你在调用 `latexmk` 的时候使用了 `-silent` 选项, `%O` 会被替换为 `-interaction=batchmode`. 下面列出可用的占位符

+ `%A`: 主 `.tex` 文件的文件名. 不同于 `%R`, `%A` 不会被设置别名(jobname) 而受影响;
+ `%B`: 最近命令中的基础名 (base name). 比如一个 `document.ps` 文件从一个 `document.dvi` 转化得到, 那么这个文件名就是 `document` 
+ `%D`: 终点 (destination) 文件名. 比如一个 `.ps` 文件由一个 `.dvi` 文件转化得到, 那么终点文件名就是这个 `.ps` 文件名.
+ `%O`: 例子中提到的选项.
+ `%P`: 如果变量 `$pre_tex_code` 不空, 那么 `%P` 会被替换为 `$pre_tex_code` 中 `\input{SOURCE}` 后面的内容, 其中 `SOURCE` 是资源文件 (source file) 的名字. 这让 TeX 代码可以在资源文件被读入之前就传给 `*latex` 引擎. 如果 `$pre_tex_code` 为空, 那么 `%P` 和 `%S`等价. 
+ `%R`: 根文件名, 这是主 `.tex` 文件的基础名, 但是这个值可以被 `-jobname` 选项或者 `$jobname` 变量改变.
+ `%S`: 源文件名. 比如当转化一个 `.dvi` 文件到 `.ps` 的时候, `.dvi` 的文件名就是源文件名.
+ `%T`: The name of the primary `.tex` file. 
+ `%U`: 如果变量 `$pre_tex_code` 不空, 那么这个值就被传递给 `%U`, 如果为空, 那么它被替换为一个空字符 (null string).
+ `%Y`: 辅助文件所在的文件夹名. 如果文件夹名不是以合法符号结束, 那么就会被添加一个文件夹分隔符 `/`. 注意: 如果设置了 `$out_dir` 却没设置 `$aux_dir`,  那么`latexmk` 就把 `$aux_dir` 设置为 `$out_dir`.
+ `%Z`: 输出文件夹名. 如果 `$out_dir` 不空, 且没以合法符号结束, 那么就会被添加一个文件夹分隔符 `/`. 

(说实在的我暂时没分清这么多 name 到底是什么 name, 比如 basename, roof filename, primary tex file...)

如果有什么原因你要使用一个 `%` 字符, 还不是上面标出的情况, 那么用 `%%`. 

**注意**: 在文件名传递的过程中会被适当地添加引号, 所以不要自行添加引号, 即使文件名中有空格. ( 如果你的 TeX 文件名中有空格, 那么一些过老版本的 TeX 程序可能不会很好地处理它们. ) 如果 `latexmk` 的引号不能正确地工作, 你也可以将它关闭, 在变量 `$quote_filenames` 的设置中. 


## 20200406 更新

昨天在使用 `latexmk -xelatex -pvc main` 的时候发现 `SumatraPDF` 不能主动更新 `PDF` 文件, 而使用 `latexmk -pdflatex -pvc main` 就可以更新, 甚至将 `rc` 文件中的 `$pdflatex` 设置为 

```tex
$pdflatex="xelatex -file-line-error -halt-on-error  -interaction=nonstopmode -synctex=1 %O %S";
```

都可以更新 `pdf` 文件. 原因是 `latexmk` 选项中的 `-xelatex` 的默认选项是 `xelatex %O %S` 下面直接引用 `texdoc latexmk` 中的原文

> **Note about `xelatex`**: `latexmk` uses `xelatex` to make an `.xdv` rather than `.pdf` file, with the `.pdf` file being created in a separate step. This is enforced by the use of the `-no-pdf` option. If `%O` is part of the command for invoking `xelatex`, then latexmk will insert the `-no-pdf` option automatically, otherwise you must provide the option yourself.

而 `-no-pdf` 是 `xelatex` 特有的选项.

综上所述, 产生这个问题的原因是: **在使用 `latexmk` 的 `-xelatex` 选项时, `latexmk` 会执行 `xelatex %O %S`, 而这个 `%O` 中带了 (`latexmk` 选项) 中的 `-no-pdf` 选项, 于是在没有配置 `rc` 文件的时候, `latexmk -xelatex main` = `xelatex -no-pdf -record main`**. 

问题出现了就可以给出解决办法: 

1. 将 `rc` 中的 `$xelatex` 选项的配置为不带 `%O` 的命令, 如 `$xelatex="xelatex %S"` 即可, 但是这么做的后果是不能将临时添加在 `latexmk` 中的 `xelatex` 的选项传递给 `xelatex`, 比如像上面一样配置 `$xelatex`, 那么在使用 `latexmk -synctex=1 main` 的时候, 不会执行 `-synctex=1` 这个选项. 
2. 修改 `rc` 文件中的 `$pdflatex` 选项为 `$pdflatex="xelatex %O %S"` 这样可以接受临时添加的选项, 后果是不能使用 `pdflatex` 编译.