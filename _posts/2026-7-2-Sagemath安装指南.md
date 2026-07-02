---
layout: post
title: "Sagemath安装指南"
date: 2026-7-2
math: true
---

## 目录

0. [前言](#0-前言)
1. [安装wsl](#1-安装wsl)
2. [通过vscode连接wsl](#2-通过vscode连接wsl)
3. [更换ubuntu源](#3-更换ubuntu源)
4. [linux基本操作指令](#4-linux基本操作指令)
5. [安装conda包管理器](#5-安装conda包管理器)
6. [通过conda安装sage](#6-通过conda安装sage)
7. [为sage安装第三方库](#7-为sage安装第三方库)
8. [使用sage](#8-使用sage)


## 0-前言

Sagemath是一款快速成长且开源的数学软件，提供了非常丰富的代数与数论方面的功能。在CTF Crypto中，有大量的题目需要编写sage脚本去解决（除非你想用python手搓各种工具）

在如今的AICTFer时代，~~当我们兴高采烈地打开agent准备一把梭时~~，总会被一个又一个环境问题给卡住。因为sage的重要性，笔者认为，**每一个入门者都应该学习如何配置和使用它**。

sagemath在windows上提供了基于 Cygwin 的开箱即用版本，但是版本比较落后，而且调用起来不方便。在网页端上也可以使用，但不支持第三方库。因此，想要全面、便利地使用sage（~~供ai调用~~）就必须在Linux子系统里配置相关环境。


## 1-安装wsl

**WSL(Windows Subsystem for Linux)** 是适用于 Linux 的 Windows 子系统，开发人员可以安装 Linux 分发版（如 Ubuntu、OpenSUSE、Kali、Debian、Arch Linux 等），并在 Windows 上直接使用 Linux 应用程序、实用工具和 Bash 命令行工具（未经修改），无需传统虚拟机或双包设置的开销。

以 管理员身份 打开 PowerShell（右键开始菜单 → Windows PowerShell (管理员)），运行：

```bash
wsl --install
```

此命令将启用运行 WSL 所需的所有功能。默认安装Ubuntu分发。

安装完成后重启电脑。

> [!info]
> 1.C盘要留足空间（大约10g）
> 2.若安装失败，请查看BIOS中虚拟化是否开启

安装完成后，在开始菜单寻找Ubuntu并打开。

首次启动会提示创建Linux用户名和密码（ 密码输入时不会显示字符，正常输入后回车即可），然后重复输入密码


## 2-通过vscode连接wsl

**Visual Studio Code**（简称VSCode）是一款轻量级但功能强大的代码编辑器，支持多种编程语言（注意不是紫色的Visual Studio）

通过配置vscode的各种插件，我们可以通过这一款编辑器，完成几乎所有语言的代码编写。有关如何在vscode中配置并使用python，可以参考[Python安装与VSCode配置保姆级教程_vscode安装python库-CSDN博客](https://blog.csdn.net/2401_82819685/article/details/146186751)。 

通过vscode连接wsl子系统，我们可以便捷地编辑并运行sage代码。

打开vscode的插件商店，搜索wsl并安装该插件，随后重启vscode，点击左下角的“打开远程窗口”，选择“连接到WSL”，即可完成连接。

连接上后，我们可以打开文件夹/home/用户名。可以新建一个终端，随后的所有命令都可以在vscode的终端中运行。


## 3-更换ubuntu源

接下来，由于Ubuntu默认源是国外服务器，国内访问速度慢，替换为清华源，更新和下载软件会快很多。

我这里以**Ubuntu 24.04**为例（默认版本），如果是其它版本，需要替换底下的源格式，参考[清华 TUNA Ubuntu 镜像帮助](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)

先确认系统版本：

```bash
cat /etc/os-release
```

确认里面有：

```text
VERSION_ID="24.04"
VERSION_CODENAME=noble
```

备份原配置：

```bash
sudo cp /etc/apt/sources.list.d/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources.bak
```

写入清华源配置：

```bash
sudo tee /etc/apt/sources.list.d/ubuntu.sources > /dev/null <<'EOF'
Types: deb
URIs: https://mirrors.tuna.tsinghua.edu.cn/ubuntu
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb
URIs: http://security.ubuntu.com/ubuntu/
Suites: noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
EOF
```

然后更新软件包索引：

```bash
sudo apt update
```

升级已安装软件包：

```bash
sudo apt upgrade
```

## 4-linux基本操作指令

| 类别     | 指令            | 作用       | 示例                       |
| ------ | ------------- | -------- | ------------------------ |
| 查看目录   | `ls`          | 列出当前目录文件 | `ls -la`                 |
| 切换目录   | `cd`          | 进入指定目录   | `cd /home/user`          |
| 查看路径   | `pwd`         | 显示当前所在目录 | `pwd`                    |
| 创建目录   | `mkdir`       | 新建文件夹    | `mkdir test`             |
| 删除文件   | `rm`          | 删除文件或目录  | `rm file.txt`            |
| 复制文件   | `cp`          | 复制文件或目录  | `cp a.txt b.txt`         |
| 移动/重命名 | `mv`          | 移动或重命名文件 | `mv old.txt new.txt`     |
| 查看文件   | `cat`         | 输出文件内容   | `cat file.txt`           |
| 分页查看   | `less`        | 分页浏览文件   | `less log.txt`           |
| 编辑文件   | `nano`        | 终端文本编辑器  | `nano file.txt`          |
| 查找文件   | `find`        | 搜索文件     | `find . -name "*.txt"`   |
| 搜索内容   | `grep`        | 在文本中查找内容 | `grep "error" log.txt`   |
| 查看进程   | `ps`          | 显示进程     | `ps aux`                 |
| 实时进程   | `top`         | 实时查看系统进程 | `top`                    |
| 结束进程   | `kill`        | 终止进程     | `kill 1234`              |
| 磁盘空间   | `df`          | 查看磁盘使用情况 | `df -h`                  |
| 目录大小   | `du`          | 查看目录占用空间 | `du -sh *`               |
| 网络测试   | `ping`        | 测试网络连通性  | `ping google.com`        |
| 更新软件源  | `apt update`  | 更新软件包列表  | `sudo apt update`        |
| 安装软件   | `apt install` | 安装软件包    | `sudo apt install nginx` |
| 卸载软件   | `apt remove`  | 删除软件包    | `sudo apt remove nginx`  |
| 查看日志   | `journalctl`  | 查看系统日志   | `journalctl -xe`         |
| 切换管理员  | `sudo`        | 以管理员权限执行 | `sudo reboot`            |
| 查看命令帮助 | `man`         | 查看指令手册   | `man ls`                 |

## 5-安装conda包管理器

**Conda**是一款开源的软件包管理系统和环境管理系统，支持在Linux系统中管理多版本软件包及其依赖关系。

下载Miniconda安装包：

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh
```

执行安装脚本：

```bash
bash miniconda.sh
```

安装过程中，一路回车。如果问[ y/n ] 输入 y 并回车。

随后将conda添加进环境变量

1. 编辑环境变量配置文件：

```bash
nano ~/.bashrc
```

2. 添加conda路径到文件末尾:

在打开的nano编辑器中，按Ctrl+End跳到文件最后一行，粘贴以下内容:

```text
export PATH="/home/你的用户名/miniconda3/bin:$PATH"
eval "$(/home/你的用户名/miniconda3/bin/conda shell.bash hook)"
```

注意替换用户名为自己的。

3. 保存并生效配置：

• 按Ctrl+O保存，按Enter确认文件名，再按Ctrl+X退出nano；
• 执行以下命令让配置立即生效：

```bash
source ~/.bashrc
```

4. 验证是否配置成功:

```bash
conda --version
```

如果正确输出版本号，则conda安装成功。


## 6-通过conda安装sage

先接受安装的服务条款：

```bash
conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main

conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/r
```

执行安装指令：

```bash
conda create -n sage -c conda-forge sage -y
```

安装时间比较久，耐心等待。

安装完成后，先激活环境（以后每次要用的时候都要激活）：

```bash
conda activate sage
```

激活成功后，前缀会变成(sage)，随后尝试进入sage交互界面：

```bash
sage
```

出现sage:提示符后，恭喜你，已经完成了sagemath for linux的安装。


## 7-为sage安装第三方库

许多我们在python中常用的第三方库在sage中并不自带，我们必须手动安装它们。

由于sage已自带pip，我们先确保sage环境已激活，随后输入：

```bash
python -m pip install 库名
```

比如说，我们想安装pycryptodome这一密码学常用库，只需要执行：

```bash
conda activate sage
python -m pip install pycryptodome
```

随后测试：

```bash
sage -c "from Crypto.Util.number import getPrime; print(getPrime(64))"
```

发现能输出一个大素数，则安装成功。


## 8-使用sage

我们只需要在vscode左侧的资源管理器中先新建一个文件夹，用来存放我们的脚本，随后新建 .sage文件，比如test.sage。在.sage文件中编写我们的解题代码，随后在终端中执行

```bash
sage test.sage
```

即可运行脚本。
