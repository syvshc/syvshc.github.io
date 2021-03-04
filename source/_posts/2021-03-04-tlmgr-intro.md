---
mathjax: true
title: tlmgr 的介绍
date: 2021-03-04 22:12:21
tags:
    - [帮助文档]
    - [LaTeX]
    - [tlmgr]
categories:
    - [LaTeX, 帮助文档]
---

<!-- [点击跳转](#jump) <span id="jump">跳转到的地方</span> -->
这篇文章是用来熟悉 TeX Live 自带的包管理器 `tlmgr(TeX Live Manager)` 的使用, 官方文档位于 [TUG](http://tug.org/texlive/doc/tlmgr.html).  ~~这可能会变成一个翻译文档~~

<!-- more -->

**注意**:

1. 文档中被中括号`[]`框起来的为可选参数, 如 `--gui [action]`, 
2. 被尖括号`<>`框起来的为必要参数, 如 `tlmgr info <what>`, 
3. 被 `|` 分割开的选项为 n 选 1, 如 `backup [option] <pkg|-all>`, 

## 概要

`tlmgr` 管理着 TeX Live 中的宏包以及配置. 最新的文档以及测试版本的信息位于 [https://tug.org/texlive/tlmgr.html](https://tug.org/texlive/tlmgr.html) . 

TeX Live 由一些最高级别的 schemes 组成, 每一种 scheme 都是由不同的 collections 与 packages 组成, 其中 collection 是 packages 的集合, 而 packages 是包含了一些文件的包. Schemes 里一般既有 collections 也有 packages, 但是每一个 package 都属于且只属于一个 collection. 

可以在 [TeX Live 文档](https://tug.org/texlive/doc) 查看 TeX Live 的全部可用文档. 

## 基本格式

`tlmgr` 使用的基本格式为:
```bash
tlmgr [option...] action [option...] [operand...]
```

## 例子

成功安装 TeX Live 后, 可以设置一些 `tlmgr` 上的常用配置:

### `tlmgr option repository ctan`

告诉 `tlmgr` 它可以从一个附近的 CTAN 镜像去获取最近的更新, 这条命令还可以写成
```bash
tlmgr option repository http://mirror.ctan.org/systems/texlive/tlnet
```
其中 `ctan` 是 `http://mirror.ctan.org/systems/texlive/tlnet` 的别名. 

**注意** mirror.ctan.org 会被解析为多个不同的域名, 它们并不是完全同步的, 我们建议 (它们) 最多每天更新一次[^update]. 如果出现了问题, 我们建议您选择一个特定的镜像站. 全部的镜像站以及它们的状态会列在 [镜像站列表](https://ctan.org/mirrors/mirmon). 

#### 大陆地区镜像
大陆用户可以将源指定为国内的镜像站, 在 CTAN 上可以找到 [亚洲镜像列表](https://ctan.org/mirrors#Asia) . 下面列举出截止至 2021-03-04 可用的镜像站[^mainland]: 

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

### `tlmgr update --list`

仅列出所有可以被升级的内容. 

### `tlmgr update --all` 

将您本地的 TeX 安装内容与包管理库中的内容同步. 

### `tlmgr info <what>`

列出 `<what>` 的详细信息, 比如它的安装状态以及介绍. 例如, 使用
```bash
tlmgr info ctex
```
可以查看 `ctex` 宏集的详细信息:
```bash
package:     ctex
category:    Package
shortdesc:   LaTeX classes and packages for Chinese typesetting
longdesc:    ctex is a collection of macro packages and document classes for LaTeX Chinese typesetting.
installed:   Yes
revision:    56705
sizes:       src: 473k, doc: 1101k, run: 1749k
relocatable: No
cat-version: 2.5.5
cat-license: lppl1.3c
cat-topics:  chinese book-pub class
cat-contact-bugs: https://github.com/CTeX-org/ctex-kit/issues
cat-contact-home: http://www.ctex.org/HomePage
cat-contact-repository: https://github.com/CTeX-org/ctex-kit
collection:  collection-langchinese
```
对于 `tlmgr` 的更详细的用法, 请继续阅读. 

## 选项 (OPTIONS)

下列 `tlmgr` 的选项是**全局**选项, 不是特定于某一个命令的 (action specific). 对于所有的选项, 无论是全局的还是命令特定的, 都可以在
```bash
tlmgr [option...] action [option...] [operand...]
```
的任意位置, 按任意顺序使用. 命令中第一个不是选项的变量将会成为这个命令的主要操作 (main action). 在所有的情况中 `-option` 与 `--option` 是等价的, `=` 可以放在一个选项名和它的值之间, 按 `key=value` 的格式使用. 

### `--repository <url|path>`

指定一个宏包的仓库, 可以是本地文件夹 `<path>` 或者网络位置 `<url>`. 这个设置会临时覆盖掉 TeX Live Package Database (TLPDB, 由文件 `tlpkg/texlive.tlpdb` 给出) 中默认的宏包仓库. 

**注意** `--repository <url|path>` 命令只会修改当前运行的仓库选择, 如果想长期修改仓库, 可以使用 `option repository` (见 [`option`](#action-option)操作)

比如, 你可以这样选择一个特定的 CTAN 镜像

```bash
-repository http://ctan.example.org/its/ctan/dir/systems/texlive/tlnet
```

当然，我们必须指定一个真正的主机名及其特定的最高级的 CTAN 目录. 全部可用的 CTAN 镜像可以在 [镜像站列表](https://ctan.org/mirrors/mirmon) 处查看. 

下面是一个使用本地目录的例子:

```bash
-repository /local/TL/repository
```

剩下是一些特定的网络下的设置, 略过. 

### `--gui [action]`

`tlmgr` 有两个 GUI 前端, `tlshell` 和 `tlcockpit`, 它们是由不同的程序启动的, 详情可以查看它们的文档. 

与命令行界面一样, `tlmgr` 自己有一个图形界面. 我们可以用一个选项 `-gui` 来调用它. 如果后面还跟着一个操作 (action), 那么会直接在图形界面执行这个操作, 比如

```bash
tlmgr --gui update
```

会直接启动升级图形界面. 如果 `-gui` 后面没有跟着操作, 那么就直接打开 GUI 的主界面. 由于我不使用 GUI 界面, 这里就不对这一部分进行介绍, 详情可以参考 [GUI FOR TLMGR](https://www.tug.org/texlive/doc/tlmgr.html#GUI-FOR-TLMGR)

### `--gui-lang <llcode>`

通常情况来说, GUI 会从计算机中获取你使用的语言. 如果它没成功, 或者你想指定一个语言的 GUI 界面, 可以设置:

| 语言         | 语言代码   |
| ------------ | ---------- |
| 英语         | `en`, 默认 |
| 捷克语       | `cs`       |
| 德语         | `de`       |
| 法语         | `fr`       |
| 意大利语     | `it`       |
| 日语         | `ja`       |
| 荷兰语       | `nl`       |
| 波兰语       | `pl`       |
| 巴西葡萄牙语 | `pt_BR`    |
| 俄语         | `ru`       |
| 斯洛伐克语   | `sl`       |
| 塞尔维亚语   | `sr`       |
| 乌克兰语     | `uk`       |
| 越南语       | `vi`       |
| 简体中文     | `zh_CN`    |
| 繁体中文     | `zh_TW`    |

### `--command-logfile <file>`

将 `tlmgr` 调用的所有程序 (`mktexlr`, `mtxrun`, `fmtutil`, `updmap`) 的输出写入一个文件 `<file>` 中, 默认位置为 
```
TEXMFSYSVAR/web2c/tlmgr-commands.log
```

### `--debug-translation`

一个协助翻译的选项, 略过

### `--machine-readable`

将标准输出写成一个更适合计算机解析的格式

### `--package-logfile <file>`

`tlmgr` 将所有宏包操作 (`install`, `remove`, `update`, `failed updates`, `failed restores`) 写入一个单独的日志文件, 默认位置为
```
TEXMFSYSVAR/web2c/tlmgr.log
```
这个选项让你可以为日志文件指定另一个路径.

### `--pause`

让 `tlmgr` 在退出之前等待用户输入, 在 Windows 上可以避免命令行的消失. 

### `--usermode`

在这次运行 `tlmgr` 时启用 user mode. 见 [`USER MODE`](#user-mode)

### `--usertree <dir>`

在 `<dir>` 中创建 user mode 的树. 见 [`USER MODE`](#user-mode)

### `--verify-repo=<none|main|all>`
没看懂

TeX Live 程序的标准选项也可以使用, 比如 `--help/-h/-?`, `--version`, `-q` (无提示信息), `-v` (显示调试信息)

## 操作 (ACTIONS)

### `help`

显示一个帮助信息并退出, 与 `--help` 选项的作用相同, 实际就是打开一个与 [`tlmgr`帮助文档](https://www.tug.org/texlive/doc/tlmgr.html) 相同的帮助信息. 

### `version`

给出版本信息, 与 `--version` 选项相同, 如果也使用了 `-v` 选项, 那么所使用模块的版本信息也会被打印. 

### `backup`
### `backup [option..] <pkg|-all>`

如果没有指定 `--clean` 选项, 那么这个操作会创建一个宏包 `<pkg>` 的备份, 如果使用了 `-all` 选项, 则创建一个全部宏包的备份.

备份文件的位置将由 `--backupdir <dir>` 指定, 前提是 `<dir>` 存在并且可写. 如果没有指定 `--backupdir`, 那么就使用 TLPDB 中设置的 `backupdir`, 如果二者皆空, 那么将不会创建备份. 

安装程序将 `backupdir` 默认设置在了 TeX Live 根目录下的 `./tlpkg/backups` 中, 所以如果没有额外的设置, `backupdir` 总是存在. 查看 [`option`](#action-option) 以获得更多信息. 

如果指定了 `--clean` 选项, 那么备份文件将被删除, 而不是被保存. 一个可选的整数值 `N` 可以指定在清理时保留的备份数. 如果没有指定 `N`, 那么将使用 `autobackup` 的值, `autobackup` 默认在 TLPDB 中为 `1`. 如果二者皆空, 那么将会报错. 查看 [`option`](#action-option) 来获得更多关于删除备份文件的详细信息. 

`backup` 操作的特定选项: 

#### `--backupdir <dir>`

覆盖 TLPDB 中 `backupdir` 的值. 变量 `<dir>` 必须要指定, 这是备份文件存放的路径, 它必须要存在且可写

#### `--all`

如果没有指定 `--clean` 选项, 那么创建一个 TeX Live 安装过的所有宏包的备份, 这会消耗大量的存储空间与时间. 如果指定 `--clean` 选项, 所有的备份将被删除. 

#### `--clean[=N]`

删除备份目录 `backupdir` 中的旧备份, 而不是创建备份. 可选参数整数值 `N` 会覆盖 TLPDB 中 `autobackup` 的值. 如果使用这个选项, 那么必须要指定 `--all` 或者一列宏包. 

#### `--dry-run`
备份或删除的操作将被打印在终端而不真正进行备份或者删除, 如 `tlmgr --dry-run backup ctex` 会打印出
```bash
❯❯ tlmgr --dry-run backup ctex
would save current status of ctex to d:/texlive/2020/tlpkg/backups/ctex.r56705.tar.lz4
no action taken due to --dry-run
```

<span id="action-option"> `option` </span>
<span id="user-mode"> `USER MODE` </span>

[^update]: mirror.ctan.org resolves to many different hosts, and they are not perfectly synchronized; we recommend updating only daily (at most), and not more often. 
[^mainland]: 这个表格来自 [install-latex-guide-zh-cn](https://github.com/OsbertWang/install-latex-guide-zh-cn)