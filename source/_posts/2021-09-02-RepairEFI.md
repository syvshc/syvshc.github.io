---
title: 修复 Windows 坏掉的 EFI 引导
date: 2021-09-02 17:32:31
tags: 
    - [帮助文档]
    - [Windows]
categories:
    - [帮助文档, Windows]

---

***下次使用 `sudo` 之前一定确认好命令再执行***

因为半夜迷糊, 运行了
```bash
sudo rm -rf /*
```
来删除我某文件夹下的全部文件. 删到一半才反应过来我干了些什么, 看着崩坏的图形界面, 我只想回到 Windows, 把 Arch Linux 的分区格式化, 再重新安装, 但是我发现, 我已经把 Windows 的 EFI 引导破坏了……

<!-- more -->
感谢 `#archlinux-cn-offtopic` 群的 Xiao Jerry!

1. 准备好一个 Windows 10 的启动 U 盘, 在 BIOS 界面从 U 盘启动
![BOOT](https://raw.githubusercontent.com/syvshc/image/master/postimg/RepairEFI/boot.jpg)
2. 点击右下角: 修复计算机
3. 进入命令行, 运行
```
bootrec /rebuildbcd
```
找到后用 `A` 添加
![扫描 Windows 安装](https://raw.githubusercontent.com/syvshc/image/master/postimg/RepairEFI/bootrec.jpg)
4. 添加后退出命令行, 点击启动修复
![启动修复](https://raw.githubusercontent.com/syvshc/image/master/postimg/RepairEFI/startup-repair.jpg)
5. 完成