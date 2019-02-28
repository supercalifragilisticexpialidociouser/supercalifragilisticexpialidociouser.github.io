---
title: Manjaro-i3
date: 2019-02-28 13:53:25
tags:
---

# 配置源

```bash
$ sudo pacman-mirrors -c China
```

# 添加archlinuxcn源

编辑`/etc/pacman.conf`，添加

```
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

# 更新系统

```bash
$ sudo pacman -Syyu
```

> 如果有大量软件包要更新，建议使用命令行。GUI更新工具比较慢，而且容易崩溃。

# 导入GPG Key

```bash
$ sudo pacman -S archlinuxcn-keyring
```

# 设置时钟

方法一：通过GUI配置界面

方法二：命令行

```bash
$ sudo hwclock --systohc
同步时间：
$ ntpdate -u ntp.api.bz
```

# 更换浏览器

首先，卸载原装的Pale Moon浏览器。

然后，安装Chromium浏览器：

```bash
$ sudo pacman -S google-chrome
```

将`Mod-F2`快捷键配置成打开Chromium，这通过修改`~/.i3/config`：

```
bindsym $mod+F2 exec --no-startup-id chromium
或
bindsym $mod+F2 exec --no-startup-id google-chrome-stable
```

代理：

```bash
$ google-chrome-stable --proxy-server="socks5://localhost:1080"
```

登录`chrome.google.com`下载`SwitchyOmega`，导入配置。

# 安装搜狗输入法

```bash
$ sudo pacman -S fcitx-im #默认全部安装
$ sudo pacman -S fcitx-configtool
$ sudo pacman -S fcitx-sogoupinyin
```

安装完成后，编辑`~/.xprofile`，添加如下配置：

```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

然后，可以通过GUI工具`fcitx-configtool`调节输入法设置。

# 配置显卡驱动

## 测试显卡性能

```bash
$ glxgears
```

## 安装显卡驱动

```bash
$ sudo mhwd -a pci nonfree 0300
```

## 多显示器配置

使用xrandr配置

# 安装字体

```bash
$ pacman -S adobe-source-code-pro-fonts wqy-bitmapfont wqy-microhei wqy-zenhei wjy-microhei-lite
```

# 更改urxvt的字体

Manjaro默认终端是urxvt。

编辑`~/.Xresources`：

```
xft.dpi:125  #设置dpi，对4k高分屏需要设置，设置成默认值的2倍试试。
URxvt.font: xft:Source Code Pro:antialias=True:pixelsize=18,xft:WenQuanYi Zen Hei:pixelsize=18
URxvt.boldfont: xft:Source Code Pro:antialias=True:pixelsize=18,xft:WenQuanYi Zen Hei:pixelsize=18
```

# 配置Shell

## 切换到zsh

查看系统已经安装有哪些Shell：

```bash
$ cat /etc/shells
或者
$ chsh -l
```

> Manjaro默认已经安装了zsh。

查看当前使用的Shell：

```bash
$ echo $0
或者
$ echo $SHELL
```

切换到zsh：

```bash
$ chsh -s /bin/zsh
```

需要重启一下系统。

也可以直接修改`/etc/passwd`中某个用户默认的Shell。

## 安装oh-my-zsh

安装：

```bash
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

> 不要加`sudo`，否则会安装到`root`用户下，而不是当前用户。

安装完成后，重启系统。

修改主题（默认是`robbyrussell`）：编辑`~/.zshrc`

```
ZSH_THEME="agnoster"
```

官方主题位于：`.oh-my-zsh/themes/`下，第三方主题位于：`.oh-my-zsh/custom/themes/`下。

第三方主题Bullet Train也不错，安装步骤如下：

1. 将[bullet-train.zsh-theme](http://raw.github.com/caiogondim/bullet-train-oh-my-zsh-theme/master/bullet-train.zsh-theme)下载到`.oh-my-zsh/custom/themes/`。
2. 配置`~/.zshrc`：`ZSH_THEME="bullet-train"`。

## 更新oh-my-zsh

```bash
$ upgrade_oh_my_zsh
```

## 卸载oh-my-zsh

```bash
$ uninstall_oh_my_zsh
```

# 配置dmenu

编辑文件`~/.dmenurc`。

# 配置i3

编辑文件`~/.i3/config`。

# 配置状态栏

```bash
$ mkdir ~/.config/i3status
$ cp /etc/i3status.conf ~/.config/i3status/config
$ vim ~/.config/i3status/config
```

修改时间格式：

```
tztime local {
	format = " %Y-%m-%d %H:%M:%S "
}
```

查看i3status的帮助：

```bash
$ man i3status
```

# 配置conky

桌面右侧的状态栏：`/usr/share/conky/conky_maia`。

桌面左侧的快捷键显示栏：`/usr/share/conky/conky1.10_shortcuts_maia`。

修改conky_maia支持中文：

```
font = 'WenQuanYi Micro Hei:size=8',
或者
font = 'WenQuanYi Zen Hei:size=8',
```

# 安装JDK

查看所有己安装的JDK：

```bash
$ archlinux-java status
```

安装Oracle JDK：

```bash
$ sudo pacman -S jdk
```

设置`JAVA_HOME`环境变量：修改`~/.zshrc`。

切换版本：

```bash
$ sudo archlinux-java set java-10-jdk
```



