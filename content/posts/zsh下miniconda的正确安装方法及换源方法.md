---
title: "zsh下miniconda的正确安装方法及换源方法"
categories: [ "记录" ]
tags: [ "miniconda","zsh" ]
draft: false
slug: "miniconda"
date: "2023-08-25 01:04:56"
---

Anaconda 是一个用于科学计算的 Python 发行版，支持 Linux, Mac, Windows, 包含了众多流行的科学计算、数据分析的 Python 包。Miniconda 是一个 Anaconda 的轻量级替代，默认只包含了 python 和 conda，但是可以通过 pip 和 conda 来安装所需要的包。通常情况下安装Miniconda构建虚拟环境是很方便的，但是安装过程中会碰到一些奇怪的问题导致使用时一直报错，这里记录一下一个成功的安装方法。


----------
####下载并执行安装脚本
```wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-x86_64.sh
chmod +x Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```
然后一路yes,到最后一步是否`conda init`一定要选no，不然大概率出问题。
####修改.zshrc
文件位于用户名根目录下，打开后在最后一行添加
```
eval "$(/root/miniconda3/bin/conda shell.zsh hook)"
```
因为我安装位置在`/root`下，如果不是这个路径自行修改。
然后使文件生效：
```
source ~/.zshrc
```
####修改为国内源
首先执行：
```
conda config --write-default
```
然后用户目录下会有一个`.condarc`的文件，打开，在最后输入
```
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  deepmodeling: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/

# channel_priority: flexible
channel_priority: strict

# pip_interop_enabled: false
pip_interop_enabled: true

# safety_checks: warn
safety_checks: enabled

# auto_activate_base: true
auto_activate_base: false

# report_errors:
report_errors: false
```
保存，然后执行
```
conda update --all
```
可以看到现在更新很快了。