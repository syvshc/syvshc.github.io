---
title: PETSc 的安装与配置
date: 2022-12-14 17:18:25
tags:
    - [配置记录]
    - [帮助文档]
    - [Linux]
    - [PETSc]
    - [HPC]
categories:
    - [配置记录, PETSc]
---

**P**ortable, **E**xtensible **T**oolkit **S**cientific **C**omputations.

<!-- more -->

## PETSc 的安装

首先跟着[官网的下载教程](https://petsc.org/release/install/download/)走, 如果有连接 [GitLab](gitlab.com) 的能力, 可以直接 `clone` 最新的版本, 即

```bash
git clone -b release https://gitlab.com/petsc/petsc.git petsc
git pull
```

如果没有条件, 可以直接下载压缩包并解压[^固定版本]

[^固定版本]: 旧的版本只推荐用来运行旧程序, 建议永远使用最新的版本.

## PTESc 的配置

### 配置

首先安装好 "C Compiler (gcc)", "Fortran", "make", "Python3", 然后进行阅读, 其他配置可以参考[官网](https://petsc.org/release/install/install/) 以及命令行帮助 `./configure --help`.

通常我们需要两个版本 `debug` (输出更多的报错信息) 与 `opt` (不带 `debug` 的版本的, 性能更好)

首先编辑 `~/.bashrc` 或 `~/.zshrc`:

```bash
vim ~/.zshrc
# 编辑 ~/.zshrc
export $PETSC_DIR=where/you/downloaded/or/cloned/petsc
export $PETSC_ARCH=arch-linux-c-debug
# export $PETSC_ARCH=arch-linux-c-opt
# 退出 vim
source ~/.zshrc
```

之后只需要更改 `$PETSC_ARCH` 的值即可切换不同的版本[^petscarch].

[^petscarch]: `$PETSC_ARCH` 可以自行定义名称, 但是不要含空格.

- 如果本地已经有了 MPI 相关的程序, 如 [`OpenMPI`](https://www.open-mpi.org/) 或 [`MPICH`](https://www.mpich.org/), 以及有 [BLAS/LAPACK](https://www.netlib.org/lapack/lug/node11.html) 程序, 那么直接在 `$PETSC_DIR` 目录下运行

```bash
./configure
```

即可. 运行后可以发现在 `$PETSC_DIR` 的目录下多了一个名为 `$PETSC_ARCH` 的文件夹, 这就是以默认配置生成库的文件夹. 然后跟随终端提示

```bash
make PETSC_DIR=/some/dir PETSC_ARCH=arch-linux-c-debug all
make PETSC_DIR=/some/dir PETSC_ARCH=arch-linux-c-debug check
```

- 如果本地没有 MPI 可以选择添加选项 `--download-mpich` (官网推荐) 或 `--download-openmpi`; 如果没有 BLAS/LAPACK, 则添加选项 `--download-fblaslapack`, 即

```bash
# 啥都没有
./configure --with-cc=gcc --with-cxx=g++ --with-fc=gfortran --download-mpich --download-fblaslapack
```

然后跟着命令提示完整接下来的 check. 

**注意**: 如果本地有 MPI, 并且没有使用 `--with-mpi=0`, 则不需要添加 `--with-cc/cxx/fc` 这三个选项, MPI 会自动使用 `mpicc`, `mpicxx`, `mpif90`.

然后将 `~/.zshrc` 或 `~/.bashrc` 中的 `$PETSC_ARCH` 中的值修改为 `opt` 的版本, 并 `source`, 随后进行配置, 其余的配置与上文完全一致, 只需要在 `./configure` 的选项中添加 `--with-debugging=0` 即可, 随后依然按照命令行提示完成接下来的配置.

最后, 可以在 `~/.zshrc` 或 `~/.bashrc` 文件中添加

```bash
export C_INCLUDE_PATH=$PETSC_DIR/include:$PETSC_DIR/$PETSC_ARCH/include:$C_INCLUDE_PATH
```

来将 PETSc 中的头文件路径添加到 include path 中.

### 更新

按照[官网的说明](https://petsc.org/release/install/multibuild/#updating-or-reinstalling-petsc), 更新了源码之后需要使用

```bash
make lib
``` 

来更新库文件.