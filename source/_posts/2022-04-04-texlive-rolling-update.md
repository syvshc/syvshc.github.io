---
title: 滚动升级 TeXLive
date: 2022-04-04 16:38:13
tags:
    - [LaTeX]
    - [帮助文档]
    - [tlmgr]
categories:
    - [帮助文档, LaTeX]
---

这里说一下如何滚动升级 TeXLive, 而不卸载安装. 由于我也是第一次这么做, 不知道有没有什么可能产生的后果. 

<!-- more -->

首先, 愿意看官方文档的小伙伴可以直接看[官网](https://www.tug.org/texlive/upgrade.html)

## Windows

1. 首先我们如果直接使用 `tlmgr update -self -all` 会得到

  ```bash
  tlmgr.pl: Local TeX Live (2021) is older than remote repository (2022).
  Cross release updates are only supported with
    update-tlmgr-latest(.sh/.exe) --update
  See https://tug.org/texlive/upgrade.html for details.
  ```

  它说让去 [https://tug.org/texlive/upgrade.html](https://tug.org/texlive/upgrade.html) 查看详细信息, 但是 Windows 只给了一句

  > There is no comparable upgrade procedure for Windows. Doing a new installation is necessary.

  甚至没有给提供 `update-tlmgr-latest.exe`, 那我们去 [`tlmgr` 的官网](https://www.tug.org/texlive/tlmgr.html) 看一下有没有, 果然发现了 

  ![`tlmgr` 官网](https://raw.githubusercontent.com/syvshc/image/master/postimg/texlive-rolling-update/tlmgr-latest.png)

2. 先别急, 我们在 TeXLive 的安装目录里复制复制一份 `2021` 文件夹, 命名为 `2022`, 当然也可以直接改名, 但是如果这样做, 一旦 TeXLive 2022 有问题, 那么我们将没有办法回到之前可行的工作环境. 如果觉得文件夹很大, 可以选择不复制 `/tlpkg/backups/` 文件夹. 
3. 将环境变量中的 `texlive/2021` 改为 `texlive/2022`.

4. 然后下载 `update-tlmgr-latest.exe`, 安装, 等到 "Close" 亮起, 点击 "Close" 来关闭安装窗口, 重启命令行. 这时我们再使用 `tlmgr update -list`, 可以看到

  ```bash
  tlmgr.pl: package repository https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet (not verified: gpg unavailable)
  ===============================================================================
  tlmgr itself needs to be updated.
  Please do this via either
    tlmgr update --self
  or by getting the latest updater for Unix-ish systems:
    https://mirror.ctan.org/systems/texlive/tlnet/update-tlmgr-latest.sh
  and/or Windows systems:
    https://mirror.ctan.org/systems/texlive/tlnet/update-tlmgr-latest.exe
  Then continue with other updates as usual.
  ===============================================================================
  tlmgr.pl: would save backups to C:/texlive/2021/tlpkg/backups
  keep:     academicons              : local:    62623, source:    62622
  keep:     altsubsup                : local:    62739, source:    62738
  keep:     arara                    : local:    62339, source:    62298
  keep:     asmeconf                 : local:    62716, source:    62713
  keep:     babel                    : local:    62203, source:    62202
  keep:     babel-polish             : local:    62681, source:    62680
  ...
  ```

这样的输出

6. 这时再使用 `tlmgr update -self -all` 即可完成升级

**注意** 由于新版的 `newtx` 宏包的问题依然没有修复, 升级的时候可以选择使用 `tlmgr update -self -all -exclude newtx` 来忽略它的升级. 

  如果出现了问题, 可以读一下 Linux 的部分

  升级后使用 `tex -v` 来测试版本

  ```log
  TeX 3.141592653 (TeX Live 2022)
  kpathsea version 6.3.4
  Copyright 2022 D.E. Knuth.
  There is NO warranty.  Redistribution of this software is
  covered by the terms of both the TeX copyright and
  the Lesser GNU General Public License.
  For more information about these matters, see the file
  named COPYING and the TeX source.
  Primary author of TeX: D.E. Knuth.
  ```

7. 完成宏包更新之后, 终端运行 

    ```bash
    luaotfload-tool -fu
    ```

    来完成 `lualatex` 的字体配置 (或者叫刷新? 我几乎没用过 `lualatex`, 对它也不了解)

## Linux

1. 首先终端进入 `/texlive` 文件夹, 运行 `cp -a ./2021/ ./2022/`, 这里同样可以重新命名, 但是不推荐, 理由同上.

2. 然后在 `~/.bashrc` 或 `~/.zshrc` 中将 TeXLive 相关的环境变量中的 `2021` 改为 `2022`. 
3. 下载 `tlmgr-update-latest.sh`, 不是必须放入 `texlive` 文件夹. (这里我是将 TeXLive 安装在了 `~` 目录下, 我不知道如果安装在 `/usr/local/texlive` 是不是也可行, 欢迎反馈.)
   **注意** [https://www.tug.org/texlive/upgrade.html](https://www.tug.org/texlive/upgrade.html) 提供的 `tlmgr-update-latest.sh` 是过时的, 需要在 [`tlmgr`官网](https://www.tug.org/texlive/tlmgr.html) 或者直接去[清华的镜像](https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet/) 进行下载. (话说这个过时的问题在 2019 年就有人提过, 见 [latex.org](https://latex.org/forum/viewtopic.php?t=32463), 莫非是现在又不更新了?)
4. 重启终端, 进入 `texlive/2022/` 文件夹, 运行

    ```bash
    sh update-tlmgr-latest.sh -- --upgrade
    ```

    如果显示了 `sing --upgrade doesn't make sense; terminating.`, 说明下载的 `.sh` 文件不是最新的

    ```bash
        Verifying archive integrity... All good.
    Uncompressing TeX Live Manager Updater  100%  
    ./runme.sh: updating in /home/syvshclily/texlive/2022...
    ./runme.sh: tlmgr version says this is TeX Live 2022,
    ./runme.sh: and this updater script created: Tue Mar  1 02:52:57 CET 2022.
    ./runme.sh: have TL 2021, new version also 2021;
    ./runme.sh: using --upgrade doesn't make sense; terminating.
    (./runme.sh: makeself creation date = Tue Mar  1 02:52:57 CET 2022.)
    ```

    如果有如下的输出, 则说明运行正常:

    ```bash
    Verifying archive integrity... All good.
    Uncompressing TeX Live Manager Updater  100%  
    ./runme.sh: updating in /home/syvshclily/texlive/2022...
    ./runme.sh: tlmgr version says this is TeX Live 2021,
    ./runme.sh: and this updater script created: Tue Mar  1 02:52:57 CET 2022.
    ./runme.sh: ok, doing full release upgrade  from 2021 to 2022.
    ./runme.sh: updating /home/syvshclily/texlive/2022/bin/x86_64-linux ...
    ./runme.sh: /home/syvshclily/texlive/2022/bin/x86_64-linux/tlmgr including objects: master/tlpkg/tlpobj/texlive.infra.tlpobj master/tlpkg/tlpobj/texlive.infra.x86_64-linux.tlpobj
    D:appending to package log file: /home/syvshclily/texlive/2022/texmf-var/web2c/tlmgr.log
    D:appending to command log file: /home/syvshclily/texlive/2022/texmf-var/web2c/tlmgr-commands.log
    D:tlmgr:main: ::tldownload_server hash:: {enabled:1,errorcount:0,initcount:1,ua:LWP::UserAgent=HASH(0x561de626d708)}
    D:setup_programs: preferring system versions
    D:program curl found in path
    D:program wget found in path
    D:TLUtils::setup_programs: checking for ssl enabled wget
    D:TLUtils::setup_programs: wget has ssl, final wget args: --no-check-certificate --user-agent=texlive/wget --tries=4 --timeout=30 -q -O
    D:program lz4 found in path
    D:program gzip found in path
    D:program xz found in path
    ./runme.sh: done.
    ```

5. 因为运行完 `.sh` 程序后, `tlmgr` 会自动将源修改为 `mirrors.ctan.org` 终端运行 `tlmgr update -list`, 如果有正确输出 (见 Windows 部分), 表示可以更新, 可以在终端运行

    ```bash
    tlmgr update -self -all
    ```

    来更新全部宏包, 同样由于 `newtx` 宏包的问题, 可以使用

    ```bash
    tlmgr update -self -all -exclude newtx
    ```

    来忽略该宏包的更新.

    在更新宏包的过程中, 如果网络有波动, 有可能出现 

    ```text
    tlmgr: An error has occurred. See above messages. Exiting.
    ```

    这时候可以向上翻一下命令行输出, 我这里是如下的错误

    ```text
    [187/258, 06:36/08:31] update: texlive-common [599k] (62149 -> 62748) ... TLPDB::_install_data: downloading did not succeed (download_file failed) for https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet/archive/texlive-common.tar.xz
    tlmgr: Installation of new version of texlive-common failed, trying to unwind.
    tlmgr: Restoring old package state succeeded.
    done
    ```

    即某些宏包下载失败, 可以再运行

    ```bash
    tlmgr update -all -reinstall-forcibly-removed
    ```

    来完成更新, 同样可以加上 `-exclude newtx` 参数来忽略 `newtx` 的更新.

6. 完成宏包更新之后, 终端运行 

    ```bash
    luaotfload-tool -fu
    ```

    来完成 `lualatex` 的字体配置 (或者叫刷新? 我几乎没用过 `lualatex`, 对它也不了解)

7. 然后将安装的时候的字体文件内容进行改动, 如果是在用户文件夹中, 路径可能为 `~/.fonts.conf/09-texlive.conf`; 如果在系统文件夹, 路径可能为 `/etc/fonts/conf.d/09-texlive.conf`. 将其中的 `texlive/2021/` 修改为 `texlive/2022`. 或者直接将 `texlive/2022/texmf-var/fonts/conf/texlive-fontconfig.conf` 复制到上述文件夹, 命名为 `09-texlive.conf`, 其操作与安装 TeXLive 时是相同的. 然后在终端执行

    ```bash
    sudo fc-cache -fsv
    ```

    来刷新字体缓存. 
