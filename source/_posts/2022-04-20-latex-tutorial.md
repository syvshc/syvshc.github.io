---
title: latex-tutorial
tags:
  - - LaTeX
  - - 帮助文档
categories:
  - - 帮助文档
    - LaTeX
date: 2022-04-20 22:21:00
---


应 ElegantLaTeX 的邀请, 来做一次与 LaTeX 相关的知识分享, 主要内容包括 LaTeX 软件包的安装, 更新和回滚; 如何用命令行编译 LaTeX 文件; 如何使用和配置 VSCode. 

<!-- more -->

在其他的环节开始之前

## 下载并安装 VSCode. 

### Windows 用户

1. 在 VSCode 的官网: [https://code.visualstudio.com/](https://code.visualstudio.com/) 选择 `stable` 版本进行下载. 
2. 安装的时候注意勾选"添加到PATH"

    ![安装选项](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-tutorial/install-vs.png)

### macOS 用户

同 Windows, 只需要下载 macOS 版本的安装文件安装即可.

### Linux 用户

在各发行版的软件源中下载即可.

---

在等待下载安装的时候, 我们来看一些其他的东西

## 打开终端/命令行

### Windows

1. Windows10/11 可以点击状态栏中的"搜索"或快捷键`win`+`S`, 并在搜索栏里搜索 "cmd" 或 "powershell", 可以选择直接左键点击打开, 也可以选择右键`以管理员身份运行`, 以管理员身份运行的命令行的权限更高.[^permission]
  ![win+S](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-terminal-compiling/cmd-on-win%2Bs.gif)
2. 可以在"文件资源管理器"的地址栏中直接输入 "cmd" 或 "powershell" 并回车, 就可以在当前文件夹中打开命令行. 
  ![点击文件资源管理器](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-terminal-compiling/click.gif)
3. 可以在"文件资源管理器"的空白处按住`shift`再点击右键, 选择选项"在此处打开 PowerShell 窗口", 就可以在当前文件夹中打开 powershell. 当然 Windows11 用户或者安装了 Windows Terminal 的用户也可以通过右键后选择"在 Windows 终端中打开". 
  ![shift+右键](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-terminal-compiling/shift-right-click.gif)

[^permission]: 如果在更新宏包等时候出现了 `Permission denied` 等字样, 就需要考虑使用管理员权限打开 cmd

### macOS 

同时按下 `command` 和空格, 在弹出的输入栏里输入 `terminal` 即可打开, 或见苹果的[官方说明](https://support.apple.com/zh-cn/guide/terminal/apd5265185d-f365-44cb-8b09-71a064a42125/mac)

## `tlmgr` 的使用

按照啸行编写的 [install-latex-guide-zh-cn](http://mirrors.ctan.org/info/install-latex-guide-zh-cn/install-latex-guide-zh-cn.pdf) 安装好 TeXLive 2022 后, 可以使用 `tlmgr` 来对软件包进行版本处理, 其中 `tlmgr` 是 **T**eX**L**ive **M**ana**g**e**r** 的意思. 

**注意**: 这里提到的软件包不只是用在 `\usepackage` 的宏包, 而是所有 TeXLive 包含的, 可以使用 `tlmgr` 管理的内容, 比如平常所说的宏包, 如 `amsmath.sty`, 一些说明文档, 如 `lshort-zh-cn`, 一些可执行文件, 如 `xetex.exe` 等等.

可以在命令行运行 `tlmgr version` 来查看 `tlmgr` 的版本, 正常情况下会有类似的输出:

```text
tlmgr revision 63033 (2022-04-15 07:19:42 +0200)
tlmgr using installation: C:/texlive/2022
TeX Live (https://tug.org/texlive) version 2022
```

首先说明, `tlmgr` 的命令格式为

```bash
tlmgr [global options] <action> [action-specific options] [operand]
```

其中

1. **选项(option)** 分为 **全局选项(global option)** 和 **操作特有选项(action-specific option)**. 它们以 `-` 或 `--` 开头, 但是所有的选项都可以放在 `tlmgr` 后的任何位置, 按任意顺序调用, 其中第一个不以 `-` 或 `--` 开头的参数将被视做**操作(action)**.
2. 所有的选项中 `-option` 与 `--option` 都是相同的. 
3. 几乎所有的操作都有 `-dry-run` 这个参数, 它用来假装进行操作, 来让用户看一下可能会发生什么.

那么我们就可以使用 `tlmgr` 来进行下面的练习了. 

### 更新软件包

操作: `update`

#### 查看有哪些软件包可以更新

通常来说 [CTAN](https://ctan.org) 上的软件包每天都会有更新, 可以在命令行运行

```bash
tlmgr update -list
```

来查看是否有可以更新的软件包, 这里的 `update` 是操作, `-list` 是这个操作的特有选项. 通常情况下会有类似的输出

```bash
tlmgr.pl: package repository https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet (not verified: gpg unavailable)
tlmgr.pl: would save backups to C:/texlive/2022/tlpkg/backups
tlmgr.pl: skipping forcibly removed package: collection-texworks
update:   adjmulticol        [316k]: local:    62935, source:    63073
update:   hitex             [2565k]: local:    62529, source:    63073
update:   texlive-fr        [1394k]: local:    62853, source:    63071
update:   texlive-msg-translations [144k]: local:    63010, source:    63072
update:   texlive-scripts.win32 [36k]: local:    62199, source:    63068
update:   texlive-scripts    [504k]: local:    63049, source:    63068
update:   utfsym            [4766k]: local:    56729, source:    63076
update:   xduts              [871k]: local:    63013, source:    63075
update:   zwpagelayout       [641k]: local:    53965, source:    63074
```

**第一行**会提示我们选择的软件包源, 这里我选择的是清华的源; 

**第二行**会提示我们备份的地址, 默认情况下会保留在 `/texlive/2022/tlpkg/backups`, 默认情况下会保留每个被更新的软件包的 local 版本的一个备份, 如果有更旧版本的备份, 则会用较新的 local 版本来替换这个旧的备份;

**第三行**会提示我们将会被跳过的软件包, 包括手动使用 `-exclude` 选项排除的软件包以及被认为是 `forcibly-removed (强制删除)` 的软件包. 比如这里我曾经强制删除了 `collection-texworks` 软件包, 那么它在更新的时候将会跳过这个软件包. 
然后会列出需要更新/安装/移除的软件包, 前缀分别为 `update`, `autoinst` 与 `auto-remove`. 后面跟着的数字为 **revision** 这是一个记录在 TeXLive 中的版本号, 即使软件包的作者可以随意更改他们的 cat-version (见后文), 但是 revision 总是在不断增加, 不可以随意改动. 并且可以利用 revision 来判断一个软件包是否更“新”.

#### 更新全部软件包

**注意**: 这里所谓的更新, 是包括了自动删除, 自动安装, 更新操作的“更新”, 而非只将旧版本软件包升级.

通常情况下我们会直接选择更新全部软件包, 即运行

```bash
tlmgr update -self -all
```

这里 `update` 是操作, `-self` 和 `-all` 均为 `update` 操作的特有选项, 其中 `-self` 是更新 `tlmgr` 本身以及相关软件, `-all` 是更新全部可更新的软件包.

#### 更新某些软件包

在可更新列表里如果发现了某些想更新的软件包, 比如 pkg1, pkg2, 那么就可以运行

```bash
tlmgr update pkg1 pkg2
```

来更新这两个软件包, 

#### 排除不想更新的软件包

如果我们在更新全部软件包时不想更新软件包 pkg1, 那么可以运行

```bash
tlmgr update -self -all -exclude pkg1
```

如果有多个不想更新的软件包 pkg1, pkg2, 那么可以多次使用 `-exclude` 命令

```bash
tlmgr update -self -all -exclude pkg1 -exclude pkg2
```

#### 更新过程被中断

如果软件包的更新由于网络原因中断, 或者由人为因素中断, 那么正在更新的软件包将会被视为**用户强制删除(forcibly-removed)**. 这时再更新就需要“重新安装”它们, 使用命令

```bash
tlmgr update -self -all -reinstall-forcibly-removed
```

当然, 更新过程中的 `-self` 不一定需要, 如果 `tlmgr` 没有需要更新的内容, 可以不写 `-self`.

### 查看软件包的信息

操作: `info`

#### 查看某个软件包的基本信息

如果我们想查看几个软件包 pkg1, pkg2 在 TeXLive 中的信息, 比如介绍, 版本号等等, 我们可以运行

```bash
tlmgr info pkg1 pkg2
```

来获得信息, 我们用 `tlmgr info ctex` 来说明一下:

```text
package:     ctex
category:    Package
shortdesc:   LaTeX classes and packages for Chinese typesetting
longdesc:    ctex is a collection of macro packages and document classes for LaTeX Chinese typesetting.
installed:   Yes
revision:    61285
sizes:       src: 481k, doc: 1137k, run: 1753k
relocatable: No
cat-version: 2.5.8
cat-license: lppl1.3c
cat-topics:  chinese book-pub class
cat-contact-bugs: https://github.com/CTeX-org/ctex-kit/issues
cat-contact-support: https://github.com/CTeX-org/ctex-kit/issues
cat-contact-repository: https://github.com/CTeX-org/ctex-kit
cat-contact-home: http://www.ctex.org/HomePage
collection:  collection-langchinese
```

1. `package:` 这个软件包的名字
2. `category:` 这个软件包所属的分类, 有 `Package`, `Collection`, `Scheme`. 注意这里的 `Package` 和 1. 中的 `package` 不是一样的.
3. `shortdesc:` 这个软件包的短介绍(short description), 是软件包作者在推送到 CTAN 上时需要提交的.
4. `longdesc:` 这个软件包的长介绍(long description), 是软件包作者在推送到 CTAN 上时需要提交的. 
5. `installed:` 这个软件包是否安装在了本地.
6. `revision:` 修订版号(我不知道它应该翻译成什么, 因为我想用“版本号”来翻译`cat-version`), 随着软件包作者的推送, 它会越来越大, 并且 `tlmgr` 使用这个数字来判断软件包是否需要更新.
7. `doc`, `run`, `src` 分别代表了这个软件包在 `/texlive/2022/texmf-dist` 文件夹下的 `doc`, `tex`, `source` 中的文件大小
8. `relocatable:` 这个软件包是否可以重置, 这个内容在 `user-mode` 中比较重要, 一般用户不用管
9. `cat-*:` 这个软件包在 CTAN 上分类 (category) 中的信息, 均是由软件包作者提供:
   1.  `version:` 软件包的版本号, 由软件包的作者给定, 它不需要遵循递增的规律.
   2.  `licence:` 软件包使用的开源协议.
   3.  `topics:` 这个软件包在 CTAN 上归属于哪个主题.
   4.  `contact-bugs/support/repository/home:` 分别指这个软件包的上报 bug 的地址, 提供支持的地址, 软件包所在的仓库, 软件包的主页. 
10. `collection:` 这个软件包属于哪个集合. TeXLive 上的软件包都被分为格式 (scheme), 集合 (collection) 和软件包 (package), 他们是依次包含的关系. 

#### 列出某个软件包的更详细的信息

如果想查看某几个软件包 pkg1 pkg2 中都包含了什么文件, 有哪些平台特定的依赖, 可以使用

```text
tlmgr info -list pkg1 pkg2
```

其中 `-list` 是 `info` 操作的特有选项, 我们 `tlmgr info -list tabularray` 为例

```text
# 省略 tlmgr info tabularray 的内容 #
Included files, by type:
run files:
  texmf-dist/tex/latex/tabularray/tabularray-2021.sty
  texmf-dist/tex/latex/tabularray/tabularray.sty
doc files:
  texmf-dist/doc/latex/tabularray/README.txt details="Readme"
  texmf-dist/doc/latex/tabularray/tabularray.pdf details="Package documentation"
  texmf-dist/doc/latex/tabularray/tabularray.tex
```

可以看到 `run files` 和 `doc files` 分别的位置.

### 回滚软件包的版本

操作: `restore`

#### 查看某个软件包的备份

如果想查看某个软件包 pkg 在备份中的 revision, 可以使用

```text
tlmgr restore pkg
```

如果存在备份, 那么就会返回一个 revision 的值, 我们以 `tlmgr restore functional` 为例, 会得到一个如下的输出

```text
Available backups for functional: 62926 (2022-04-17 11:15)
```

可以看到存在一个 revision 为 62926 的备份. 

#### 将某个软件包回滚为备份

如果我们获得了某个软件包 pkg 在备份中的 revision, 就可以使用

```text
tlmgr restore pkg revision
```

来将 pkg 回滚. 比如这时候我们可以使用 

```text
tlmgr restore functional 62926
```

来将 functional 软件包回滚为 revision 62926. 
这个方法在某个软件包更新后出现了不兼容的时候很好用. 

### 修改 `tlmgr` 的设置

操作: `option`

这里只列出一个常用的命令

#### 设置软件包的更新源

如果要将软件包的更新源设置为 `<mirror>`, 那么可以使用

```text
tlmgr option repository <mirror>
```

来进行更改, 其中 `repository` 可以简化为 `repo`. 默认设置下的源为 `ctan`, 它告诉 `tlmgr` 它可以从一个附近的 CTAN 镜像去获取最近的更新, 这条命令还可以写成

```text
tlmgr option repository http://mirror.ctan.org/systems/texlive/tlnet
```

其中 `ctan` 是 `http://mirror.ctan.org/systems/texlive/tlnet` 的别名. 

**注意** mirror.ctan.org 会被解析为多个不同的域名, 它们并不是完全同步的, 我们建议 (它们) 最多每天更新一次[^update]. 如果出现了问题, 我们建议您选择一个特定的镜像站. 全部的镜像站以及它们的状态会列在 [镜像站列表](https://ctan.org/mirrors/mirmon)[^mainland]. 

[^update]: mirror.ctan.org resolves to many different hosts, and they are not perfectly synchronized; we recommend updating only daily (at most), and not more often. 
[^mainland]: 这个表格来自 [install-latex-guide-zh-cn](https://github.com/OsbertWang/install-latex-guide-zh-cn). 

#### 大陆地区镜像
大陆用户可以将源指定为国内的镜像站, 在 CTAN 上可以找到 [亚洲镜像列表](https://ctan.org/mirrors#Asia) . 下面列举出截止至 2022-04-20 可用的镜像站: 

| 镜像名                                             | 镜像地址                                                   |
| -------------------------------------------------- | ---------------------------------------------------------- |
| [阿里云](https://developer.aliyun.com/mirror/)     | https://mirrors.aliyun.com/CTAN/systems/texlive/           |
| [北京交通大学](https://mirror.bjtu.edu.cn/)        | https://mirror.bjtu.edu.cn/ctan/systems/texlive/           |
| [北京理工大学](https://mirrors.bit.edu.cn/web/)    | https://mirrors.bit.edu.cn/CTAN/systems/texlive/           |
| [北京外国语大学](https://mirrors.bfsu.edu.cn/)     | https://mirrors.bfsu.edu.cn/CTAN/systems/texlive/          |
| [重庆大学](https://mirrors.cqu.edu.cn/)            | https://mirrors.cqu.edu.cn/CTAN/systems/texlive/           |
| [东莞理工学院](https://mirrors.dgut.edu.cn/)       | https://mirrors.dgut.edu.cn/CTAN/systems/texlive/          |
| [哈尔滨工业大学](https://mirrors.hit.edu.cn/)      | https://mirrors.hit.edu.cn/CTAN/systems/texlive/           |
| [华为云](https://mirrors.huaweicloud.com/)         | https://mirrors.huaweicloud.com/CTAN/systems/texlive/      |
| [华中科技大学](http://mirrors.hust.edu.cn/)        | http://mirrors.hust.edu.cn/CTAN/systems/texlive/           |
| [兰州大学](https://mirror.lzu.edu.cn/)             | https://mirror.lzu.edu.cn/CTAN/systems/texlive/            |
| [南京大学](https://mirrors.nju.edu.cn/)            | https://mirrors.nju.edu.cn/CTAN/systems/texlive/           |
| [清华大学](https://mirrors.tuna.tsinghua.edu.cn/)  | https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/ |
| [上海交通大学](https://mirrors.sjtug.sjtu.edu.cn/) | https://mirrors.sjtug.sjtu.edu.cn/ctan/systems/texlive/    |
| [上海科技大学](https://mirrors.geekpie.club/)      | https://mirrors.geekpie.club/CTAN/systems/texlive/         |
| [腾讯云](https://mirrors.cloud.tencent.com/)       | https://mirrors.cloud.tencent.com/CTAN/systems/texlive/    |
| [中国科学技术大学](https://mirrors.ustc.edu.cn/)   | https://mirrors.ustc.edu.cn/CTAN/systems/texlive/          |

并用 `tlmgr option repository <site>/tlnet` 的方式进行设置. 

### 更多内容

如果想了解更多关于 `tlmgr` 的信息, 欢迎阅读我翻译的 [tlmgr-intro-zh-cn](http://mirrors.ctan.org/info/tlmgr-intro-zh-cn/tlmgr-intro-zh-cn.pdf) 以及 [`tlmgr` 的官方文档](https://www.tug.org/texlive/doc/tlmgr.html)

## 我们的 VSCode 安装好了

我们来简单的配置一下 VSCode

1. 刚打开的 VSCode 会提示我们切换界面语言:

  ![更改语言](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-tutorial/change-lang.png)

  如果没来得及点，发现提示消失了，点击右下角的铃铛即可出现

  ![铃铛](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-tutorial/bell.png)

点击之后**什么也不用做**，即使它页面有变化，等待它自己安装完成重启即可，出现下图的效果说明语言设置成功：

![更改语言成功](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-tutorial/chinese.png)

2. 然后我们安装一下 LaTeX 相关的插件 `LaTeX-workshop`. 点击左侧工具栏的最下方的“扩展”

![扩展](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-tutorial/extension.png)

输入`latex`

![输入 `latex`](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-tutorial/input-latex.png)

出来的第一个`LaTeX-Workshop`就是我们要的，点击安装

![latex-workshop](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-tutorial/lw.png)

等待一会，当左侧的“安装”消失了或者右侧的“安装”变成了“禁用”“卸载”就说明安装成功了

![成功安装](https://raw.githubusercontent.com/syvshc/image/master/postimg/latex-tutorial/inst-success.png)

3. 我们的 VSCode 暂时就配置好了, 至于那些 recipes, 后面再说. 

## 命令行编译

这里我直接放一个之前写过的 {% post_link latex-terminal-compiling "在终端中编译 LaTeX" %}.

## 如何安装外部宏包

通常情况下我们需要的宏包都被 TeXLive 收录, 并且随 TeXLive 安装在了我们的电脑中, 如果我们想确认一个宏包 pkg 是否被安装了, 可以使用

```text
tlmgr info pkg
```

或

```text
kpsewhich pkg.sty
```

如果宏包已经被安装了, 那么前者会有输出 `Installed: Yes`, 后者会输出 `pkg.sty` 的路径. 

如果我们需要安装一个没有被 TeXLive 收录的宏包, 如 `mcode.sty`, 那么需要怎么做呢？这里我们用一个简单的文件来做示范. 

```tex
% mypackage.sty
\NeedsTeXFormat{LaTeX2e}[2017/04/15]
\ProvidesPackage{mypackage}[2022/4/20 v1.0 test]
\newcommand{\mycmd}{Hello \LaTeX}
```

将上述代码保存为 `mypackage.sty`, 这个宏包的内容非常简单, 它定义了一个命令 `\mycmd` 来输出 `Hello \LaTeX`. 现在我们把它安装到我们的电脑中, 让每一个 TeX 文档都可以通过 `\usepackage{mypackage}` 来使用这个宏包. 

1. 在 `/texlive/texmf-local/tex/latex` 文件夹下, 新建一个文件夹, 名为 `mypackage`, 然后我们把 `mypackage.sty` 放进去.
2. 在命令行运行 `texhash` 来刷新数据库, 如果得到

```bash
texhash: Updating C:/texlive/texmf-local/ls-R...
texhash: Updated C:/texlive/texmf-local/ls-R.
texhash: Updating C:/texlive/2022/texmf-config/ls-R...
texhash: Updated C:/texlive/2022/texmf-config/ls-R.
texhash: Updating C:/texlive/2022/texmf-var/ls-R...
texhash: Updated C:/texlive/2022/texmf-var/ls-R.
texhash: Updating C:/texlive/2022/texmf-dist/ls-R...
texhash: Updated C:/texlive/2022/texmf-dist/ls-R.
texhash: Done.
```

如果得到一些 Permission denied, 那就需要用管理员权限打开命令行, 再运行 `texhash`.

3. 然后可以用 `kpsewhich mypackage.sty` 来测试一下能不能找到这个宏包, 如果有输出

  ```text
  c:/texlive/texmf-local/tex/latex/mypackage/mypackage.sty
  ```

那么说明安装成功. 我们新建一个 `main.tex` 文件, 写入

```tex
% main.tex
\documentclass{article}
\usepackage{mypackage}
\begin{document}
  \mycmd
\end{document}
```

使用 `pdflatex` 编译, 即可得到输出.

## 配置 VSCode 的编译链

在网上形形色色的 VSCode + TeXLive 教程中, 总会看到它们提供一个 `settings.json` 文件, 说把它粘贴到你的电脑里, 配置就完成了, 然而我们在做这个事情的时候, 通常都不知道自己在做什么. 

现在我们已经了解了什么是编译命令, 什么是编译选项, 那么我们就可以写一个属于自己的配置单, 就算我们不会自己写, 也可以看懂别人在写什么了.

这里我贴一个 [CCNUthesis.wiki 的链接](https://gitee.com/xkwxdyy/CCNUthesis/wikis/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98FAQ/%E5%A6%82%E4%BD%95%E5%AE%89%E8%A3%85%E3%80%81%E9%85%8D%E7%BD%AE%E5%92%8C%E4%BD%BF%E7%94%A8VScode)

## 后记

这就是这次分享会我要讲的东西, 我也只是一个普通的用户, 使用的时候也需要常翻手册去找命令叫什么, 命令的参数是什么. 有能力学底层的用户可以读一下 the texbook, `texdoc source2e`, `texdoc texbytopic`, `texdoc classes`, `texdoc interface3` 等等, 有些内容了解了底层之后会更加的明了, 当然, 做一个关注于用户层的使用者也不是什么丢人的事. 希望大家可以多读手册, 多学习, 有不懂的东西可以积极向社区提问, 包括但不限于

* [tex.stackexchange.com](https://tex.stackexchange.com/)
* [CTeX-org/forum](https://github.com/CTeX-org/forum/issues)
* [LaTeX 工作室](https://ask.latexstudio.net/)