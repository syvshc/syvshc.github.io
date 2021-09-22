---
title: 使用 KDE 桌面时遇到的一些问题
date: 2021-09-22 14:29:40
tags: 
    - [Linux]
    - [帮助文档]
    - [KDE]
categories:
    - [配置记录, Linux]
---

这里记录一些使用 ArchLinux + KDE 时遇到的问题


<!-- more -->

#### 某些情况下快捷键失灵

表现与这个[帖子](https://forum.kde.org/viewtopic.php?f=17&t=166079)一致, 解决方法在贴子中也已经提到, 即将 `~/.config/kglobalshortcutsrc` 重命名或删除, 在重新登录 KDE 的时候将会自动重新生成一个 `~/.config/kglobalshortcutsrc`, 并且问题解决.

另外发现这个方法把 [flameshot 延迟启动](https://github.com/flameshot-org/flameshot/issues/1916)的问题解决了, 具体原理并不清楚.

#### 桌面黑屏

![](https://raw.githubusercontent.com/syvshc/image/master/postimg/KDEProblems/2021-09-21_19-09.png)

右键时不出现菜单, 新建文件夹也不显示, 需要重启 KDE, 在终端中运行

```
plasmashell --replace &
```

其他桌面环境的问题可以类似处理.

#### 桌面动画效果消失

首先尝试 `Alt+Shift+F12` 来打开"显示特效混合器", 如果无效, 需要重启 kwin, 在终端中运行

```
kwin_x11 --replace &
```

#### 默认状态下按 `Ctrl+Alt+F2` 可以退出桌面进入终端, 在终端中按 `Ctrl+Alt+F1` 可以返回桌面

#### 桌面语言中英混合

首先在 `设置->区域设置->语言` 中查看是否添加了 `简体中文` 

![](https://raw.githubusercontent.com/syvshc/image/master/postimg/KDEProblems/20210922152136.png)

再查看 `设置->区域设置->格式` 中是否设置为了 `zh_CN`

![](https://raw.githubusercontent.com/syvshc/image/master/postimg/KDEProblems/20210922152409.png)

如果一致, 在终端中运行 `locale` 与 `locale -a` 查看其中的输出是否含有 `zh_CN.utf8` 及 `zh_CN.UTF-8`, 如否, 请参考 [Wiki](https://wiki.archlinux.org/title/Locale_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E7%B3%BB%E7%BB%9F%E5%8C%BA%E5%9F%9F%E8%AE%BE%E7%BD%AE) 进行设置.

#### 待续