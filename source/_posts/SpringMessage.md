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

# WebSocket

# STOMP

# Email