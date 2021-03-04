---
title: Win10+Ubuntu18.04LTS双系统安装记录
date: 2020-12-12 18:00
categories:
    - [配置记录, 帮助文档]
tags:
    - [系统安装]
    - [配置记录]
    - [帮助问题]
---

本文记录过程的时候会同时记录踩过的坑, 这些坑有一些是由于我自己的操作不当, 还有一些是由于网上的教程版本过旧或书写错误造成的, 我都会记录下来, 但是文中的一些文本的描述会与系统标准输出的文本有一定差异, 只要不影响命令的运行的文字笔者不会过分追求一字不差. 那么我们开始吧[^1].

<!--more-->

## 双系统安装准备
[^1]: 由于事先没有准备, 导致很多情况下都没有截图, 这里会尽量找图来保证记录的易读性.

* 在[Ubuntu官网](https://cn.ubuntu.com/)下载`Ubuntu18.04LTS`. 虽然`Ubuntu19.10`已经问世, 但是考虑到`LTS`版本的长期支持, 还是选择了这个版本;
* 关闭`Windows10`中电源设置中的`快速启动`;
* 下载`UltraISO`作为启动盘制作工具;
* `SanDisk USB3.0 32G` U盘一个作为启动盘;
* 在`BIOS`中关闭`Secure Boot`.
* 在硬盘中[^2]压缩出至少`25G`的空间作为`Ubuntu`的系统盘, 我因为考虑到可能长期使用, 压缩了`100G`, 但是现在已经有不够用的趋势了……

[^2]: 尽量不要是`Windows`的系统盘, 并没有试过如果是系统盘会发生什么

在我尝试压缩我的机械硬盘的时候`Windows`提示我

![压缩提示](https://pic4.zhimg.com/80/v2-df63444c0adf72d52ae7240ae7bc97f1.png)


而我的机械硬盘上还有`300G`的空余空间, 我没多想, 只是认为是磁盘里碎片太多, 导致无法找出`100G`连续的空间进行压缩, 于是我运行了磁盘优化, 但是优化完还是报错, 这时我感觉问题不像我想的那样, 问了[`google`](google.com.hk)才知道我的磁盘已经在我不知道是什么的时候变成了**动态磁盘**[^3]!
[^3]:在`Windows`自带的磁盘管理中动态磁盘为黄色, 基本磁盘为深蓝色.

而这块动态磁盘还被分成了4个区, 尽管我的机械硬盘在`Win`中显示为一块磁盘. 这下知道问题所在就该着手解决了, 查了[google](google.com.hk)知道可以用`分区助手`或`DiskGenius`来实现动态磁盘逆转回基本磁盘, 在用移动硬盘备份好机械硬盘里所有的文件后开始运行软件, 应该是我的磁盘型号的问题, 这两个软件都**无法处理**, 只能将其格式化, 转换为基本磁盘后再压缩出`100G`.


## 开始操作

### 安装`Ubuntu18.04`

* 用`UltraISO`将`ubuntu1804`刻录进U盘;
* 重启电脑, 进入`BIOS`, 选择`USB`启动;
* 进入`Ubuntu`的`grub`界面后会选择`试用(try ubuntu without installing)`还是`安装(install ubuntu)`, 都可以, 如果选择`Try ubuntu`会在桌面的左上角出现一个`install`, 双击即可. 选择`安装Ubuntu`. 
* 选择语言为`汉语`

* 选择`为图像或无线硬件, 以及MP3和其它媒体安装第三方软件`

 我取消了勾选, 而由于我的`安装Ubuntu时下载更新`默认勾选且无法选择就只能勾选

![准备安装](https://pic4.zhimg.com/80/v2-bf16d4403832c000d0d7f3dabebb6332.png)

但是在后续的使用中发现缺少了很多依赖, 并不知道是不是安装时这个选项造成的, 但是还是勾选上吧, 只是安装时间会长一些. 

* 选择安装类型

![安装类型](https://pic4.zhimg.com/80/v2-a98750890a22b38fd625febad744072b.png)

我看到的几乎所有的教程中都提到了要选择`其他选项`, 而正常来说应该在`其他选项`的下一个界面中出现`100G`的空闲空间才对, 但是我只有几个`1M`的空闲空间, 为此我还返回`Windows`重新格式化了机械硬盘, 重新压缩卷, 但是都没有用. 后来问了我的同学, 他表示直接选择第一个选项`安装Ubuntu, 与其他系统共存`就可以, 开机时可以自动使用`Ubuntu`的`grub2`引导系统, 选完了之后开始安装, 重启电脑. 

**又出问题了.** 我的系统重启后可以进入`Ubuntu`的`grub2`, 选择进入`Ubuntu`, 在开机图标界面停留了很长时间, 我就强制关机了, 开机再进入`Ubuntu`的时候同样是停在了开机图标界面, 当我打算再次强制关机甚至重装系统的时候, 电脑自己关机了, **我不确定这和我第一次的强制关机是否有关系**. 在我查了一些文档后发现可能和我的`nvidia`显卡有关, 即`Ubuntu`中没有`nvidia`显卡的驱动, 而独显又主动启动, 导致了开机时的`bug`, 这里我尝试了两种方法, 均可以进入系统

* 在`grub2`界面将光标停在`Ubuntu`, 按`e`, 找到`quiet`, 在后面加上`nouveau.modeset=0`, 按`F10`保存, 进入系统. 
* 在`grub2`界面选择第二个选项`Advance`, 然后选择`Resume boot`, 进入系统.

进入系统后要开始下载`nvidia`的驱动， 打开终端， 输入

```bash
 ubuntu-drivers devices
```

可以看到类似下面的输出

```bash
== /sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0 ==
modalias : pci:v000010DEd00001C8Dsv00001028sd000007E1bc03sc02i00
vendor   : NVIDIA Corporation
model    : GP107M [GeForce GTX 1050 Mobile]
driver   : nvidia-driver-430 - distro non-free
driver   : nvidia-driver-435 - distro non-free recommended
driver   : nvidia-driver-390 - distro non-free
driver   : xserver-xorg-video-nouveau - distro free builtin
```

可以看到我的显卡型号是`GP107M [GeForce GTX 1050 Mobile]`, 推荐的驱动是`nvidia-driver-435 - distro non-free recommended`, 我使用的是默认安装

```bash
  sudo ubuntu-drivers autoinstall
```

也可以使用`apt`安装, 比如`sudo apt install nvidia-340`可以安装`340`版本的驱动. 安装驱动后就可以重启电脑, 这时不会卡开机界面了[^4]
[^4]:在我这里没有问题但是不代表没有其他问题.

### 安装Ubuntu下的基本软件

#### 安装文本编辑器`vim`

在终端输入

```bash
  sudo apt-get install vim 
```

即可安装, 可以在终端中输入

```bash
 vimtutor
```

可以查看`vim`自带的简易教程, 但是如果终端是`UTF8`编码, 中文是`LANG`环境, 那么这个命令是看不到中文版的教程的, 可以在终端运行

```bash
 iconv -f gb2312 -t utf8 /usr/share/vim/vim80/tutor/tutor.zh.euc -o ~/vimtutor.txt
```

其中的`vim80`为自己的`vim`版本, 如果不确定的话可以`cd \usr/share/vim && ls` 看一下, 会将`vimtutor.txt`的中文版输出到`~`目录下. 

#### 安装`TexLive`

关于安装教程可以在啸行的[install-LaTeX项目](https://github.com/OsbertWang/install-latex)处下载， 有很详细的教程， 这里我就说几个要注意的点.

* **先卸载`Ubuntu`预装的`TeXLive`**，本来我以为`Ubuntu`是不自带`TeXLive`的, 就直接装了`TL2019`, 但是当我想要用

```bash
    tlmgr update --self --all
```


升级宏包时, 提示我`TeXLive`的版本不匹配

![升级宏包错误](https://pic4.zhimg.com/80/v2-94ddf1ee3e06abb707dfe0e226a44dad.png)

而且当我使用`tex -v`查看版本的时候, 发现输出的是`TeXLive2017`, 然后可以按照`install-latex`中提到的方法卸载, 虽然安装后再卸载也没出什么问题, 但是安装前还是卸一下好一点.

* 中文索引引擎`zhmakeindex`

在`Windows`添加这个引擎并不难, 从[CTAN](https://ctan.org/pkg/zhmakeindex)上下载, 或从leo-liu的[zhmakeindex项目](https://github.com/leo-liu/zhmakeindex)`clone`到本地, 但是后者需要自己用`go`编译, 具体教程可以参考[图灵社区](https://www.ituring.com.cn/article/207412), 有必要一提, 我在照教程实践的时候, 在**安装zhmakeindex**一步中并没有完成, 报了一个我看不懂的错

![go报错](https://pic4.zhimg.com/80/v2-6241ad17c3d2436d93bb804d7021ed93.png)

由于我不会`GOLANG`, 也就没有去查相关原因, 知道的大佬可以在评论区补充.

在`Windows`下直接将编译好的`zhmakeindex.exe`复制到`C:\texlive\2019\bin\win32`即可, 在命令行中进入写有索引的文件夹中, 执行

```bash
    zhmakeindex filename
```

即可生成索引文件 `filename.ind`.

而在`Ubuntu`下编译失败后我也尝试用这种方法将`zhmakeindex`直接复制进`/usr/local/texlive/2019/bin/x86_64-linux`, 然后执行

```bash
    zhmakeindex filename
```

但是`Ubuntu`并不认识这个命令, 在群里交流了一下, 

在下载的目录里执行

```bash
    ls -hal
```

会返回下面的结果

![查看 zhmakeindex 权限](https://pic4.zhimg.com/80/v2-6241ad17c3d2436d93bb804d7021ed93.png)


可以发现`zhmakeindex`没有执行权限`x`. 于是执行

```bash
    chmod +x zhmakeindex
```

为`zhmakeindex`添加执行权限, 在该文件夹下的终端运行

```bash
    zhmakeindex
```

如果得到以下的输出

![zhmakeindex 输出](https://pic4.zhimg.com/80/v2-edd3a958503aff3401e4e42103cc6b4a.png)


即表示可以执行, 这时可以选择`sudo cp ./zhmakeindex /bin/`方便调用, 或者在`~/.bashrc`或`~/.zshrc`中添加别名后更新


```bash
    alias zhmakeindex="/usr/local/texlive/2019/bin/x86_64-linux/zhmakeindex"
```

其中`/usr/local/texlive/2019/bin/x86_64-linux/`为存有改过权限的`zhmakeindex`文件的目录

然后

```bash
    source ~/.zshrc
```

即可. 


#### 安装`MATLAB`

装`MATLAB`没有什么好说的, 我的学校提供了教育版的`MATLAB`, 所以不涉及到破解, 一切按流程走就行, 注意最后填写可使用的用户时的用户名填对. 

#### 安装`VSCode`及配置

可以从[官网](https://code.visualstudio.com/)上下载`*.deb`文件安装, 或者从`Ubuntu软件`中安装, 我选择的是后者.

暂时我对`VSCode`的使用就是作为`LaTeX`的编辑器, 下载的插件有

 *  `LaTeX Workshop`
 *  `Markdown All in One`
 *  `Chinese (Simplified) Language Pack for Visual Studio Code`

`settings.json`的内容在[这里](https://paste.ubuntu.com/p/PhQJSbByZd/)


另外根据我的`packages`使用习惯增加了用户代码片段`latex.json`, 在[这里](https://paste.ubuntu.com/p/WS6CBxxY2G/)

#### 安装通讯工具`wine+qq`

由于腾讯最近发布的`QQ for Linux2.0`体验极差, 还是只能选择`wine+QQ!`的方式解决, 具体教程可以看[撸Linux](https://www.lulinux.com/archives/1319)上的教程, 在我的电脑上没有问题, 可以完美的使用. 

#### 输入法`fcitx-rime`以小鹤音形配置

这个东西在我用`linux`虚拟机的时候就把我折磨地痛不欲生, 网上现存的教程太少了, 只能参考其他教程顺藤摸瓜. 

在安装小鹤音形之前, 我曾使用自带的`ibus`输入法中的双拼简单替代, 使用体验并不是太好, 出现的问题有**输入法响应不及时**, 以及在部分软件中**无法被呼出**, 比如`TeXStudio`, 放弃. 也使用过`Sougou`中的双拼, 出现的问题有**候选区乱码** , 并且尝试了解决办法后依然没有解决, 还有一点不符合我使用习惯: **无法设置在中文输入方式下输入半角符号** , 最终还是选择`rime`挂接码表, 但是还有一个问题就是最新几版的码表已经被隐藏了, 在[小鹤音形网盘](http://flypy.ys168.com/)中的码表资源较旧, 但也不是不能用, 就凑合一下吧.

1. 首先下载`fcitx-rime`

```bash
    sudo apt-get install fcitx-rime
```

2. 在[小鹤音形网盘](http://flypy.ys168.com/)中下载`小鹤音形Rime平台for Linux.zip`,  解压后会发现有一个`bin`文件夹, 还有几个`*.yaml`文件. 
3. 按照我找到的几乎所有教程都说要把所有文件复制到`~/.config/fcitx/rime`下, 但是这么做的话在部署的时候报错, 看一下`log`文件, 里面有为数不多的我能看懂的

```
    E1021 19:55:48.464576 31050 deployment_tasks.cc:242] source file 'flypyplus.dict.yaml' does not exist.
    E1021 19:55:48.492090 31050 deployment_tasks.cc:242] source file 'flypy.dict.yaml' does not exist.
```

表示`rime`找不到上面两个文件, 而`rime`要用这两个文件来生成对应的`bin`文件来调用, 但是在鹤大提供的包里直接把编译结果`bin`文件给出来了, 那就是为什么`rime`找不到这两个文件的问题了. 
4. 将`bin/`文件夹下的所有`*.bin`文件都拿出来, 放到`~/.config/fcitx/rime`下, 再进行部署就行了. 
5. 也可以在这个文件夹下的`flypy_user.txt`, `flypy_top.txt`, `flypy_sys.txt`来添加自己的编码, 这里就不赘述了. 

#### 小飞机

由于某些原因, 这里只贴一个[项目链接](https://github.com/qingshuisiyuan/electron-ssr-backup)

### 配置`zsh`

因为最近刚学了`git`， 并且有项目在[Github](https://github.com/syvshc)上, 而`Ubuntu`自带的`bash`并不显示`git`的分支等信息, 用起来并不是很顺手, 于是用`zsh`来美化一下. 安装及配置的教程可以看[这里](https://segmentfault.com/a/1190000015283092). 

## 尾声

至此我的`Ubuntu`的配置已经可以满足日常使用, 分配出来的`100G`空间还剩下`25G`, 看起来还是不太够用, 不过对于不在`Linux`下玩游戏来说使用也够了, 就这样吧. 如果有表述错误还请评论区指正