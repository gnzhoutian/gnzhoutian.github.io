---
layout: post
title: Linux环境初始化
categories: Linux
tags: Linux 初始化
version: 1.0.0
comments: true
mathjax: false
mtime: 
published: true
---

* content
{:toc}

本文主要介绍个人常用的`Linux`环境**初始化步骤**及**常见问题解决方案**。

工作环境为：`Win10`+`HyperV`+`CentOS 7(Server)`。



## 一、背景

由于个人有一定程度的环境洁癖，因而，操作系统都是最小化安装，再进行初始化配置，最后安装必要软件。

初始化过程中有很多步骤，涉及面广泛，也遇到较多的问题，此处记录下来，以便后来者借鉴。

## 二、系统初始化


### 1. 防火墙设置

- **`Iptables`规则按需调整，如果为`Docker`、`WSL`环境，建议不要清除**
- **`SELinux` 和 `Firewalld`，无特殊需求，一般关闭即可**

#### a) Iptables清除

```shell
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

iptables -F
iptables -X
iptables -Z

# 保存规则
iptables-save >/etc/sysconfig/iptables

# 开机自启时应用规则
sed -i /iptables/d /etc/rc.d/rc.local
echo "iptables-restore < /etc/sysconfig/iptables" >>/etc/rc.d/rc.local
```

#### b) SELinux禁用

```shell
sed -i 's/SELINUX=.*$/SELINUX=disabled/g' /etc/sysconfig/selinux &>/dev/null  #centos7
sed -i 's/SELINUX=.*$/SELINUX=disabled/g' /etc/selinux/config &>/dev/null  #centos6
setenforce 0 &>/dev/null
```

#### c) Firewalld禁用

```shell
systemctl stop firewalld &>/dev/null
systemctl disable firewalld &>/dev/null
```

### 2. 系统设置

- **操作系统语言和环境变量中的`LANG`建议修改为`en_US.UTF-8`，以免出现编码、显示问题**

#### a) 开机自启

```shell
# 开机自启时执行的脚本，一般赋予执行权限即可
touch /etc/rc.d/rc.local
chmod 755 /etc/rc.d/rc.local
```

#### b) 语言

```shell
#修改系统语言 需要为英文
sed -i 's/^LANG=.*$/LANG="en_US.UTF-8"/g' /etc/locale.conf
```

#### c) 时区

```shell
#修改时区相差八小时问题
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

#### d) DNS

```shell
#配置DNS服务器，实际地址按需调整
echo "nameserver 223.5.5.5"  >/etc/resolv.conf
```

#### e) SSH

```shell
#ssh登陆慢
sed -i '/^UseDNS/d' /etc/ssh/sshd_config
sed -i '/^#UseDNS/a\UseDNS no' /etc/ssh/sshd_config
sed -i '/^GSSAPIAuthentication/d' /etc/ssh/sshd_config
sed -i '/^#GSSAPIAuthentication/a\GSSAPIAuthentication no' /etc/ssh/sshd_config
systemctl restart sshd
```


#### f) 环境变量

```shell
# 配置history和LANG相关属性
mkdir -p /etc/profile.d
cat >/etc/profile.d/private.sh <<'EOF'
PS1='\[\033[01;35m\][\u@\h \w]\$ \[\033[00m\]'
HISTSIZE=1000000

mkdir -p ~/.history
HISTFILE=~/.history/history_`echo $SSH_CLIENT | cut -d' ' -f1`
HISTTIMEFORMAT="[%F %T] "
export HISTTIMEFORMAT

export LANG=en_US.UTF-8
export LESSCHARSET=UTF-8
EOF
source /etc/profile.d/private.sh
```


### 3. 常用软件配置

- **默认的`Vim`、`Git`、`Yum`使用不太友好，建议都配置一下，其它配置按需修改。**\
- **扩展软件建议全部安装，基本覆盖常用功能**

#### a) Vim

```shell
cat >/root/.vimrc <<'EOF'
set nocompatible
set backspace=indent,eol,start
""set backup
syntax on
set hlsearch
filetype plugin on
set ruler
set ts=4
set sw=4
set shiftwidth=4
set softtabstop=4
set nu
set autoindent
""set textwidth=200
set noexpandtab
set encoding=utf-8
set fileencoding=utf-8
set fileencodings=ucs-bom,utf-8,chinese
set modeline
set t_vb=
EOF
```

#### b) Git

```shell
cat >/root/.gitconfig <<'EOF'
[user]
    name = haha
    email = haha@haha.com
[credential]
    helper = store
[http]
    sslVerify = false
[i18n]
    logOutputEncoding = UTF-8
    commitEncoding = UTF-8
[core]
    editor = vim
    autocrlf = false
    quotepath = false
[push]
    default = current
[safe]
    directory = *
[alias]
    lg = log --graph --format=format:'%C(cyan)[%ai]%C(reset) %C(bold blue)%h%C(reset) %C(bold green)(%ar)%C(reset) %C(bold red)%an%C(reset) %C(white)%s%C(reset) %C(bold yellow)%d%C(reset)'
    st = status 
    ls = log --graph --format=format:'%C(cyan)[%ai]%C(reset) %C(bold blue)%h%C(reset) %C(bold green)(%ar)%C(reset) %C(bold red)%an%C(reset) %C(white)%s%C(reset) %C(bold yellow)%d%C(reset)' --stat
    so = show
    cl = clean -xd
    ck = checkout
    ba = branch -a
    rb = "!cd \"${GIT_PREFIX:-.}\" && git rebase --ignore-date \"$1\" && git rebase -i \"$1\" #"
    rc = rebase --abort
[color]
    ui = auto
[color "branch"]
    current = yellow reverse bold
    local = yellow bold
    remote = green bold
[color "status"]
    added = yellow bold
    changed  = red bold
    untracked = green bold
[color "diff"]
    meta = yellow bold
    frag = magenta bold
    commit = yellow bold
    old = red bold
    new = green bold
    whitespace = red reverse
[color "diff-highlight"]
    oldNormal = red bold
    oldHighlight = red bold 52
    newNormal = green bold
    newHighlight = green bold 22
EOF
```

#### c) PyPI

```shell
mkdir -p /root/.pip
cat >/root/.pip/pip.conf <<'EOF'
[global]
index-url=http://pypi.douban.com/simple
trusted-host=pypi.douban.com
EOF
```

#### d) Yum工具

```shell
sed -i 's!cachedir=.*$!cachedir=/opt/yum/!g' /etc/yum.conf
sed -i 's/^keepcache=.*$/keepcache=1/g' /etc/yum.conf
sed -i 's/^gpgcheck=.*$/gpgcheck=0/g' /etc/yum.conf
sed -i 's/^plugins=.*$/plugins=0/g' /etc/yum.conf
sed -i 's/^enabled=.*$/enabled=0/g' /etc/yum/pluginconf.d/fastestmirror.conf
```

#### e) Yum仓库

```shell
mkdir -p /etc/yum.repos.d/
rm -f /etc/yum.repos.d/*

cat >/etc/yum.repos.d/CentOS-Base.repo <<'EOF'
[base]
name=Base
baseurl=https://mirrors.aliyun.com/centos/7/os/x86_64/
enabled=1

[epel]
name=epel
baseurl=https://mirrors.aliyun.com/epel/7/x86_64/
enabled=1

[extra]
name=extra
baseurl=https://mirrors.aliyun.com/centos/7/extras/x86_64/
enabled=1

[rh]
name=rh
baseurl=https://mirrors.aliyun.com/centos/7/sclo/x86_64/rh/
enabled=1

[sclo]
name=sclo
baseurl=https://mirrors.aliyun.com/centos/7/sclo/x86_64/sclo/
enabled=1

[docker]
name=docker
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/x86_64/stable/
enabled=0

[svn]
name=svn
baseurl=http://opensource.wandisco.com/centos/7/svn-1.11/RPMS/
enabled=0

[gitlab]
name=gitlab-ee
baseurl=https://packages.gitlab.com/gitlab/gitlab-ee/el/7/x86_64/
enabled=0
EOF
```


#### f) 扩展软件安装

```shell
# 重建缓存
yum clean all && yum makecache
# 基础包 - 建议安装
yum -y install vim git wget mlocate net-tools doxygen tree zip bzip2 file screen lrzsz ntpdate lsof psmisc postfix cifs-utils
# 编译包 - 可选安装
yum -y install autoconf libtool automake gcc gcc-c++ 
```

## 三、常见问题


### 1. 通用问题

#### a) 时间同步

```shell
ntpdate -s ntp.aliyun.com
```

### 2. Ubuntu相关

- **默认Shell建议切换为bash，否则可能遇到奇怪的脚本兼容问题**
- **自定义环境变量仍建议统一放在：`/etc/profile.d/private.sh`**

#### a) 镜像源切换

```shell
# 镜像地址: https://mirrors.aliyun.com/ubuntu/
# 帮助文档: https://developer.aliyun.com/mirror/ubuntu/  
# 复制对应版本的镜像配置至: /etc/apt/sources.list
apt update
```

#### b) 默认Shell切换

```shell
# Ubuntu默认shell从 dash 切换为 bash
CMD> dpkg-reconfigure dash
no
```

#### c) sudo密码超时

```shell
CMD> visudo 
Defaults env_reset,timestamp_timeout=-1
```

#### d) 环境变量未加载

```shell
# 用户登陆后未加载 /etc/profile问题
CMD> vim /root/.bashrc
[ -f /etc/profile.d/private.sh ] && . /etc/profile.d/private.sh
```

### 3. WSL相关

- **WSL+VSCode使用时，由于虚拟磁盘性能差，经常卡顿，已使用`HyperV`代替该方案**
- **[磁盘挂载](https://www.noyix.cn/wsl-drvfs-file-system-permissions-issue.html)和[开机自启](https://zhuanlan.zhihu.com/p/47733615)有一些启发性作用，此处留存纪念**
- **下述[版本切换](#d-版本切换)和[环境重启](#e-环境重启)两项只是备忘，基本没有用过，偶尔出现问题建议直接重启计算机**

#### a) 挂载磁盘权限问题

```shell
cat >/etc/wsl.conf <<'EOF'
[automount]
options = "metadata,umask=22,fmask=111"

[filesystem]
umask = 022
EOF
```


#### b) SSH服务配置

```shell
CMD> apt install openssh-server   # SSH服务端安装
CMD> ssh-keygen -A && ssh-keygen  # 基础密钥生成

CMD> vim /etc/ssh/sshd_config     # SSH服务端配置，调整下述四个配置
PermitRootLogin yes
PasswordAuthentication yes
GSSAPIAuthentication no
UseDNS no

CMD> service ssh restart          # 启动ssh服务
```

#### c) 服务开机自启

```shell
# WSL 环境中创建 init.wsl
cat >/etc/init.wsl <<'EOF'
#!/bin/bash
/etc/init.d/cron $1
/etc/init.d/ssh $1
#/etc/init.d/supervisor $1
EOF
chmod 775 /etc/init.wsl 

# Win环境中开机自启时调用 init.wsl
# win+R -> shell:startup -> 创建Ubuntu.vbs文件 (CRLF+ASCII) -> 文件内容如下:
Set ws = CreateObject("Wscript.Shell")
ws.run "wsl -d Ubuntu-20.04 -u root /etc/init.wsl start", vbhide
```

#### d) 版本切换

```shell
# 切换WSL2的方法
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
wsl --set-default-version 2
wsl --set-version Ubuntu-20.04 2
```

#### e) 环境重启

```shell
# WSL重启, WinCmd 中执行
net stop LxssManager; net start LxssManager

# WSL ubuntu reboot , WinCmd 中执行
Get-Service LxssManager | Restart-Service
```

## 参考链接

- [博客 - WSL 服务自动启动的正确方法](https://zhuanlan.zhihu.com/p/47733615)
- [博客 - WSL 的 DrvFs 文件系统权限问题](https://www.noyix.cn/wsl-drvfs-file-system-permissions-issue.html)


