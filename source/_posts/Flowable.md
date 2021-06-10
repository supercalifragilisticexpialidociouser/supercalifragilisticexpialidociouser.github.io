---
title: Flowable
date: 2021-05-27 09:52:06
tags: [6.6.0]
---

# 入门

有两种方式来使用Flowable工作流引擎：

- 内嵌式：直接将Flowable的JAR添加为应用的依赖。这种方式适合需要高度可定制的场景。
- REST式：将Flowable的WAR部署成一个独立应用，然后自已的应用通过HTTP使用Flowable REST API来与Flowable工作流引擎通信。这种方式几乎没有什么耦合，工作量少。另外，Flowable还提供了一些开箱即用的应用（Flowable Modeler、Flowable Admin、Flowable IDM 和 Flowable Task）。（推荐）

## 创建项目

Flowable既可以在普通的Java SE项目中使用，也可以在Spring项目（包括Spring Boot）中使用。

采用内嵌式的，需要在项目中导入相应依赖，并在配置文件中做相应配置。而采用独立应用式的，则不需要导入任何Flowable依赖，直接通过HTTP访问Flowable REST API。

在不集成Spring Boot下，需要添加如下依赖：

```xml
<dependency>
   <groupId>org.flowable</groupId>
   <artifactId>flowable-engine</artifactId>
   <version>6.6.0</version>
</dependency>
<dependency>
   <groupId>com.h2database</groupId>
   <artifactId>h2</artifactId>
   <version>1.4.200</version>
</dependency>
<dependency>
   <groupId>org.slf4j</groupId>
   <artifactId>slf4j-api</artifactId>
   <version>1.7.30</version>
</dependency>
<dependency>
   <groupId>org.slf4j</groupId>
   <artifactId>slf4j-log4j12</artifactId>
   <version>1.7.30</version>
</dependency>
```

并添加一个Log4j配置文件*log4j.properties*到类路径。

在集成Spring Boot下，需要添加如下依赖：

```xml
<dependency>
   <groupId>org.flowable</groupId>
   <artifactId>flowable-spring-boot-starter</artifactId>
   <version>${flowable.version}</version>
</dependency>
```

或者：

```xml
<dependency>
   <groupId>org.flowable</groupId>
   <artifactId>flowable-spring-boot-starter-rest</artifactId>
   <version>${flowable.version}</version>
</dependency>
```



## 创建流程引擎

流程引擎是一个线程安全的对象，您通常只需在应用程序中实例化一次。

### 内嵌式

#### 非Spring环境

Flowable本身使用了Spring，但它可以在没有Spring的情况下被使用。

在非Spring环境中使用Flowable时，流程引擎的配置部分在的XML配置文件中定义（默认为*flowable.cfg.xml*，位于类路径中）。该配置文件实际上是一个Spring配置，它必须包含一个id为“processEngineConfiguration”的bean。

有多个类可用于定义 processEngineConfiguration。这些类代表不同的环境，并相应地设置默认值：

- **org.flowable.engine.impl.cfg.StandaloneProcessEngineConfiguration**：在非Spring环境中使用。Flowable 将处理所有交易。默认情况下，只会在引擎启动时检查数据库（如果没有 Flowable schema 或 schema 版本不正确，则会抛出异常）。
- **org.flowable.engine.impl.cfg.StandaloneInMemProcessEngineConfiguration**：这是一个用于单元测试目的的便利类，在非Spring环境中使用。Flowable 将处理所有交易。默认使用 H2 内存数据库。当引擎启动和关闭时，将创建和删除数据库。
- **org.flowable.spring.SpringProcessEngineConfiguration**：在 Spring 环境中使用流程引擎时使用。
- **org.flowable.engine.impl.cfg.JtaProcessEngineConfiguration**：在非Spring环境中使用，带有 JTA 事务。

在非Spring环境中，流程引擎本身则通过编程方式获得。

最简单的创建流程引擎的方式是：`ProcessEngines.getDefaultProcessEngine()`（使用*flowable.cfg.xml* 配置文件来配置流程引擎）。

如果需要更高的可定制，则可以使用`ProcessEngineConfiguration`，以编程方式来构建流程引擎：

```java
ProcessEngineConfiguration cfg = 
   ProcessEngineConfiguration.createStandaloneProcessEngineConfiguration()
      .setJdbcUrl("jdbc:h2:mem:flowable;DB_CLOSE_DELAY=-1")
      .setJdbcUsername("sa")
      .setJdbcPassword("")
      .setJdbcDriver("org.h2.Driver")
      .setDatabaseSchemaUpdate(ProcessEngineConfiguration.DB_SCHEMA_UPDATE_TRUE);

ProcessEngine processEngine = cfg.buildProcessEngine();
```

`ProcessEngineConfiguration`的构建流程引擎方法：

```java
//基于配置文件（配置文件位于类路径中）
createProcessEngineConfigurationFromResourceDefault();
createProcessEngineConfigurationFromResource(String resource);
createProcessEngineConfigurationFromResource(String resource, String beanName);
createProcessEngineConfigurationFromInputStream(InputStream inputStream);
createProcessEngineConfigurationFromInputStream(InputStream inputStream, String beanName);

//不使用配置文件，基于默认值
ProcessEngineConfiguration.createStandaloneProcessEngineConfiguration();
ProcessEngineConfiguration.createStandaloneInMemProcessEngineConfiguration(); //使用H2数据库，且是内存模式：jdbc:h2:mem:flowable
```

#### Spring环境

在Spring环境中使用Flowable时，流程引擎的配置部分和流程引擎本身都配置为普通的Spring bean。下面以XML方式配置为例：

```xml
<bean id="processEngineConfiguration" class="org.flowable.spring.SpringProcessEngineConfiguration">
   ...
</bean>

<bean id="processEngine" class="org.flowable.spring.ProcessEngineFactoryBean">
   <property name="processEngineConfiguration" ref="processEngineConfiguration" />
</bean>
```

请注意， *processEngineConfiguration* bean 现在使用 **org.flowable.spring.SpringProcessEngineConfiguration** 类。



### REST式

在启动flowable-rest应用时，自动创建流程引擎实例。

## 流程定义和部署

### 流程定义

Flowable 引擎期望以 BPMN 2.0 格式定义流程，这是一种被业界广泛接受的 XML 标准。

holiday-request.bpmn20.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:xsd="http://www.w3.org/2001/XMLSchema"
             xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI"
             xmlns:omgdc="http://www.omg.org/spec/DD/20100524/DC"
             xmlns:omgdi="http://www.omg.org/spec/DD/20100524/DI"
             xmlns:flowable="http://flowable.org/bpmn"
             typeLanguage="http://www.w3.org/2001/XMLSchema"
             expressionLanguage="http://www.w3.org/1999/XPath"
             targetNamespace="http://www.flowable.org/processdef">

   <process id="holidayRequest" name="Holiday Request" isExecutable="true">

      <startEvent id="startEvent"/>
      <sequenceFlow sourceRef="startEvent" targetRef="approveTask"/>

      <userTask id="approveTask" name="Approve or reject request"/>
      <sequenceFlow sourceRef="approveTask" targetRef="decision"/>

      <exclusiveGateway id="decision"/>
      <sequenceFlow sourceRef="decision" targetRef="externalSystemCall">
         <conditionExpression xsi:type="tFormalExpression">
            <![CDATA[
          ${approved}
        ]]>
         </conditionExpression>
      </sequenceFlow>
      <sequenceFlow  sourceRef="decision" targetRef="sendRejectionMail">
         <conditionExpression xsi:type="tFormalExpression">
            <![CDATA[
          ${!approved}
        ]]>
         </conditionExpression>
      </sequenceFlow>

      <serviceTask id="externalSystemCall" name="Enter holidays in external system"
                   flowable:class="org.flowable.CallExternalSystemDelegate"/>
      <sequenceFlow sourceRef="externalSystemCall" targetRef="holidayApprovedTask"/>

      <userTask id="holidayApprovedTask" name="Holiday approved"/>
      <sequenceFlow sourceRef="holidayApprovedTask" targetRef="approveEnd"/>

      <serviceTask id="sendRejectionMail" name="Send out rejection email"
                   flowable:class="org.flowable.SendRejectionMail"/>
      <sequenceFlow sourceRef="sendRejectionMail" targetRef="rejectEnd"/>

      <endEvent id="approveEnd"/>

      <endEvent id="rejectEnd"/>

   </process>

</definitions>
```

*流程定义*除了直接手工编写XML文件外，也可使用可视化建模工具建模的，例如 Flowable Designer (Eclipse) 或 Flowable Modeler（Web 应用程序）。

### 部署

*部署*流程定义意味着：

- 流程引擎会将 XML 文件存储在数据库中，以便在需要时随时检索；
- 流程定义被解析为一个内部的、可执行的对象模型，以便*流程实例*可以从它启动。

内嵌式：

```java
RepositoryService repositoryService = processEngine.getRepositoryService();
Deployment deployment = repositoryService.createDeployment()
   .addClasspathResource("holiday-request.bpmn20.xml")
   .deploy();
```

在集成Spring Boot下，在类路径下的*processes*目录中的任何BPMN 2.0流程定义、*cases*目录中任何CMMN 1.1 定义、*dmn*目录下任何DMN 1.1定义、*forms*目录中任何表单定义都将被自动部署。

REST式：

```bash
$ curl --user admin:test -F "file=@holiday-request.bpmn20.xml" http://localhost:8080/flowable-ui/process-api/repository/deployments
```

## 启动流程实例

从*流程定义中*，可以启动许多**流程实例**。将*流程定义*视为*流程*多次执行的蓝图。在例子中，*流程定义*定义了请求假期所涉及的不同步骤，而一个*流程实例*与一位特定员工的假期请求相匹配。

### 内嵌式

```java
RuntimeService runtimeService = processEngine.getRuntimeService();
//流程变量（通常来自表单）
Map<String, Object> variables = new HashMap<String, Object>();
variables.put("employee", "Tom");
variables.put("nrOfHolidays", 7);
variables.put("description", "None");
ProcessInstance processInstance =
  runtimeService.startProcessInstanceByKey("holidayRequest", variables);
```

`startProcessInstanceByKey`的第一个参数对应BPMN 2.0 XML 文件中设置的*id*属性，例如：

`<process id="holidayRequest" name="Holiday Request" isExecutable="true">`。

### REST式

```bash
$ curl --user admin:test -H "Content-Type: application/json" -X POST -d '{ "processDefinitionKey":"holidayRequest", "variables": [ { "name":"employee", "value": "Tom" }, { "name":"nrOfHolidays", "value": 7 }]}' http://localhost:8080/flowable-ui/process-api/runtime/process-instances
```

## 完成任务

### 内嵌式

获取“managers”组的所有任务的列表：

```java
TaskService taskService = processEngine.getTaskService();
List<Task> tasks = askService.createTaskQuery().taskCandidateGroup("managers").list();
System.out.println("You have " + tasks.size() + " tasks:");
for (int i=0; i<tasks.size(); i++) {
   System.out.println((i+1) + ") " + tasks.get(i).getName());
}
```

完成任务：

```java
variables = new HashMap<String, Object>();
variables.put("approved", true);
taskService.complete(task.getId(), variables);
```

### REST式

获取“managers”组的所有任务的列表：

```bash
curl --user admin:test -H "Content-Type: application/json" -X POST -d '{ "candidateGroup" : "managers" }' http://localhost:8080/flowable-ui/process-api/query/tasks
```

完成任务：

```bash
curl --user admin:test -H "Content-Type: application/json" -X POST -d '{ "action" : "complete", "variables" : [ { "name" : "approved", "value" : true} ]  }' http://localhost:8080/flowable-ui/process-api/runtime/tasks/25
```

注意：需要将*CallExternalSystemDelegate*类打包成JAR，放到Tomcat的*webapps*文件夹下的*flowable -rest*文件夹的*WEB-INF/lib*文件夹中。（自已定制的类可以通过这种方式集成进Flowable Rest应用）

# 设计模式

## 门面模式

ProcessEngine
