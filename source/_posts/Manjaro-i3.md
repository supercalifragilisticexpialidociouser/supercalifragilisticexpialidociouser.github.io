---
title: Manjaro-i3
date: 2019-02-28 13:53:25
tags: [21.0.7]
---

# 配置源

打开GUI安装器（mod-Z，选中“System”->“Add/remove software”） -> 首选项 ->官方软件仓库 -> “使用镜像”改成“China”，然后点击“刷新镜像列表”按钮。。

或者执行下列命令：

```bash
$ sudo pacman-mirrors -c China
```

# 更新系统

使用GUI安装器更新系统。

或者：

```bash
$ sudo pacman -Syyu
```

> 如果有大量软件包要更新，建议使用命令行。GUI更新工具比较慢，而且容易崩溃。

更新完成后，重启系统。

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

## 安装主题（可选）

Manjaro自带的主题是Powerlevel10k的改版，已经很好用了。

### Powerlevel10k主题

它是Powerlevel9k的升级版，速度更快。参见：https://github.com/romkatv/powerlevel10k

安装ttf-meslo-nerd-font-powerlevel10k字体

### Pure主题

参见：https://github.com/sindresorhus/pure

# 安装字体

用GUI安装器或下列命令安装ttf-meslo-nerd-font-powerlevel10k、wqy-microhei字体：（新版本Manjaro已经默认安装了adobe-source-code-pro-fonts）

```bash
$ pacman -S ttf-meslo-nerd-font-powerlevel10k wqy-microhei
```

# 配置conky

桌面右侧的状态栏：`/usr/share/conky/conky_maia`。

桌面左侧的快捷键显示栏：`/usr/share/conky/conky1.10_shortcuts_maia`。

修改conky_maia支持中文：（修改conky_maia中的size=16和size=18的字体为WenQuanYi Micro Hei。）

```
font = 'WenQuanYi Micro Hei:size=8',
```

# 配置URxvt

Manjaro默认终端是URxvt。

## 更改URxvt的字体

编辑`~/.Xresources`：

```
xft.dpi:125  #设置dpi，对4k高分屏需要设置，设置成默认值的2倍试试。
……
URxvt.font: xft:MesloLGS NF:size=11  #使用Nerd Font
```

重启系统。

## 使用Chromium打开链接

编辑`~/.Xresources`：

```
URxvt*url-launcher:     chromium
```

# 更换浏览器

首先，安装Chromium浏览器：

使用GUI安装器安装，或者：

```bash
$ sudo pacman -S google-chrome
```

将`Mod-F2`快捷键配置成打开Chromium，这通过修改`~/.i3/config`：

```
bindsym $mod+F2 exec chromium（通过GUI安装器安装的，选择这个）
或
bindsym $mod+F2 exec google-chrome-stable
```

修改`~/.profile`：将`export BROWSER=/usr/bin/palemoon`改成`export BROWSER=/usr/bin/chromium`。

然后，卸载原装的Pale Moon浏览器。

重启系统。

# 安装输入法

通过GUI安装器安装*fcitx*和*fcitx-configtool*，或者通过命令安装：

```bash
$ sudo pacman -S fcitx-im #默认全部安装
$ sudo pacman -S fcitx-configtool
```

安装完成后，编辑`~/.profile`或`~/.xprofile`，添加如下配置：

```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

重启系统后，按mod-D，然后输入`fcitx`回车，状态栏上就会出现输入法。右击状态栏中的输入法图标，选中“配置”菜单进入配置界面，点击添加输入法。只	留下“五笔拼音”输入法。

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

# 安装JDK

安装最新版Open JDK，使用GU安装器安装或者使用命令安装：

```bash
$ sudo pacman -S jdk-openjdk
```

安装指定版本Open JDK（例如JDK 11）：

```bash
$ sudo pacman -S jdk11-openjdk
```

设置`JAVA_HOME`环境变量：修改`~/.zshrc`。

```
export JAVA_HOME="/usr/lib/jvm/default"
```

查看所有己安装的JDK：

```bash
$ archlinux-java status
```

切换版本：

```bash
$ sudo archlinux-java set java-11-openjdk
```

# 安装VS Code

使用GUI安装器安装。

设置自带终端的字体：打开*File → Preferences → Settings*，找到*terminal.integrated.fontFamily*，将它的值设置为 `MesloLGS NF`。

安装如下扩展：

- Language Support for Java(TM) by Red Hat
- Maven for Java
- Project Manager for Java
- Debugger for Java
- Java Test Runner
- Spring Initializr Java Support
- EditorConfig for VS Code
- Angular Language Service
- Angular Snippets (Version 12)

# 安装Typora

```bash
$ yay -S typora
```

# 安装NVM

使用GUI安装器安装。

然后，执行：

```bash
$ echo 'source /usr/share/nvm/init-nvm.sh' >> ~/.zshrc
```

重新打开终端。

安装指定版本的Node.js：

```bash
$ nvm install 14.17.2
```

查找可用的LTS Node.js：

```bash
$ nvm ls-remote --lts
```

设置国内源：
