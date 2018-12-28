---
title: SpringJMX
date: 2018-12-27 13:57:18
tags:
---

JMX能够让我们管理、监视和配置应用——甚至在应用的运行期间。

JMX管理应用的核心组件是MBean（Managed Bean）。所谓的MBean就是暴露特定方法的JavaBean，这些方法定义了管理接口。JMX规范定义了如下4种类型的MBean：

- 标准MBean：标准MBean的管理接口是通过在固定的接口上执行反射确定的，Bean类会实现这个接口；
- 动态MBean：动态MBean的管理接口是在运行时通过调用`DynamicMBean`接口的方法来确定的。因为管理接口不是通过静态接口定义的，因此可以在运行时改变；
- 开发MBean：开发MBean是一种特殊的动态MBean，其属性和方法只限定于原始类型、原始类型的包装类以及可以分解为原始类型或原始类型包装类的任意类型；
- 模型MBean：模型MBean也是一种特殊的动态MBean，用于充当管理接口与受管资源的中介。模型MBean并不像它们所声明的那样来编写。它们通常通过工厂生成，工厂会使用元信息来组装管理接口。

# 将Spring Bean导出为MBean

Spring的`MBeanExporter`可以把一个或多个Spring Bean导出为MBean服务器（MBean Server）内的模型MBean。MBean服务器（也称为MBean代理）是MBean生存的容器，对MBean的访问，是通过MBean服务器来实现的。

![MBeanExporter](SpringJMX/MBeanExporter.png)

将Spring Bean导出为JMX MBean之后，可以使用基于JMX的管理工具（例如JConsole或VisualVM）查看正在运行的应用程序，显示Bean的属性并调用Bean的方法。

下面注册了一个`MBeanExporter`，它会将`spittleController` Bean导出为一个模型MBean：

```java
@Bean
public MBeanExporter mbeanExporter(SpittleController spittleController) {
  MBeanExporter exporter = new MBeanExporter();
  Map<String, Object> beans = new HashMap<String, Object>();
  beans.put("spitter:name=SpittleController", spittleController);
  exporter.setBeans(beans);
  return exporter;
}
```

每个`Map`条目的key就是MBean的名称（由管理域名字和一个key-value对组成）。

> 根据以上配置，`MBeanExporter`会假设它正在一个应用服务器（例如Tomcat）或提供MBean服务器的其他上下文中运行。但是，如果Spring应用程序是独立的应用或运行的容器没有提供MBean服务器，我们就需要在Spring上下文中配置一个MBean服务器。
>
> 在XML配置中，`<context:mbean-server>`元素可以为我们实现该功能。如果使用Java配置的话，就是直接配置类型为`MBeanServerFactoryBean`的Bean。
>
> `MBeanServerFactoryBean`会创建一个MBean服务器，并将其作为Spring应用上下文中的Bean。默认情况下，这个Bean的ID是`mbeanServer`。了解到这一点，我们就可以将它装配到`MBeanExporter`的`server`属性中用来指定MBean要暴露到哪个MBean服务器中。

SpittleController.java：

```java
@Controller
@RequestMapping("/spittles")
public class SpittleController {
  public static final int DEFAULT_SPITTLES_PER_PAGE = 25;
  private int spittlesPerPage = DEFAULT_SPITTLES_PER_PAGE;
  private SpittleRepository spittleRepository;
  
  public void setSpittlesPerPage(int spittlesPerPage) {
    this.spittlesPerPage = spittlesPerPage;
  }
  public int getSpittlesPerPage() {
    return spittlesPerPage;
  }
  
  @Autowired
  public SpittleController(SpittleRepository spittleRepository) {
    this.spittleRepository = spittleRepository;
  }
  
  @RequestMapping(method=RequestMethod.GET)
  public String spittles(Model model) {
    model.addAttribute(spittleRepository.findSpittles(Long.MAX_VALUE, 20));
    return "spittles";
  }
}
```

默认情况下，`SpittleController`所有的public成员都将被导出为MBean的操作或属性。为了对MBean的属性和操作获得更细粒度的控制，Spring提供了几种选择：

- 通过名称来声明需要暴露或忽略的Bean方法；
- 通过为Bean增加接口来选择要暴露的方法；
- 通过标注来标识托管的属性和方法。

> 要暴露属性，只需要暴露它的存取器方法

## 通过名称暴露方法

`MethodNameBasedMBeanInfoAssembler` MBean信息装配器有一个`managedMethods`属性可以接受一个方法名称列表，用于指定哪些方法将暴露为MBean的操作。

```java
@Bean
public MethodNameBasedMBeanInfoAssembler assembler() {
  MethodNameBasedMBeanInfoAssembler assembler =
    new MethodNameBasedMBeanInfoAssembler();
  assembler.setManagedMethods(new String[] {
    "getSpittlesPerPage", "setSpittlesPerPage"
  });
  return assembler;
}
```

本示例所暴露的是`spittlesPerPage`属性的存取器方法，也就是将`spittlesPerPage`属性暴露为MBean的托管属性。而`spittles`方法则不会暴露为MBean的操作。

这个装配器只是告诉哪些方法要暴露为MBean的操作，但没有具体指定是哪些Bean的方法。因此，我们还需要将它装配进`MBeanExporter`中：

```java
@Bean
public MBeanExporter mbeanExporter(SpittleController spittleController,
                                   MBeanInfoAssembler assembler) {
  MBeanExporter exporter = new MBeanExporter();
  Map<String, Object> beans = new HashMap<String, Object>();
  beans.put("spitter:name=SpittleController", spittleController);
  exporter.setBeans(beans);
  exporter.setAssembler(assembler);
  return exporter;
}
```

另一个基于方法名称的装配器是`MethodExclusionMBeanInfoAssembler`，它是`MethodNameBasedMBeanInfoAssembler`的反操作，它指定不需要暴露为MBean托管操作的方法名称列表：

```java
@Bean
public MethodExclusionMBeanInfoAssembler assembler() {
  MethodExclusionMBeanInfoAssembler assembler =
    new MethodExclusionMBeanInfoAssembler();
  assembler.setIgnoredMethods(new String[] {
    "spittles"
  });
  return assembler;
}
```

## 使用接口定义MBean的操作和属性

基于方法名称的装配器虽然简单、直接，但如果需要把多个Spring Bean导出为MBean，则为装配器所配置的方法名称列表将会变得非常庞大；而且，如果希望暴露一个Bean的某个方法，但不希望暴露另一个Bean的同名方法，基于方法名称的方式将不能很好满足需要。这时，使用`InterfaceBasedMBeanInfoAssembler`，通过使用接口来选择Bean的哪些方法需要暴露为MBean的操作和属性将更合适。

假设我们定义了如下接口：

```java
public interface SpittleControllerManagedOperations {
  int getSpittlesPerPage();
  void setSpittlesPerPage(int spittlesPerPage);
}
```

这里我们选择暴露`getSpittlesPerPage`方法和`setSpittlesPerPage`方法。

然后，使用如下的装配器替换之前基于方法名称的装配器：

```java
@Bean
public InterfaceBasedMBeanInfoAssembler assembler() {
  InterfaceBasedMBeanInfoAssembler assembler =
    new InterfaceBasedMBeanInfoAssembler();
  assembler.setManagedInterfaces(
    new Class<?>[] { SpittleControllerManagedOperations.class }
  );
  return assembler;
}
```

需要注意的是：`SpittleController`并不需要显式实现`SpittleControllerManagedOperations`接口。这个接口只是为了标识导出的内容，我们并不需要在代码中直接实现该接口。但是，为了MBean与实现类之间有一个一致的协议，建议实现这个接口。

## 使用标注驱动的MBean

`MetadataMBeanInfoAssembler`装配器可以使用标注标识哪些Bean的方法需要暴露为MBean的托管操作和属性。但是，直接使用这种装配器手工装配非常繁杂。相反，我们应该使用Spring Context配置命名空间中的`<context:mbean-export>`元素，它装配了MBean导出器以及为了在Spring启用标注驱动的MBean所需要的装配器。我们要做的就是使用它来替换之前所使用的`MBeanExporter` Bean：

```xml
<context:mbean-export server="mbeanServer" />
```

现在，要把任意Spring Bean转变为MBean，只需要使用`@ManagedResource`标注Bean，并使用`@ManagedOperation`或`@ManagedAttribute`标注需要暴露为MBean操作的Bean方法：

```java
@Controller
@ManagedResource(objectName="spitter:name=SpittleController") //将SpittleController导出为MBean
public class SpittleController {
  ...
  @ManagedAttribute //将spittlesPerPage暴露为托管属性
  public void setSpittlesPerPage(int spittlesPerPage) {
    this.spittlesPerPage = spittlesPerPage;
  }
  @ManagedAttribute //将spittlesPerPage暴露为托管属性
  public int getSpittlesPerPage() {
    return spittlesPerPage;
  }
}
```

`objectName`属性标识了域（`spitter`）和MBean的名称（`SpittleController`）。

如果托管属性是只读或只写的，只需要标注两个存取器方法中的一个。

我们还可以使用`@ManagedOperation`标注替换`@ManagedAttribute`标注来标注存取器方法：

```java
@ManagedOperation
public void setSpittlesPerPage(int spittlesPerPage) {
	this.spittlesPerPage = spittlesPerPage;
}
@ManagedOperation
public int getSpittlesPerPage() {
	return spittlesPerPage;
}
```

这会将方法暴露为MBean的托管操作，但是并不会把`spittlesPerPage`属性暴露为MBean的托管属性。

## 处理MBean冲突

MBean指定的对象名称是由管理域名和key-value对组成的，如果MBean服务器中不存在与我们MBean名字相同的已注册MBean，则我们的MBean注册时就不会有问题。但是如果名字冲突，默认情况下，`MBeanExporter`将抛出`InstanceAlreadyExistsException`异常。

我们可以通过`MBeanExporter`的`registrationBehaviorName`属性或者`<context:mbean-export>`的`registration`属性指定冲突处理机制来改变默认行为。

Spring提供了3种处理MBean名字冲突的机制：

- `FAIL_ON_EXISTING`：如果已存在同名的MBean，则失败（默认行为）；
- `IGNORE_EXISTING`：忽略冲突，同时也不注册新的MBean；
- `REPLACTING_EXISTING`：用新的MBean覆盖已存在的MBean。

```java
@Bean
public MBeanExporter mbeanExporter(SpittleController spittleController,
                                   MBeanInfoAssembler assembler) {
  MBeanExporter exporter = new MBeanExporter();
  Map<String, Object> beans = new HashMap<String, Object>();
  beans.put("spitter:name=SpittleController", spittleController);
  exporter.setBeans(beans);
  exporter.setAssembler(assembler);
  exporter.setRegistrationPolicy(RegistrationPolicy.IGNORE_EXISTING);
  return exporter;
}
```

