---
title: SpringTask
date: 2019-01-03 11:11:34
tags:
---

不需要用户参与的后台任务有两种：

- 调度任务
- 异步方法

<!--more-->

# 启用标注驱动的后台任务

```xml
<task:annotation-driven />
```

或者：

```java
@Configuration
@EnableAsync  //启用异步方法
@EnableScheduling  //启用调度任务
public class AppConfig {
}
```

# 声明调度方法

每隔（两次开始调用之间）24小时（86400000毫秒）触发一个方法：

```java
@Scheduled(fixedRate=86400000)
public void doSomething() {
  // something that should execute periodically
}
```

每隔（上次调用完成后到这次开始调用之间）24小时（86400000毫秒）触发一个方法：

```java
@Scheduled(fixedDelay=86400000)
public void doSomething() {
  // something that should execute periodically
}
```

在1000毫秒之后开始执行第一次，以后每隔5000毫秒执行一次：

```java
@Scheduled(initialDelay=1000, fixedRate=5000)
public void doSomething() {
  // something that should execute periodically
}
```

周一至周五每隔5秒执行一次：

```java
@Scheduled(cron="*/5 * * * * MON-FRI")
public void doSomething() {
  // something that should execute on weekdays only
}
```

Cron表达式由6个或7个空格分隔的域构成。从左至右依次：

- 秒（0~59）；
- 分钟（0~59）；
- 小时（0~23）；
- 月份中的日期（1~31）；
- 月份（1~12 或 JAN~DEC）；
- 星期中的日期（1~7 或 SUN~SAT）；
- 年份（可选，1970~2099）。

每个域都可以：

- 指定单个值，如`6`；
- 指定一个范围，如`9-12`；
- 指定一个列表，如`9,11,13`；
- 指定一个通配符，如`*`；
- 指定一个起始时间和每次间隔时间，如`5/20`。

`L`只能出现在“月份中的日期”或“星期中的日期”域中。当出现在“月份中的日期”域中时，表示指定月份的最后一天（单独使用）或倒数第几天（例如`6L`表示一个月的倒数第6天）；当出现在“星期中的日期”域中时，表示星期六（单独使用）或一个月中的最后一个星期几（例如`6L`表示一个月的最后一个星期五）。

只能出现在“月份中的日期”域中：

- `W`：表示离指定日期最近的有效工作日（周一至周五）。例如，如果5日是星期六，则`5W`表示4日（即最近的工作日——星期五）；如果5日是星期日，则`5W`表示6日（周一）；如果5日是星期一至星期五中的一天，则`5W`表示5日。另外，`W`在寻找最近工作日时不会跨过月份。
- `LW`：表示某个月最后一个星期五。

`#`只出现在“星期中的日期”域中，表示一个月中第几个星期几。例如，`6#3`或`FRI#3`表示一个月的第三个星期五。

另外，月份中的日期和星期中的日期是互斥的，应该通过设置一个`?`来表明你不想设置的那个字段。

示例：

`0 0 10,14,16 * * ?`：每天上午10点，下午2点和下午4点。

`*/5 * * * * ?`：每隔5秒。

`0 0/30 9-17 * * ?`：上午9点正至下午5点内每隔半小时（从0分开始）。

`0 0 12 ? * WED`：表示每个星期三中午12点。

`0 15 10 ? * *`、`0 15 10 * * ?`或`0 15 10 * * ? *`：每天上午10:15。

`0 * 14 * * ?`：每天下午2点正到下午2:59期间的每1分钟。

`0 0/5 14,18 * * ?`：每天下午2点正到2:55期间和下午6点正到6:55期间的每5分钟（从0分开始）。

`0 0-5 14 * * ?`：每天下午2点到下午2:05期间的每1分钟。

`0 10,44 14 ? 3 WED`：每年三月的星期三的下午2:10和2:44。

`0 15 10 L * ?`：每月最后一日的上午10:15。

`0 15 10 ? * 6L 2002-2005`：2002年至2005年的每月的最后一个星期五上午10:15。

`0 15 10 ? * 6#3`：每月的第三个星期五上午10:15。

# 声明异步方法

在一个Bean的方法上标注`@Async`，该方法就会变成异步的。

```java
@Async
public void addSpittle(Spittle spittle) {
  …
}
```

当`addSpittle`方法被调用时，控制权会立即交给调用者。同时，`addSpittle`方法将会在后台继续执行。

异步方法也可以有返回值：

```java
@Async
public Future<Long> performSomeReallyHairyMath(long input) {
  …
  return new AsyncResult<Long>(result);
}
```

在异步方法的结果计算过程中，调用者会持有一个`Future`对象（实际上是`AsyncResult`对象）。一旦结果出来，调用者可以通过调用`Future`对象的`get`方法来得到它。在此之前，调用者可以使用`isDone`方法和`isCancelled`方法来判断结果的状态。