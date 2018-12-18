---
title: SpringMessage
date: 2018-12-12 11:09:46
tags:
---

像RMI、HTTP invoker等远程调用机制，以及Web服务、RESTful都是同步通信，而消息则属于异步通信。

在异步消息中有两个主要概念：消息代理（message broker）和目的地（destination）。

当一个应用发送信息时，会将消息交给一个消息代理（相当于邮局）。消息代理可以确保消息被投递到指定的目的地，同时解放发送者，使其能够继续进行其他的业务。

有两种通用的目的地：队列（queue）和主题（topic）。它们分别对应两种消息模型：点对点模型和发布/订阅模型。

# 消息模型

## 点对点模型

在点对点模型中，每一条消息都有一个发送者，并且都只会被一个接收者接收。

![点对点模型](SpringMessage/point-to-point.png)

尽管消息队列中中的每条消息只会被一个接收者取走，但是并不意味着只能使用一个接收者从队列中获取消息。事实上，通常会有多个接收者来处理队列中的消息。类似于银行排队办理业务一样，银行柜员类似于接收者。

在点对点模型中，如果有多个接收者监听队列，我们是无法知道某条特定消息会由哪一个接收者处理。

## 发布/订阅模型

在发布/订阅模型中，消息会发送给一个主题。与队列类似，多个接收者都可以监听一个主题。但是，与队列不同的是，消息不再是只投递给一个接收者，而是主题的所有订阅者都会接收到此消息的副本。类似于杂志发行商与杂志订阅者。

![发布/订阅模型](SpringMessage/publish-subscribe.png)

# JMS

JMS（Java Message Service）是一个Java标准，定义了使用消息代理的通用API。

## 安装消息代理

ActiveMQ是使用JMS进行异步消息传递的最佳选择。

首先，下载并解压发行包；

然后，在`解压目录/bin/指定操作系统子目录/`下，运行`activemq start`来启动ActiveMQ。

另外，要将解压目录下的`activemq-core-x.x.x.jar`添加到应用程序的类路径中，这样在应用程序中就可以使用ActiveMQ的API了。

## 创建连接工厂

应用程序需要借助JMS连接工厂通过消息代理发送消息，所以我们还要配置JMS连接工厂，让它知道如何连接到ActiveMQ。

```xml
<bean id="connectionFactory"
      class="org.apache.activemq.spring.ActiveMQConnectionFactory"
      p:brokerURL="tcp://localhost:60000"/>
```

如果省略`brokerURL`属性，则ActiveMQ代理默认监听`localhost`的61616端口。

另外，也可以使用ActiveMQ自己的Spring配置命名空间来声明连接工厂：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:jms="http://www.springframework.org/schema/jms"
       xmlns:amq="http://activemq.apache.org/schema/core"
       xsi:schemaLocation="http://activemq.apache.org/schema/core
                           http://activemq.apache.org/schema/core/activemq-core.xsd
                           http://www.springframework.org/schema/jms
                           http://www.springframework.org/schema/jms/spring-jms.xsd
                           http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">
  <amq:connectionFactory id="connectionFactory"
                         brokerURL="tcp://localhost:61616"/>
  ...
</beans>
```

## 声明消息目的地

消息目的地可以是一个队列，也可以是一个主题，这取决于应用的需求。

### 声明队列

```xml
<bean id="spittleQueue"
      class="org.apache.activemq.command.ActiveMQQueue"
      c:_="spitter.queue" />
```

或者：

```xml
<amq:queue id="spittleQueue" physicalName="spittle.queue" />
```

### 声明主题

```xml
<bean id="spittleTopic"
      class="org.apache.activemq.command.ActiveMQTopic"
      c:_="spitter.topic" />
```

或者：

```xml
<amq:topic id="spittleTopic" physicalName="spittle.topic" />
```

## 使用传统的JMS

### 发送消息

```java
ConnectionFactory cf = new ActiveMQConnectionFactory("tcp://localhost:61616");
Connection conn = null;
Session session = null;
try {
  conn = cf.createConnection();
  session = conn.createSession(false, Session.AUTO_ACKNOWLEDGE);
  Destination destination = new ActiveMQQueue("spitter.queue");
  MessageProducer producer = session.createProducer(destination);
  TextMessage message = session.createTextMessage();
  message.setText("Hello world!");
  producer.send(message);
} catch (JMSException e) {
  // handle exception?
} finally {
  try {
    if (session != null) {
      session.close();
    }
    if (conn != null) {
      conn.close();
    }
  } catch (JMSException ex) {
  }
}
```

### 接收消息

```java
ConnectionFactory cf = new ActiveMQConnectionFactory("tcp://localhost:61616");
Connection conn = null;
Session session = null;
try {
  conn = cf.createConnection();
  conn.start();
  session = conn.createSession(false, Session.AUTO_ACKNOWLEDGE);
  Destination destination = new ActiveMQQueue("spitter.queue");
  MessageConsumer consumer = session.createConsumer(destination);
  Message message = consumer.receive();
  TextMessage textMessage = (TextMessage) message;
  System.out.println("GOT A MESSAGE: " + textMessage.getText());
  conn.start();
} catch (JMSException e) {
  // handle exception?
} finally {
  try {
    if (session != null) {
      session.close();
    }
    if (conn != null) {
      conn.close();
    }
  } catch (JMSException ex) {
  }
}
```

## 使用JmsTemplate

### 注册JmsTemplate Bean

```xml
<bean id="jmsTemplate"
      class="org.springframework.jms.core.JmsTemplate"
      c:_-ref="connectionFactory" />
```

#### 设置默认目的地

如果总是将消息发送给或接收自相同的目的地，则可以为`JmsTempate`装配一个默认的目的地。这样，每次发送或接收消息时就不需要指定一个目的地了：

```xml
<bean id="jmsTemplate"
      class="org.springframework.jms.core.JmsTemplate"
      c:_-ref="connectionFactory"
      p:defaultDestinationName="spittle.queue" />
```

`defaultDestinationName`属性只是指定了一个目的地的名称，它没有说明目的地是什么类型。如果已经存在该名称的队列或主题，就会使用已有的。否则，将会创建一个新的目的地（通常会是队列）。

如果想指定要创建的目的地类型，则需要使用`defaultDestination-ref`属性将之前创建的队列或主题的目的地Bean装配进来：

```xml
<bean id="jmsTemplate"
      class="org.springframework.jms.core.JmsTemplate"
      c:_-ref="connectionFactory"
      p:defaultDestination-ref="spittleTopic" />
```

现在调用`JmsTempate`的`send`方法时，我们可以省略目的地参数：

```java
jmsOperations.send(
  new MessageCreator() {
    ...
  }
);
```

### 发送消息

```java
public interface AlertService {
	void sendSpittleAlert(Spittle spittle);
}

public class AlertServiceImpl implements AlertService {
  private JmsOperations jmsOperations;
  @Autowired
  public AlertServiceImpl(JmsOperations jmsOperatons) {
    this.jmsOperations = jmsOperations;
  }
  public void sendSpittleAlert(final Spittle spittle) {
    jmsOperations.send(
      "spittle.queue",  //指定目的地
      new MessageCreator() {
        public Message createMessage(Session session) throws JMSException {
          return session.createObjectMessage(spittle); //创建消息
        }
      }
    );
  }
}
```

`JmsOperation`是`JmsTemplate`所实现的接口。

![使用JmsTempate发送消息](SpringMessage/JmsTempate-send.png)

#### 对消息进行转换

除了`send`方法外，`JmsTemplate`还提供了`convertAndSend`方法，它不需要`MessageCreator`作为参数，而是使用内置的消息转换器为我们创建消息，然后发送消息。

```java
public void sendSpittleAlert(Spittle spittle) {
  jmsOperations.convertAndSend(spittle);
}
```

Spring提供的消息转换器：

| 消息转换器                      | 功能                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| MappingJacksonMessageConverter  | 使用Jackson JSON库实现消息与JSON格式之间的相互转换。         |
| MappingJackson2MessageConverter | 使用Jackson 2 JSON库实现消息与JSON格式之间的相互转换。       |
| MarshallingMessageConverter     | 使用JAXB库实现消息与XML格式之间的相互转换。                  |
| SimpleMessageConverter          | 实现`String`与`TextMessage`之间，字节数组与`BytesMessage`，`Map`与`MapMessage`之间，`Serializable`对象与`ObjectMessage`之间的相互转换。 |

默认情况下，`JmsTemplate`在`convertAndSend`方法中使用`SimpleMessageConverter`。如果要自己指定消息转换器，则首先声明一个消息转换器Bean，例如：

```xml
<bean id="messageConverter"
  class="org.springframework.jms.support.converter.MappingJacksonMessageConverter" />
```

> 各个消息转换器可能会需要配置一些额外的属性。

然后，将其注入到`JmsTemplate` Bean中：

```xml
<bean id="jmsTemplate"
      class="org.springframework.jms.core.JmsTemplate"
      c:_-ref="connectionFactory"
      p:defaultDestinationName="spittle.alert.queue"
      p:messageConverter-ref="messageConverter" />
```

如果内置的消息转换器满足不了需求，则可以自己实现消息转换器，然后将其注入到`JmsTemplate` Bean中。自定义的消息转换器要实现`MessageConverter`接口。

### 接收消息

#### 同步接收

```java
public Spittle receiveSpittleAlert() {
  try {
    ObjectMessage receivedMessage =
      (ObjectMessage) jmsOperations.receive(); //从默认目的地接收消息
    return (Spittle) receivedMessage.getObject();
  } catch (JMSException jmsException) { //由gerObject()抛出
    throw JmsUtils.convertJmsAccessException(jmsException); //抛出转换后的非检查型异常
  }
}
```

![使用JmsTemplate接收消息](SpringMessage/JmsTemplate-receive.png)

`receive`方法会尝试从消息代理中获取一条消息，如果没有可用的消息，它会一直等待，直到获得消息（或超时）为止。

`JmsTemplate`只能处理自己方法抛出的`JMSException`异常，它无法处理`ObjectMessage`的`getObject`方法所抛出的`JMSException`异常。因此，我们要么捕获`JMSException`异常，要么声明抛出`JMSException`异常。这里，使用Spring中JmsUtils的`convertJmsAccessException`方法把检查型异常`JMSException`转换为非检查型异常`JmsException`。

同步接收消息也可以使用消息转换器，也就是使用`JmsTemplate`的`receiveAndConvert`方法。

```java
public Spittle retrieveSpittleAlert() {
	return (Spittle) jmsOperations.receiveAndConvert();
}
```

现在没有必要将`Message`转换为`ObjectMessage`，也没有必要通过调用`getObject`方法来获取`Spittle`，更无需担心检查型异常`JMSException`。

使用`JmsTemplate`接收消息的最大缺点在于`receive`方法和`receiveAndConvert`方法都是同步的，它们会一直阻塞，直到有可用消息（或者直到超时）。

## 使用消息驱动POJO：异步接收

### 创建消息监听器

Spring的消息监听器类就是一个POJO：

```java
public class SpittleAlertHandler {
  public void handleSpittleAlert(Spittle spittle) { //处理方法
    // ... implementation goes here...
  }
}
```

### 配置消息监听器

首先，将上面创建的消息监听器类配置成Bean：

```xml
<bean id="spittleHandler"
      class="com.habuma.spittr.alerts.SpittleAlertHandler" />
```

然后，把这个Bean注册为消息监听器：

```xml
<jms:listener-container connection-factory="connectionFactory">
  <jms:listener destination="spitter.queue"
                ref="spittleHandler" method="handleSpittleAlert" />
</jms:listener-container>
```

消息监听器容器是一个特殊Bean，它可以监控JMS目的地并等待消息到达。一旦有消息到达，它取出消息，然后把消息传给任意一个对此消息感兴趣的消息监听器。

![消息监听器](SpringMessage/message-listener.png)

`connection-factory`属性配置了对`connectionFactory`的引用，容器中每个消息监听器都使用这个连接工厂进行消息监听。在本例中，`connection-factory`属性可以移除，因为该属性的默认值就是`connectionFactory`。

另外，如果`ref`引用的Bean实现了`MessageListener`接口，则`method`属性也可移除，这时默认就是`onMessage`方法。

## 使用基于消息的RPC

现在我们将了解一下如何使用JMS作为传输通道来进行远程调用。

为了支持基于消息的RPC，Spring提供了`JmsInvokerServiceExporter`，它可以把Bean导出为基于消息的服务；为客户端提供了`JmsInvokerProxyFactoryBean`来使用这些服务。

### 导出基于JMS的服务

要导出的服务：

```java
public interface AlertService {
	void sendSpittleAlert(Spittle spittle);
}

@Component("alertService")
public class AlertServiceImpl implements AlertService {
  private JavaMailSender mailSender;
  private String alertEmailAddress;
  public AlertServiceImpl(JavaMailSender mailSender,
                          String alertEmailAddress) {
    this.mailSender = mailSender;
    this.alertEmailAddress = alertEmailAddress;
  }
  public void sendSpittleAlert(final Spittle spittle) {
    SimpleMailMessage message = new SimpleMailMessage();
    String spitterName = spittle.getSpitter().getFullName();
    message.setFrom("noreply@spitter.com");
    message.setTo(alertEmailAddress);
    message.setSubject("New spittle from " + spitterName);
    message.setText(spitterName + " says: " + spittle.getText());
    mailSender.send(message);
  }
}
```

配置`JmsInvokerServiceExporter`：

```xml
<bean id="alertServiceExporter"
      class="org.springframework.jms.remoting.JmsInvokerServiceExporter"
      p:service-ref="alertService"
      p:serviceInterface="com.habuma.spittr.alerts.AlertService" />
```

`JmsInvokerServiceExporter`可以充当JMS监听器，因此，我们使用`<jms:listener-container>`配置它：

```xml
<jms:listener-container connection-factory="connectionFactory">
  <jms:listener destination="spitter.queue"
                ref="alertServiceExporter" />
</jms:listener-container>
```

这样，基于JMS的服务就准备好了，它将等待`spitter.queue`队列中RPC消息的到达。

### 使用基于JMS的服务

在客户端配置`JmsInvokerProxyFactoryBean`代理来访问JMS服务：

```xml
<bean id="alertService"
      class="org.springframework.jms.remoting.JmsInvokerProxyFactoryBean"
      p:connectionFactory-ref="connectionFactory"
      p:queueName="spittle.queue"
      propp:serviceInterface="com.habuma.spittr.alerts.AlertService" />
```

# AMQP

与JMS不同，AMQP的生产者并不会直接将消息发布到队列中，AMQP在消息的生产者和传递信息的队列之间引入了一种间接的机制：Exchange。消息生产者将信息发布到一个Exchange，Exchange会绑定到一个或多个队列上，它负责将信息路由到队列上。

![AMQP](SpringMessage/AMQP.png)

AMQP定义了四种不同类型的Exchange：

- Direct：如果消息的routing key与binding的routing key直接匹配的话，消息将会路由到该队列上；
- Topic：如果消息的routing key与binding的routing key符合通配符匹配，则消息将会路由到该队列上；
- Headers：如果消息参数表中的头信息和值都与binding参数表中相匹配，则消息将会路由到该队列上；
- Fanout：不管消息的routing key和参数表中的头信息/值是什么，消息将会路由到所有队列上。

另外，Exchange可以绑定到另一个Exchange上。

## 创建连接工厂

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/rabbit"
             xmlns:beans="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://www.springframework.org/schema/rabbit
                                 http://www.springframework.org/schema/rabbit/spring-rabbit-1.0.xsd
                                 http://www.springframework.org/schema/beans
                                 http://www.springframework.org/schema/beans/spring-beans.xsd">
  <connection-factory id="connectionFactory"
                      host="${rabbitmq.host}"
                      port="${rabbitmq.port}"
                      username="${rabbitmq.username}"
                      password="${rabbitmq.password}" />
  ...
</beans:beans>
```

`host`、`port`、`username`和`password`都是可选，如果省略，则RabbitMQ默认监听`localhost`的5672端口，并且用户名和密码均为`guest`。

## 声明队列、Exchange以及binding

在JMS中，队列和主题的路由行为都是通过规范建立的，而AMQP则不同，它的路由方式可通过队列、Exchange以及binding的各种组合实现。

例如：点对点模型

```xml
<admin connection-factory="connectionFactory" />
<queue id="spittleAlertQueue" name="spittle.alerts" />
```

这里会有一个默认的没有名称的Direct Exchange，所有队列都会绑定到这个Exchange上，并且routing key与队列的名称相同。

更复杂的例子：

```xml
<admin connection-factory="connectionFactory" />
<queue name="spittle.alert.queue.1" />
<queue name="spittle.alert.queue.2" />
<queue name="spittle.alert.queue.3" />
<fanoutexchange name="spittle.fanout">
  <bindings>
    <binding queue="spittle.alert.queue.1" />
    <binding queue="spittle.alert.queue.2" />
    <binding queue="spittle.alert.queue.3" />
  </bindings>
</fanoutexchange>
```

## 发送消息

### 注册RabbitTemplate

```xml
<template id="rabbitTemplate" connection-factory="connectionFactory" />
```

### 使用convertAndSend方法发送消息

```java
public class AlertServiceImpl implements AlertService {
  private RabbitTemplate rabbit;
  @Autowired
  public AlertServiceImpl(RabbitTemplate rabbit) {
    this.rabbit = rabbit;
  }
  public void sendSpittleAlert(Spittle spittle) {
    rabbit.convertAndSend("spittle.alert.exchange", //Exchange名称
                          "spittle.alerts",  //routing key
                          spittle);  //发送的对象
  }
}
```

另外，如果在`<template`上使用`exchange`和`routing-key`属性配置默认的Exchange名称和routint key，则在调用`convertAndSend`方法时，可以省略前两个参数：

```xml
<template id="rabbitTemplate"
          connection-factory="connectionFactory"
          exchange="spittle.alert.exchange"
          routing-key="spittle.alerts" />
```

则：

```java
rabbit.convertAndSend(spittle);
```

最后，如果在`<template>`和`convertAndSend`方法中均未指定Exchange名称和routing key，则Exchange名称和routing key均默认为空。

通过`convertAndSend`方法指定的Exchange名称和routing key，将覆盖`<template>`的相应配置。

`convertAndSend`方法会自动将对象转换为`Message`对象。它需要借助一个消息转换器来完成该转换，默认的消息转换器是`SimpleMessageConverter`，它适用于`String`、`Serializable`实例以及字节数组。

### 使用send方法发送消息

我们还可以使用较低级的`send`方法来直接发送`org.springframework.amqp.core.Message`对象：

```java
Message helloMessage =
  new Message("Hello World!".getBytes(), new MessageProperties());
rabbit.send("hello.exchange", "hello.routing", helloMessage);
```

## 接收消息

### 使用RabbitTemplate来同步接收消息

#### 注册RabbitTemplate

```xml
<template id="rabbitTemplate"
          connection-factory="connectionFactory"
          exchange="spittle.alert.exchange"
          routing-key="spittle.alerts"
          queue="spittle.alert.queue" />
```

#### 使用receive方法接收消息

```java
Message message = rabbit.receive();
```

`receive`方法的其他重载版本可以显式指定Exchange名称和routing key来覆盖`<tempate>`中的配置。

```java
Message message = rabbit.receive("spittle.alert.queue2");
```

#### 使用receiveAndConvert方法接收消息

`receiveAndConvert`方法会使用与`sendAndConvert`方法相同的消息转换器，来将`Message`对象转换为原始的类型。

```java
Spittle spittle = (Spittle) rabbit.receiveAndConvert();
```

#### 管理轮询

调用`receive`和`receiveAndConvert`方法都会立即返回。如果队列中没有等待的消息时，将会得到`null`。这就需要我们来管理轮询（polling）以及必要的线程，实现队列的监控。

### 使用消息驱动的POJO来异步接收消息

我们并非必须同步轮询并等待消息到达，Spring AMQP还提供了消息驱动POJO的支持。

#### 创建消息监听器POJO类

我们可以完全重用JMS的消息监听器POJO类——`SpittleAlertHandler`，因为它丝毫没有依赖于JMS或AMQP。

```java
public class SpittleAlertHandler {
  public void handleSpittleAlert(Spittle spittle) {
    // ... implementation goes here ...
  }
}
```

#### 配置消息监听器

首先，将上面创建的消息监听器类配置成Bean（与配置JMS消息监听器完全相同）：

```xml
<bean id="spittleListener"
      class="com.habuma.spittr.alert.SpittleAlertHandler" />
```

然后，把这个Bean注册为消息监听器：

```xml
<rabbit:listener-container connection-factory="connectionFactory">
  <rabbit:listener ref="spittleListener"
                   method="handleSpittleAlert"
                   queue-names="spittle.alert.queue" />
</rabbit:listener-container>
```

`queue-names`可以设置多个队列的名称，用逗号分割。

另外，还可以使用`queues`属性来引用一个或多个（用逗号分割）使用`<queue>`元素声明要监听的队列Bean。

```xml
<listener-container connection-factory="connectionFactory">
  <listener ref="spittleListener"
            method="handleSpittleAlert"
            queues="spittleAlertQueue" />
</listener-container>

<queue id="spittleAlertQueue" name="spittle.alert.queue" />
```

# WebSocket

JMS和AMQP是用于应用程序之间的通讯，而如果某个应用是运行在Web浏览器中，则就需要使用WebSocket。

WebSocket协议提供了通过一个套接字实现全双工通信（服务器和浏览器可以互相发送消息）的功能，位于WebSocket一端的应用发送消息，另一端处理消息。因为它是全双工的，所以每一端都可以发送和处理消息。

![WebSocket](SpringMessage/WebSocket.png)

WebSocket通信可以应用于任何类型的应用中，但是它最常见的应用场景是实现Web浏览器和服务器之间的异步通信。浏览器中的JavaScript客户端开启一个到服务器的连接，服务器通过这个连接发送更新给浏览器。相比历史上轮询服务端以查找更新的方案，这种技术更加高效和自然。

## 使用Spring的低层级WebSocket API

### 创建消息处理器

WebSocket消息处理器是一个实现了`WebSocketHandler`接口的类。

`WebSocketHandler`接口：

```java
public interface WebSocketHandler {
  void afterConnectionEstablished(WebSocketSession session) 
    throws Exception;
  void handleMessage(WebSocketSession session, WebSocketMessage<?> message) 
    throws Exception;
  void handleTransportError(WebSocketSession session, Throwable exception) 
    throws Exception;
  void afterConnectionClosed(WebSocketSession session, CloseStatus closeStatus) 
    throws Exception;
  boolean supportsPartialMessages();
}
```

通常，创建消息处理器更简便的方法是扩展`AbstractWebSocketHandler`抽象类：

```java
public class MarcoHandler extends AbstractWebSocketHandler {
  private static final Logger logger = LoggerFactory.getLogger(MarcoHandler.class);
  protected void handleTextMessage(WebSocketSession session, TextMessage message) 
    	throws Exception {
    logger.info("Received message: " + message.getPayload());
    Thread.sleep(2000); //模拟延时
    session.sendMessage(new TextMessage("Polo!")); //发送文本消息
  }
}
```

`AbstractWebSocketHandler`额外定义了三个方法：

- handleBinaryMessage()
- handlePongMessage()
- handleTextMessage()

这三个方法只是`handleMessage()`的具体化，每个方法对应于某一种特定类型的消息。

如果只是处理文本消息，则可扩展`TextWebSocketHandler`类。

### 启用WebSocket

启用WebSocket并注册消息处理器：

```java
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {
  @Override
  public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
    registry.addHandler(marcoHandler(), "/marco"); //将MarcoHandler映射到“/marco”
  }
  @Bean
  public MarcoHandler marcoHandler() {
    return new MarcoHandler();
  }
}
```

或者：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:websocket="http://www.springframework.org/schema/websocket"
       xsi:schemaLocation="
         http://www.springframework.org/schema/websocket
         http://www.springframework.org/schema/websocket/spring-websocket.xsd
         http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd">
  <websocket:handlers>
    <websocket:mapping handler="marcoHandler" path="/marco" />
  </websocket:handlers>
  <bean id="marcoHandler"
        class="marcopolo.MarcoHandler" />
</beans>
```

### JavaScript客户端

```javascript
var url = 'ws://' + window.location.host + '/websocket/marco';
var sock = new WebSocket(url); //打开WebSocket
sock.onopen = function() { //处理连接开启事件
  console.log('Opening');
  sayMarco();
};
sock.onmessage = function(e) { //处理消息
  console.log('Received message: ', e.data);
  setTimeout(function(){sayMarco()}, 2000);
};
sock.onclose = function() { //处理连接关闭事件
  console.log('Closing');
};
function sayMarco() {
  console.log('Sending Marco!');
  sock.send("Marco!"); //发送消息
}
```

URL使用了`ws://`前缀，表明这是一个基本的WebSocket连接。如果是安全WebSocket，则使用`wss://`前缀。

### 关闭WebSocket连接

下列三种场景均可关闭WebSocket连接：

- 客户端调用`sock.close()`；
- 服务器端调用`session.close()`；
- 浏览器转向其他页面。

## 应对不支持WebSocket的场景——SockJS

当前不支持WebSocket场景主要由下列三种原因造成：

- 某些浏览器不支持WebSocket；
- 某些应用服务器不支持WebSocket；
- 防火墙限制了WebSocket通信。

SockJS是WebSocket技术的一种模拟，在表面上，它尽可能对应WebSocket API，但是在底层它非常智能。SockJS会优先选用WebSocket，但是如果WebSocket不可用，则它会从如下方案中挑选最优的可行方案：

- XHR流
- XDR流
- iFrame事件源
- iFrame HTML文件
- XHR轮询
- XDR轮询
- iFrame XHR轮询
- JSONP轮询

### 在服务端启用SockJS

```java
@Override
public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
	registry.addHandler(marcoHandler(), "/marco").withSockJS();
}
```

通过简单地调用`withSockJS`方法，就能声明我们想要使用的SockJS功能，如果WebSocket不可用，则SockJS的备用方案就会发挥作用。

或者使用XML来配置：

```xml
<websocket:handlers>
  <websocket:mapping handler="marcoHandler" path="/marco" />
  <websocket:sockjs />
</websocket:handlers>
```

### 在客户端启用SockJS

#### 加载SockJS客户端库

```html
<script src="http://cdn.sockjs.org/sockjs-0.3.min.js"></script>
```

#### 编写客户端代码

只需要在原来基于WebSocket API的代码上修改两行代码就可以使用SockJS：

```javascript
var url = 'marco';
var sock = new SockJS(url);
```

第一个修改就是URL。SockJS所处理的URL是`http://`或`https://`模式，而不是`ws://`或`wss://`。而且可以使用相对URL，如果包含JavaScript的页面位于http://localhost:8080/websocket ，则本例给定的相对路径`marco`将对应于http://localhost:8080/websocket/marco。

第二个修改是创建SockJS实例代替WebSocket。

## 使用STOMP消息

直接使用WebSocket（或SockJS）就类似于使用TCP套接字来编写Web应用，显得过于低级。因为没有高层级的线路协议（wire protocol），因此就需要我们定义应用之间所发送消息的语义，还需要确保连接的两端都能遵循这些语义。

STOMP在WebSocket之上提供了一个基于帧的线路格式（frame-based wire format）层，用来定义消息的语义。

STOMP帧由命令、一个或多个头信息以及负载所组成，非常类似于HTTP请求结构：

```
SEND
destination:/app/marco
content-length:20

{\"message\":\"Marco!\"}
```

Spring为STOMP消息提供了基于Spring MVC的编程模型，在Spring MVC控制器中处理STOMP消息与处理HTTP请求并没有太大的差别。

### 启用STOMP消息功能

```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketStompConfig extends AbstractWebSocketMessageBrokerConfigurer {
  @Override
  public void registerStompEndpoints(StompEndpointRegistry registry) {
    //将“/marcopolo”注册为STOMP端点，客户端在订阅或发布消息到目的地路径前，要连接该端点。
    registry.addEndpoint("/marcopolo").withSockJS();
  }
  @Override
  public void configureMessageBroker(MessageBrokerRegistry registry) {
    //使用内置消息代理进行订阅和广播，并将destination头以“/topic”或“/queue”开头的消息路由到代理。
    registry.enableSimpleBroker("/queue", "/topic");
    //destination头以“/app”为前缀的STOMP消息将路由到@Controller类中的@MessageMapping方法。
    registry.setApplicationDestinationPrefixes("/app");
  }
}
```

![STOMP代理](SpringMessage/STOMP-broker.png)

如果没有重写`configureMessageBroker`方法，则会自动配置一个简单的内存消息代理，用它来处理以`/topic`前缀的消息。

#### 启用STOMP代理中继

简单的代理是基于内存的，尽管它模拟了STOMP消息代理，但是它只支持STOMP命令的子集，并且它不适合集群环境。在生产环境中，我们会使用真正支持STOMP的代理（例如RabbitMQ或ActiveMQ）来支撑WebSocket消息。

![STOMP代理中继](SpringMessage/STOMP-broker-relay.png)

## 为目标用户发送消息

## 处理消息异常

# Email