---
mathjax: true
title: '搭建 github.io'
date: 2021-03-03 23:16:26
tags:
    - [配置记录]
    - [帮助文档]
categories:
    - [帮助文档, blog]
---

**在 NexT 主题更新 8.0 之后又重新配置了一次 `hexo+NexT`, 这次来记录一下, 并且会随时更新新增的配置.**

<!-- more -->

## 准备工作
假定用户的电脑上已经安装 `git`, 并且已经与 [github](github.com) 连接, 并熟悉 `git` 的相关操作. 

### 创建仓库
在 [github](github.com) 上新建一个仓库, 名称为 `<username>.github.io`, 将 `<username>` 替换为你的 `github` 账户名. 

### 安装 `node.js`
在 [node.js的官网](https://nodejs.org/en/) 上下载 `LTS` 版本安装, 记得勾选 `ADD TO PATH`. 安装后在命令行输入 `npm -v`, 如果有版本号输出, 那么说明安装成功, 如果返回
```bash
'npm' 不是内部或外部命令，也不是可运行的程序或批处理文件。
```
那么将 `<InstallPath>/nodejs` 添加到环境变量. 然后命令行运行
```bash
npm install -g hexo-cli
```
安装 `hexo`, 确保安装后可以运行 `hexo` 命令. 

### 初始化博客
用 `cmd` 进入想存放博客的文件夹, 运行 `hexo init <blogname>`, 将 `<blogname>` 替换成博客的项目名, 比如 `hexotest`. 因为 `hexo init` 的过程要链接 `github`, 如果出现卡顿, 可以尝试看[Github issue](https://github.com/hexojs/hexo/issues/3693)以及这个[博客](https://yuhaicheng.xyz/5.html)尝试解决. 

初始化结束后会看到这样的文件树

```
hexotest
 ├── db.json
 ├── node_modules
 ├── package-lock.json
 ├── package.json
 ├── public
 ├── scaffolds
 ├── source
 ├── themes
 ├── _config.landscape.yml
 └── _config.yml
```

## 部署博客

### 本地部署

我们进入 `hexotest` 文件夹, 输入

```bash
hexo g
hexo s
```

会有如下输出

```bash
INFO  Validating config
INFO  Start processing
INFO  Hexo is running at http://localhost:4000 . Press Ctrl+C to stop.
```

这时在浏览器中输入 `localhost:4000` 即可看到部署在本地的博客

![](https://pic4.zhimg.com/80/v2-8c21ffd0058d9cba4be5da45c49f1663.png)

然后我们命令行输入 `ctrl+C` 来结束本地部署. 

### 在线部署

当然我们写博客的目的大多都是要上传到互联网, 于是我们要把博客部署到 `<username>.github.io`.

首先安装部署插件

```bash
npm install hexo-deployer-git
```

打开 `hexotest` 下的 `_config.yml` 配置文件, 找到 `deploy`, 填入以下信息

```yml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: <git repo ssh address>
  branch: master
```

再运行

```bash
hexo d
```
等待部署成功, 成功后就可以在 `<username>.github.io` 上看到博客的内容了. 

## 创建博文

### 新建博文

在 `hexotest/` 文件夹下, 用 `cmd` 执行

```bash
hexo n "<blog_name>"
```

将 `<blog_name>` 替换为博文的名字, 注意不能有空格, 这里我们使用 `hexo n "first-blog"`, 成功后会在 `source/_posts/` 文件夹下看到 `first-blog.md` 文件

```tree
source
└── _posts
   ├── first-blog.md
   └── hello-world.md
```
同时我们还发现了另一个文件 `hello-world.md`, 这就是我们在本地部署成功后看到的那一篇博文. 

用熟悉的编辑器打开 `first-blog.md`, 将其内容修改为

```
---
title: 第一篇博文
date: 2021-03-04 00:32:07
tags:
    - [博文]
    - [测试]
    - [whatever]
categories:
    - [测试, 测试的子分类]
---
这是我的第一篇博文
```

其中 `tags` 与 `categories` 可以自行设置, 它们的关系可以参考这个[问题](https://www.zhihu.com/question/19561626)

再使用 `hexo g && hexo s` 进行本地部署, 在 `localhost:4000` 看到刚刚编辑的博文"第一篇博文", 点进去可以看到

 ![](https://pic4.zhimg.com/80/v2-797a1b83cb08f7653adcb43d56838b8c.png)

 然后可以再使用 `hexo d` 将其推送到 `<username>.github.io`, 等待一会如果没有更新内容, 那么就执行一下 `hexo clean` 来清空部署缓存. 于是我们将博文推送到远端的命令可以归为

 ```bash
 hexo clean && hexo g && hexo d
 ```

## 美化界面

### 配置基本信息

配置文件 `hexotest/_config.yml` 是对应于我们博客的最高级的配置文件, 里面有一些字段可以进行配置, 作为示例修改了下面几项

```
# Site
title: SYVHSC's BLOG
subtitle: ''
description: '无锤乙醇的博客'
keywords: LaTeX, 数学
author: 无锤乙醇
language: zh-CN
timezone: Asia/Shanghai
```

进行本地部署可以看到

![](https://pic4.zhimg.com/80/v2-8121a42938033c596ae6e0a8022d1bcd.png)

### 更换主题

这里我们选用 `NexT` 主题进行配置. 由于 `NexT8.0+` 已经支持 `npm` 安装, 于是可以在目录 `hexotest/` 下用 `cmd` 执行

```bash
npm install hexo-theme-next
```

并在 `hexotest/_config.yml` 中找到 `theme` 字段, 修改为 `theme: next`, 保存后本地部署, 可以看到博客的主题已经更换 

![](https://pic4.zhimg.com/80/v2-d115746d176f5fb3a6aa6ac7d05e73ba.png)

然后我们可以把 `node_modules/hexo-theme-next/` 中的 `_config.yml` 改为 `_config.next.yml` 并且复制到根目录 `hexotest/` 下进行修改, 这样当我们升级 `NexT` 主题的时候不需要重新配置. 

### 下面配置 `NexT` 主题的一些事项, 这里只介绍一些功能的作用, 并不具体配置. 
1. **scheme** `NexT` 主题内置了 4 种样式, 可以依次启用来选择一个适合自己的, 这里我选择了 `Gemini`;
2. **favicon** 配置标签页的图标, 可以自行选择图片, 放在 `hexotest/source/img` 文件夹下便于寻找, 设置中的路径也应改为 `/img/***.png(jpg, svg...)`;
3. **menu** 配置博客页面的链接;
4. **social** 配置博客页面的外部链接, 图标可以在 [fontawsome](https://fontawesome.com/) 进行选择;
5. **avatar** 配置博客侧边栏的头像, 图片选择同 **favicon**;
6. **toc** 配置浏览博文时侧边栏的目录;
7. **font** 配置文章中的字体;
   在 `_config.next.yml` 中不能配置多种字体, 如果想配置多种字体, 可以修改 
   ```
   hexotest\node_modules\hexo-theme-next\source\css\_variables\base.styl
   ```
   中的 `\\Font families` 中的值
8. **codeblock** 配置代码块;
9.  **back2top** 配置返回顶部的按钮;
10. **reading_progress** 配置阅读进度条;
11. **symbols_count_time** 配置站点字数和阅读时间统计, [使用方法](https://github.com/next-theme/hexo-word-counter);
12. **creative_commons** 配置文末版权信息, 关于 cc 协议可以参考[知乎专栏](https://zhuanlan.zhihu.com/p/20641764);
13. **local_search** 配置搜索栏, [使用方法](https://github.com/theme-next/hexo-generator-searchdb);
14. **links_settings & links** 配置友情链接;

### 这是一些不知道放到哪里的配置
1. **markdown-it-plus** 可以提供脚注等功能, 目录, emoji, katex 等功能, [使用方法](https://github.com/CHENXCHEN/hexo-renderer-markdown-it-plus) emoji 功能与 `hexo-renderer-pandoc` 冲突;
2. **hexo-renderer-pandoc** 提供 mathjax 渲染, 将 `tags` 设置为 `ams` 还可以使用公式编号[使用方法](https://theme-next.js.org/docs/third-party-services/math-equations);

   示例: 
   ```tex
   一元二次方程组 $ax^{2}+bx+c=0\ (a \ne 0)$ 的解为 $\eqref{eq:slo}$
   % 注意: 行内公式的 $...$ 前后不要有空格
   \begin{equation}\label{eq:slo}
        x_{1, 2}=\frac{-b\pm\sqrt{b^{2}-4ac}}{2a}
   \end{equation}
    ```
  一元二次方程组 $ax^{2}+bx+c=0\ (a \ne 0)$ 的解为 $\eqref{eq:slo}$  
  \begin{equation}\label{eq:slo}
      x_{1, 2}=\frac{-b\pm\sqrt{b^{2}-4ac}}{2a}
  \end{equation}

3. **gitalk** 配置评论区. 配置方法在 {% post_link enable-gitalk "为博客添加 Gitalk 评论区" %}.
4. **busuanzi** 配置阅读访问量. 在 `hexotest/` 文件夹下运行 
```bash
npm install busuanzi.pure.js --save
```
来安装不蒜子阅读访问量插件, 在 `_config.next.yml` 文件中搜索 `busuanzi` 即可找到
```yml
# Show Views / Visitors of the website / page with busuanzi.
# For more information: http://ibruce.info/2015/04/04/busuanzi/
busuanzi_count:
  enable: false
  total_visitors: true
  total_visitors_icon: fa fa-user
  total_views: true
  total_views_icon: fa fa-eye
  post_views: true
  post_views_icon: far fa-eye
```
把 `enable` 字段改为 `true` 即可开启. 使用
```bash
hexo clean && hexo g -d
```
部署到线上, 如果没有刷新, 使用 `ctrl+f5` 进行强制刷新即可. 


## 未完待续