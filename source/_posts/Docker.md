---
title: Docker
date: 2018-04-13 10:00:28
tags:
---

# 简介

## Docker与虚拟机的区别

Docker只能运行在与底层宿主机相同或相似的操作系统上（例如：包含RHEL的Docker可以运行在Ubuntu上，但包含Windows的Docker无法运行在Ubuntu上），而虚拟机则没有这种限制。

# 安装

在Windows和Mac OS中推荐安装Docker Desktop。

如果在使用Docker Desktop时出现连接超时，则右击系统托盘，Settings -> Network，将DNS Server由默认的Automatic改成Fixed（8.8.8.8）。

## 配置

### 使用国内镜像

方法一：

```bash
# vim /etc/docker/daemon.json
{
	"registry-mirrors": ["https://registry.docker-cn.com"]
}
# systemctl restart docker
```

方法二：

```bash
# dockerd --registry-mirrors=https://registry.docker-cn.com
```



# 入门



