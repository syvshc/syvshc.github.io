---
title: 备份博客内容
date: 2021-11-18 21:57:15
tags:
    - [帮助文档]
    - [hexo]
    - [配置记录]
categories:
    - [帮助文档, blog]
---

在 [ssayno 的博客](https://ssayno.github.io) 没备份挂了之后, 来写一个我备份博客的方法.

<!-- more -->

我们已经在 {% post_link build-github-io "搭建 github.io" %} 中介绍了如何搭建一个博客. 这里介绍把博客备份在 [Github](https://github.com) 的方法, 包括备份在 `<username>.github.io` 的另一个分支, 以及备份在另外一个仓库中的方法, 两种方法大同小异.

## 存放在 `<username>.github.io` 的另一个分支中

这是我个人选择的方法.

1. 在存储博客的文件夹 `hexotest` 中, 运行

    ```bash
    git init
    ```

来进行 git 仓库的初始化,

2. 由于默认状态下 `<username>.github.io` 的主分支 (`master`/`main`) 是用来部署 `github pages` 的, 于是我们新建一个分支, 比如名为 `hexo`

    ```bash
    git checkout -b hexo
    ```

此时我们新建了 `hexo` 分支并且切换到了该分支. 

3. 跟踪所有文件, 添加 commit 信息:

    ```bash
    git add .
    git commit -m "commit message"
    ```

4. 添加远程仓库的地址:

    ```bash
    git remote add origin git@github.com:<username>/<username>.github.io.git
    ```

5. 设置默认上游分支并推送

    ```bash
    git push --set-upstream origin hexo
    ```

6. `push` 成功后可以在 `<username>.github.io` 的 `hexo` 分支中查看到博客原文.

## 备份至另一个仓库

这个方法的好处是可以把博客内容设置为私有仓库, 在 [Github](https://github.com) 上新建一个仓库, 这里设名字为 `post`, 由于不需要规避 `main` 分支[^1] 的内容, 我们就不需要新建分支, 直接

```bash
git add .
git commit -m "commit message"
```

再设置远程仓库

```bash
git remote add origin git@github.com:<username>/post.git
```

设置上游分支并推送

```bash
git push --set-upstream origin main
```

即可在 `post` 的主分支 `main` 中查看到博客原文.

[^1]: 如果未经设置, 默认分支名为 `main`. 请自行查看新建仓库的默认分支名称
