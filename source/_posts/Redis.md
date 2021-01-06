---
title: Redis
date: 2020-12-27 15:28:24
tags: [6.0.9]
---

# 安装

方法一：使用Docker安装

```bash
# 拉取Redis镜像
> docker pull redis
# 启动Redis容器
> docker run --name myredis -d -p6379:6379 redis
# 运行容器中的redis-cli
> docker exec -it myredis redis-cli
```

方法二：从Github拉取源码编译安装（略）

方法三：使用预构建的二进制安装包安装（略）

# 基础数据结构

## string（字符串）

Redis的字符串是动态扩容的。当字符串长度小于1MB时，扩容是加倍现有空间。当字符串长度超过1MB时，扩容时一次只会多扩1MB。

字符串最大长度为512MB。

简单的增删改查操作：

```bash
> set xxx yyy
OK
> get xxx
"yyy"
> exists xxx
(integer) 1
> del xxx
(integer) 1
> get xxx
(nil)
> setnx foo bar  # 如果foo不存在就执行set创建，否则，不执行set
```

批量读写：

```bash
> mset x1 y1 x2 y2
> mget x1 x2
1) "y1"
2) "y2"
```

过期操作：

```bash
> set xxx yyy
> expire xxx 5  # 5秒后过期

> setex x3 5 y3 # 5秒后过期，相当于set+expire
```

若值是整数，还可以对它进行自增操作：

```bash
> set age 30
> incr arg
(integer) 31
> incrby age 5
(integer) 36
> incrby age -5
(integer) 31
```

自增的范围在`signed long`的最大值和最小值之间，超出范围会报错。

## list（列表）

Redis的列表是双向链表，因此，它插入和删除操作非常快，但索引定位很慢。

列表可用来做异步队列（`lpop`）或栈（`rpop`）：

```bash
> rpush books python java golang
(integer) 3
> llen books
(integer) 3
> lpop books
"python"
> rpop books
"golang"
```

获取操作（O(n)慎用）：

```bash
> rpush books python java golang
> lindex books 1
"java"
> lrange books 0 -1  # -1表示倒数第一个元素
1) "python"
2) "java"
3) "golang"
> ltrim books 1 -1  # 截掉区间外的元素
OK
> ltrim books 1 0 # 清空列表
OK
> llen books
(integer) 0
```

## hash（字典）

```bash
> hset books java "think in java" # 字符串要包含空格，要用引号括起来
(integer) 1
> hset books golang "concurrency in go"
(integer) 1
> hgetall books # 键和值间隔出现
1) "java"
2) "think in java"
3) "golang"
4) concurrency in go"
> hlen books
(integer) 2
> hget books java
"think in java"
> hset books golang "learning go programming" # 更新操作，返回0
(integer) 0
> hmset books java "effective java" python "learning python" # 批量
OK

> hset user-laoqian age 29
> hincrby user-laoqian age 1
(integer) 30
```

## set（集合）

Redis的集合相当于Java里的`HashSet`。

```bash
> sadd books python
(integer) 1
> sadd books python
(integer) 0
```



## zset（有序集合）