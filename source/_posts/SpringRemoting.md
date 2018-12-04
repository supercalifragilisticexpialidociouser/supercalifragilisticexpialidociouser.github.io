---
title: SpringRemoting
date: 2018-12-03 16:58:59
tags:
---

# 远程调用模型

Spring通过多种远程调用技术支持RPC：

| RPC模型             | 适用场景                                                     |
| ------------------- | ------------------------------------------------------------ |
| 远程方法调用（RMI） | 不考虑网络限制（例如防火墙），访问/发布基于Java的服务。      |
| Hessian或Burlap     | 考虑网络限制，通过HTTP访问/发布基于Java的服务。Hessian是二进制协议，而Burlap是基于XML的。 |
| HTTP invoker        | 考虑网络限制，并希望使用基于XML或专有的序列化机制实现Java序列化时，访问/发布基于Spring的服务。 |
| JAX-RPC和JAX-WS     | 访问/发布平台独立的、基于SOAP的Web服务。                     |

在Spring中，远程服务被代理，所以它们能够像其他Spring Bean一样被装配到客户端代码中。代理代表客户端与远程服务进行通信，由它负责处理连接的细节并向远程服务发起调用。

![远程服务](SpringRemoting/remote-services.png)

在服务端，Spring通过远程导出器（remote exporter）将Bean发布为远程服务。

![远程导出器](SpringRemoting/remote-exporter.png)

任何传递给远程调用的Bean或从远程调用返回的Bean需要实现`java.io.Serializable`接口。

# RMI

## 导出RMI服务

通常创建一个RMI服务需要涉及好多步骤：

1. 编写一个服务实现类，类中的方法必须抛出`java.rmi.RemoteException`异常；
2. 创建一个继承于`java.rmi.Remote`的服务接口；
3. 运行RMI编译器（rmic），创建客户端stub类和服务端skeleton类；
4. 启动一个RMI注册表，以便持有这些服务；
5. 在RMI注册表中注册服务。

幸运的是，Spring提供了更简单的方式来发布RMI服务，不用再编写那些需要抛出`RemoteException`异常的特定RMI类，只需要简单地编写实现服务功能的POJO就可以了，Spring会处理剩余的其他事项。

```java
public interface SpitterService {
  List<Spittle> getRecentSpittles(int count);
  void saveSpittle(Spittle spittle);
  void saveSpitter(Spitter spitter);
  Spitter getSpitter(long id);
  void startFollowing(Spitter follower, Spitter followee);
  List<Spittle> getSpittlesForSpitter(Spitter spitter);
  List<Spittle> getSpittlesForSpitter(String username);
  Spitter getSpitter(String username);
  Spittle getSpittleById(long id);
  void deleteSpittle(long id);
  List<Spitter> getAllSpitters();
}
```



## 装配RMI服务

# Hessian和Burlap

# Spring的HttpInvoker

# Web服务