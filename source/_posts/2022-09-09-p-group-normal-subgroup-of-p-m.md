---
title: $p$ 群的正规子群
tags:
  - 数学
  - 抽象代数
  - 群论
mathjax: true
categories:
  - - 数学
    - 代数
date: 2022-09-09 13:50:16
---


在 [Wiki](https://en.wikipedia.org/wiki/P-group) 上看到了这样一个命题:

> If $p$ is prime and $G$ is a group of order $p^k$, then $G$ has a normal subgroup of order $p^m$ for every $1\leqslant m \leqslant k$.

下面来证明这个看起来很强的结论. 

<!-- more -->

首先先给一些会用到的知识:

**定义** 若有限群 $G$ 的阶等于 $p^m$, 其中 $p$ 为素数, 则称 $G$ 是一个 $p$ 群.

**定理** 任意 $p$ 群的中心 $C$ 含有不止一个元素. 

这个定理可以由有限群的类方程 (class equation):
$$|G| = |C| + \sum[G:C(y_i)]$$
来说明, 这里 $C(y_i)$ 表示 $y_i$ 在 $G$ 中的中心化子, $y_i$ 是 $G$ 中每个含不止一个元素的共轭类的代表元.

**命题** $p^2$ 阶群是 Abel 群, 其中 $p$ 是素数.

**定理**(Cauchy) 设  $G$ 是一个有限 Abel 群, $p$ 是素数, 若 $p\mid |G|$, 则 $G$ 有一个周期为 $p$ 的元素, 从而 $G$ 有一个 $p$ 阶的子群, 这个群是循环群. 

**定理**(对应定理) 设 $f: G_1 \to G_2$ 是满同态, 则映射 $H \to f(H)$ 定义了 $G_1$ 包含 $ \ker f$ 的子群集与 $G_2$ 的子群集之间的一一对应, 在这个对应下, $H\triangleleft G_1$ 当且仅当 $f(H)\triangleleft G_2$. 

**注意: 这里只有当 $\ker f \subset H$, 且 $f$ 是满同态的时候有 $H\triangleleft G_1 \Longleftrightarrow f(H)\triangleleft G_2$ 的结论.**

下面来证明这个命题. 

**证明** 用数学归纳法, 对 $|G|$ 的阶数 $p^k$ 中的 $k$ 进行归纳. 当 $k = 2$ 时, 这时 $|G|=p^2$, 由 Cauchy 定理知 $G$ 存在一个 $p$ 阶子群 $H$, 又因为 $G$ 是 Abel 群, 那么就有 $H \triangleleft G$, 这是因为对任意的 $h \in H,\ g \in G$, 都有 $hg=gh$, 那自然就有
$$Hg=gH.$$
假设 $|G|=p^k$ 时, 对于任意的 $1\leqslant m\leqslant k$, $G$ 都存在一个阶为 $p^m$ 的正规子群. 

下面考虑 $|G|=p^{k+1}$ 时. 由于 $G$ 的中心 $C$ 是一个 Abel 群, 且 $C$ 中元素不止一个, 那就有 $p\mid |C|$, 由 Cauchy 定理知 $C$ 存在一个周期为 $p$ 的元素 $a$, 也就是说循环群 $H = \langle a\rangle$ 是 $C$ 的子群, 从而是 $C$ 的正规子群. 而任意的 $a^\ell$ 与 $g \in G$ 乘法都可交换, 所以 $H \triangleleft G$, 这时我们找到了一个阶为 $p$ 的正规子群. 下面找阶数更高的. 做商群 $G/H$, 于是有
$$|G/H| = [G:H] = |G|/|H| = p^k,$$
那么由归纳假设可知对于任意的 $1\leqslant m \leqslant k$, $G/H$ 都含有一个阶为 $p^m$ 的正规子群, 设为 $N/H$, 于是
$$|N|=|N/H|\cdot|H|=p^{m+1},$$
那么只要说明 $N\triangleleft G$ 即可. 做自然同态
$$
\begin{aligned}
  f : G & \to G/H \\
  g & \mapsto gH
\end{aligned},
$$
显然这是一个满同态, 并且
$$
\ker f = \{g \in G: f(g)=eH\} = \{g \in G: gH=eH\} = H,
$$
所以有 $H \subset N$, 于是由对应定理可知当 $f(N) = N/H$ 是 $G/H$ 的正规子群时, $N$ 是 $G$ 的正规子群. 

归纳结束, 命题得证.