---
title: Kubernetes
date: 2018-07-03 08:21:46
tags: [1.11]
---

# 简介

Kubernetes是一个容器编排引擎，它是Google Omega（之前叫Borg）的开源版本。

# 入门

[Kubernetes.io](https://kubernetes.io) 上有一个基于Minikube的 [在线交互式教程](https://kubernetes.io/docs/tutorials/kubernetes-basics/) ，可以快速体验Kubernetes的功能和应用场景。

## 创建集群

```bash
$ minikube start
```

查看集群信息：

```bash
$ kubectl cluster-info
```

查看集群中节点：

```bash
$ kubectl get nodes
```

> 注意：当前执行命令的地方并不是集群中的节点，我们是通过Kubernetes的命令行工具kubectl远程管理集群（Kubectl使用Kubernetes API与集群进行交互）。可以使用命令`hostname`查看当前执行命令的主机。
>
> kubectl命令的通用格式是：`kubectl action resource`。它对指定的资源（如node，container）执行指定的操作（如create，describe）。 
>
> 可在kubctl后带上`--help`选项，以获取相应帮助。例如，`kubectl get nodes --help`。

## 部署应用

一旦运行了Kubernetes集群，就可以在其上部署容器化应用程序。为此，您需要创建Kubernetes Deployment配置。

 Deployment指示Kubernetes如何创建和更新应用程序的实例。创建Deployment后，Kubernetes主调度将应用程序实例提到集群中的各个节点上。 

创建应用程序实例后，Kubernetes Deployment Controller会持续监视这些实例。如果托管实例的节点关闭或被删除，则Deployment控制器会替换它。 这提供了一种自我修复机制来解决机器故障或维护问题。 

![deployment](Kubernetes/deployment.svg)

部署一个名为`kubernetes-bootcamp`的应用：

```bash
$ kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port:8080
```

`--image`指定应用的容器镜像。

`--port`指定应用提供服务的端口。

这条命令执行了如下操作：

- 搜索可以运行应用程序实例的合适节点（这里只有1个可用节点）。
- 安排应用程序在该节点上运行。
- 配置集群以在需要时重新安排实例到新节点上。

查看集群上的Deployments：

```bash
$ kubectl get deployments
```

## 访问应用

部署到Kubernetes集群的应用，将运行在一个私有、隔离的网络上。它们只能在集群内部可见，要从集群外部访问这些应用，需要将容器的端口映射到节点的端口。

```bash
$ kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
```

上面的命令创建了一个service，将容器的8080端口映射到`minikube`节点的随机分配端口（例如，30670）。

可以通过下面命令查看集群中的services：

```bash
$ kubectl get services
```

要访问这个服务，只要：

```bash
$ curl minikube:30670
```

可以通过下列命令来获取NodePort：

```bash
$ export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
```

## 扩展应用

默认情况下，部署的应用只会运行一个副本，可以通过`kubectl get deployments`来查看副本数。

执行下列命令将副本数增加到4个：

```bash
$ kubectl scale deployments/kubernetes-bootcamp --replicas=4
```

扩展前：

![scaling1](Kubernetes/scaling1.svg)

扩展后：

![scaling2](Kubernetes/scaling2.svg)

> 注意：每个Pod的集群IP都不一样。

Kubernetes还支持Pod的自动缩放。缩放到零也是可能的，它将终止指定部署的所有Pod。 

通过`curl minikube:30670`访问应用，可以看到每次请求发送到不同的pod。4个副本（pod）轮询处理，这样就实现了负载均衡。

服务集成有负载平衡器，可将网络流量分配到公开部署的所有Pod副本。服务将使用端点连续监视正在运行的Pod，以确保流量仅发送到可用的Pod。 

要缩减服务的副本数到2，只需再次运行scale命令：

```bash
$ kubectl scale deployments/kubernetes-bootcamp --replicas=2
```

可以看到有两个副本被删除。

## 滚动更新

滚动更新允许通过使用新的Pods实例逐步更新旧的Pods实例来实现部署的更新，而无需停机。新的Pod将在具有可用资源的节点上进行调度。 

滚动更新之前：

![rollingupdates1](Kubernetes/rollingupdates1.svg)

使用下列命令将应用的镜像版本从`v1`升级到`v2`：

```bash
$ kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
```

在更新期间，服务只会将网络流量负载均衡到可用的Pod上。 

![rollingupdates2](Kubernetes/rollingupdates2.svg)

![rollingupdates3](Kubernetes/rollingupdates3.svg)

![rollingupdates4](Kubernetes/rollingupdates4.svg)

如果在滚动更新过程中，出现错误，要回滚到更新之前的状态，可以执行：

```bash
$ kubectl rollout undo deployments/kubernetes-bootcamp
```

更新已进行版本控制，您可以恢复到之前已知的任何部署状态。 

# 架构

Kubernetes用于协调一个高可用的计算机集群，这些计算机连接起来作为一个单元工作。 

## Kubernetes集群

Kubernetes集群包含两种类型的资源： 

- **Master**节点负责管理集群。 例如，调度应用程序，维护应用程序的所需状态，扩展应用程序以及推出新更新。 
- **Node**节点是运行容器应用的工作器，它对应一个虚拟机或物理机（具体取决于集群）。每个Node都有一个Kubelet，它是一个管理Node并与Kubernetes Master通信的代理。Node还应具有用于处理容器操作的工具，例如Docker或rkt。 

Kubernetes集群可以部署在虚拟机或物理机上。一个应对生产流量的Kubernetes集群应至少有三个节点。

Node使用Master公开的Kubernetes API与Master进行通信。终端用户还可以直接使用Kubernetes API与群集进行交互。 

![Kubernetes-cluster](Kubernetes/Kubernetes-cluster.svg)

Node可以包含多个pod，Kubernetes master会自动处理在群集中的节点上调度pod。

![nodes](Kubernetes/nodes.svg) 

## Pods

当我们在Kubernetes上创建一个Deployment时，Kubernetes会创建一个Pod（包含容器）来托管你的应用程序实例，而不是直接创建容器。 

Pod是一个Kubernetes抽象，表示紧密相关的一组（一个或多个）应用程序容器（如Docker或rkt），以及这些容器的一些共享资源。 这些资源包括：

- 共享存储——Kubernetes中存储卷是在Pod级别中设置的，而不是容器级别。 
- 网络——共享Pod的集群IP地址（ClusterIP） 和端口空间，Kubernetes集群中的每个Pod都有一个唯一的集群IP地址。而容器之间能直接通过localhost来发现和通信。 
- 有关如何运行每个容器的信息，例如容器映像版本或要使用的特定端口 

Pod模拟一个特定于应用程序的“逻辑主机”，并且可以包含相对紧密耦合的不同应用程序容器。 

> 只有需要直接共享资源的容器才应该放在一个Pod中。而象Tomcat和MySQL，它们是通过JDBC交换数据，而不是直接共享存储，因此不应该放在同一个Pod中。

Pod是Kubernetes的最小调度单位，同一Pod中的容器始终作为一个整体被Master调度到一个Node上运行，并在同一节点的共享上下文中运行。 

每个Pod都与调度它的节点绑定，并保持在那里直到终止（根据重启策略）或删除。如果节点发生故障，则会在群集中的其他可用节点上安排相同的Pod。 

![pods](Kubernetes/pods.svg)

可用下列命令获取Pods的名称：

```bash
$ export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
```

## 控制器——运行Pod

Kubernetes通常不会直接创建Pods，而是通过控制器（Controller）来管理Pod。

为了满足不同业务场景，Kubernetes提供了多种控制器：

- Deployment：可管理Pod的多个副本，并确保Pod按照预期的状态运行。
- ReplicaSet：实现了Pod的多副本管理。实际上，使用Deployment时会自动创建ReplicaSet，也就是说，Deployment是通过ReplicaSet来管理Pod的多个副本。我们通常不需要直接使用ReplicaSet。
- DaemonSet：用于每个Node最多运行一个Pod副本的场景。
- StatefuleSet：能够保证Pod的每个副本在整个生命周期中名称是不变的，并且保证副本按照固定的顺序启动、更新或删除。
- Job：用到运行结束就删除的应用，常用于一次性的任务。其他控制中的Pod通常是长期持续运行，除非手动删除。

## 服务——访问Pod

在K8s中Pod的实例是动态变化的，IP也不是固定的。这就给访问者带来了困难，而服务就是为了解决这个困难的。

服务是实际应用的抽象，它定义了一组逻辑的Pod集合和访问这个Pod集合的策略。服务降低了Pods之间的耦合度。服务使用标签和选择器匹配一组Pods。

服务将代理Pod对外表现为一个单一访问接口，外部不需要了解后端Pod如何运行，提供了一套简化的负载均衡、服务代理和发现机制。

与Pods一样，每个服务也有自己在集群中的唯一集群IP地址。

![services](Kubernetes/services.svg)

在ServiceSpec中，通过`type`来指定服务公布的方式：

- ClusterIP（默认）：在群集中的内部IP上公开服务。此类型使服务只能从群集中访问。 
- NodePort：使用NAT方式，在集群中每个选定节点（由LabelSelector确定）的**同一端口**上公开服务。在集群外部，通过`<NodeIP>:<NodePort>` 访问服务。ClusterIP的超集。 
- LoadBalancer：在当前云中创建外部负载均衡器（如果支持），并为服务分配固定的外部IP。 NodePort的超集。（minikube不支持）
- ExternalName：通过返回带有名称的CNAME记录，使用任意名称（在规范中由externalName指定）公开服务。没有使用代理。此类型需要v1.7或更高版本的kube-dns。 

## 标签

标签是附加到对象的键/值对，它是一个允许对Kubernetes中的对象进行逻辑操作的分组原语。 

![labels](Kubernetes/labels.svg)

标签可以在创建时或稍后附加到对象。它们可以随时修改。 

## 命名空间

命名空间可以将一个物理的集群逻辑地划分成多个虚拟集群。不同命名空间里的资源是完全隔离的。

Kubernetes默认创建了两个命名空间：

- default：默认命名空间。创建资源时如果不指定命名空间，将被放到default命名空间。
- kube-system：Kubernetes自己创建的系统资源将放到这个命名空间。

# 部署

## Minikube

Minikube是一种轻量级Kubernetes实现，可在本地计算机上创建VM并部署仅包含一个节点的简单集群，它适用于开发和测试。 

显示Minikube版本号：

```bash
$ minikube version
```
