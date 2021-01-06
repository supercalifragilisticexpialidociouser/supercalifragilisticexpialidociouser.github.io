---
ctitle: Kubernetes
date: 2018-07-03 08:21:46
tags: [1.20]
---

# 简介

Kubernetes是一个容器编排引擎，它是Google Omega（之前叫Borg）的开源版本。

<!--more-->

# 起步

[Kubernetes.io](https://kubernetes.io) 上有一个基于Minikube的 [在线交互式教程](https://kubernetes.io/docs/tutorials/kubernetes-basics/) ，可以快速体验Kubernetes的功能和应用场景。

## 创建集群

```bash
# 启动集群
$ minikube start

# 查看集群信息
$ kubectl cluster-info

# 查看集群中节点
$ kubectl get nodes

# 查看Kubernetes集群上部署的所有资源
$ kubectl get all --all-namespaces

# 在浏览器中打开Kubernetes仪表盘
$ minikube dashboard
```

> kubectl命令的通用格式：`kubectl 命令 资源类型 资源名`。
>
> 资源类型可以使用单数、复数、简写三种形式，下列命令都是等价的：
>
> ```bash
> $ kubectl get pods
> $ kubectl get pod
> $ kubectl get po
> ```
>
> 资源类型和资源名之间可以使用空格或“/”分隔，下列命令都是等价的：
>
> ```bash
> $ kubectl get pods foo
> $ kubectl get pods/foo
> $ kubectl get pod foo
> …
> ```
>
> 

## 部署应用

一旦运行了Kubernetes集群，就可以在其上部署容器化应用程序。为此，您需要创建Kubernetes Deployment配置。

 Deployment指示Kubernetes如何创建和更新应用程序的实例。创建Deployment后，Kubernetes主调度将应用程序实例提到集群中的各个节点上。 

创建应用程序实例后，Kubernetes Deployment Controller会持续监视这些实例。如果托管实例的节点关闭或被删除，则Deployment控制器会替换它。 这提供了一种自我修复机制来解决机器故障或维护问题。 

![deployment](Kubernetes/deployment.svg)

部署一个名为`hello-node`的应用：

```bash
kubectl create deployment hello-node --image=registry.cn-hangzhou.aliyuncs.com/google_containers/echoserver:1.4
```

`--image`指定应用的容器镜像。

这条命令执行了如下操作：

- 搜索可以运行应用程序实例的合适节点（这里只有1个可用节点）。
- 安排应用程序在该节点上运行。
- 配置集群以在需要时重新安排实例到新节点上。

查看集群上的Deployments：

```bash
$ kubectl get deployments
```

查看Pods：

```bash
$ kubectl get pods -o wide
```

> 参数`-o wide`会列出pod的IP，及它运行在哪个节点上。

查看集群事件：

```bash
$ kubectl get events
```

## 访问应用

部署到Kubernetes集群的应用，将运行在一个私有、隔离的网络上。它们只能在集群内部可见，要从集群外部访问这些应用，需要将容器的端口映射到节点的端口。

```bash
$ kubectl expose deployment hello-node --type=LoadBalancer --port 8080
```

上面的命令创建了一个service，并使用负载均衡器在集群外公开服务。这里的端口是容器暴露的端口，而不是访问服务用的端口。可以通过下面命令来查看访问服务的端口。

可以通过下面命令查看集群中的services：

```bash
$ kubectl get services
```

这里会列出服务的集群IP和外部IP。

> Minikube不支持LoadBalancer类型的服务，因此，上面命令中服务不会有外部IP显示。这时，可以运行`minikube service hello-node`获取可以访问服务的IP和端口。
>
> 还可以，在另一个窗口执行`minikube tunnel`，启动tunnel来创建一个可路由的IP。这样，运行`kubectl get services hello-node`，就可以看到外部IP了。

要访问这个服务，只要：

```bash
$ minikube service hello-node
或者
$ curl 服务的外部IP:端口号
```

它将打开一个浏览器，显示服务的响应。

如果不喜欢系统随机映射的端口，也可以使用下列命令来自己指定一个转发端口：

```bash
> $ kubectl port-forward service/hello-node 7080:8080
```

这样，就可以通过`localhost:7080`来访问hello-node服务了。

## 扩展应用

默认情况下，部署的应用只会运行一个副本，可以通过`kubectl get deployments`来查看副本数。

执行下列命令将副本数增加到4个：

```bash
$ kubectl scale deployments hello-node --replicas=4
```

扩展前：

![scaling1](Kubernetes/scaling1.svg)

扩展后：

![scaling2](Kubernetes/scaling2.svg)

> 注意：每个Pod的集群IP都不一样。

Kubernetes还支持Pod的自动缩放。缩放到零也是可能的，它将终止指定部署的所有Pod。 

服务集成有负载平衡器，可将网络流量分配到公开部署的所有Pod副本。服务将使用端点连续监视正在运行的Pod，以确保流量仅发送到可用的Pod。 

要缩减服务的副本数到2，只需再次运行scale命令：

```bash
$ kubectl scale deployments hello-node --replicas=2
```

可以看到有两个副本被删除。

## 滚动更新

滚动更新允许通过使用新的Pods实例逐步更新旧的Pods实例来实现部署的更新，而无需停机。新的Pod将在具有可用资源的节点上进行调度。 

滚动更新之前：

![rollingupdates1](Kubernetes/rollingupdates1.svg)

### 更新

使用下列命令将应用的镜像版本从`v1`升级到`v2`：

```bash
$ kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
```

如果使用YAML定义文件，则只需要修改YAML文件，然后再次运行下列命令，就会触发滚动更新：

```bash
$ kubectl apply -f kubernetes-bootcamp.yaml --record
```

> `--record`的作用是将当前命令记录到revision记录中，这样我们就可以知道每个revision对应的是哪个定义文件。
>
> 通过`kubectl rollout history deployments/kubernetes-bootcamp`可以查看revision历史记录。

在更新期间，服务只会将网络流量负载均衡到可用的Pod上。 

![rollingupdates2](Kubernetes/rollingupdates2.svg)

![rollingupdates3](Kubernetes/rollingupdates3.svg)

![rollingupdates4](Kubernetes/rollingupdates4.svg)

可以通过`maxSurge`和`maxUnavailable`字段来控制副本替换的数量：

- `maxSurge`：控制滚动更新过程中副本总数超过DESIRED的上限。`maxSurge`可以取整数值，也可以是百分数（**向上**取整），默认值为`25%`。
- `maxUnavailable`：控制滚动更新过程中，不可用的副本的上限。`maxUnavailable`可以取整数值，也可以是百分数（**向下**取整），默认值为`25%`。

`maxSurge`影响新副本创建的速度，`maxUnavailable`影响旧副本销毁的速度。

例如：

```yaml
spec:
	strategy:
		rollingUpdate:
			maxSurge: 35%
			maxUnavailable: 3
```

### 回滚

每次更新应用时，Kubernetes都会记录下当前的配置，保存为一个revision（修订版），这样就可以回滚到某个revision。

默认配置下，Kubernetes只会保留最近的几个revision，可以在Deployment定义文件中通过`revisionHistoryLimit`属性增加revision数量。

如果在滚动更新过程中，出现错误，要回滚到更新之前的状态，可以执行：

```bash
$ kubectl rollout undo deployments/kubernetes-bootcamp
```

您可以恢复到之前已保留的任何revision：

```bash
$ kubectl rollout undo deployments/kubernetes-bootcamp --to-revision=2
```

 

# 架构

Kubernetes用于协调一个高可用的计算机集群，这些计算机连接起来作为一个单元工作。 

![arch](Kubernetes/arch.png)

## Kubernetes集群

Kubernetes集群包含两种类型的资源： 

- **Master**节点（主节点，也称控制面板）负责管理集群。 例如，调度应用程序，维护应用程序的所需状态，扩展应用程序以及推出新更新。 
- **Node**节点（工作节点）是运行容器应用的工作器，它对应一个虚拟机或物理机（具体取决于集群）。每个Node都有一个Kubelet，它是一个管理Node并与Kubernetes Master通信的代理。Node还应具有用于处理容器操作的工具，例如Docker或rkt。 

Kubernetes集群可以部署在虚拟机或物理机上。一个应对生产流量的Kubernetes集群应至少有三个节点（学习环境可以只部署一个节点）。

> Master和Node可以部署在同一个虚拟机或物理机上。

Node使用Master公开的Kubernetes API与Master进行通信。终端用户还可以直接使用Kubernetes API与群集进行交互。 

![Kubernetes-cluster](Kubernetes/Kubernetes-cluster.svg)

Node可以包含多个pod，Kubernetes master会自动处理在群集中的节点上调度pod。

![nodes](Kubernetes/nodes.svg) 

### 控制面板组件

控制面板组件可以运行在集群的任何机器上，但为了简单起见，设置脚本通常会在同一个计算机上启动所有控制平面组件，并且不会在此计算机上运行用户容器。

**Kubernetes API 服务器**： [kube-apiserver](https://kubernetes.io/zh/docs/reference/command-line-tools-reference/kube-apiserver/)，是 Kubernetes 控制面的前端。

**etcd**：是兼具一致性和高可用性的键值数据库，用于保存 Kubernetes 所有集群数据的后台数据库。

**kube-scheduler**：为Pod的运行分配工作节点。

**kube-controller-manager**：在主节点上运行[控制器](https://kubernetes.io/docs/admin/kube-controller-manager/)的组件。

这些控制器包括:

- 节点控制器（Node Controller）: 负责在节点出现故障时进行通知和响应。
- 副本控制器（Replication Controller）: 负责为系统中的每个副本控制器对象维护正确数量的 Pod。
- 端点控制器（Endpoints Controller）: 填充端点(Endpoints)对象(即加入 Service 与 Pod)。
- 服务帐户和令牌控制器（Service Account & Token Controllers）: 为新的命名空间创建默认帐户和 API 访问令牌。

**cloud-controller-manager**：仅在使用云平台时需要，是将 Kubernetes 与任何其他云集成的最佳方式。

### 节点组件

节点组件在每个Node上运行，维护运行的 Pod 并提供 Kubernetes 运行时环境。

## Pods

当我们在Kubernetes上创建一个Deployment时，Kubernetes会创建一个Pod（包含容器）来托管你的应用程序实例，而不是直接创建容器。 

Pod是一个Kubernetes抽象，表示紧密相关的一组（一个或多个）应用程序容器（如Docker或rkt），以及这些容器的一些共享资源。 这些资源包括：

- 共享存储——Kubernetes中存储卷是在Pod级别中设置的，而不是容器级别。 
- 网络——Kubernetes集群中的每个Pod都有一个唯一的IP地址，Pod中的容器共享这个IP地址和端口空间。而同一个Pod的容器之间能直接通过localhost来发现和通信。 
- 有关如何运行每个容器的信息，例如容器映像版本或要使用的特定端口 

Pod模拟一个特定于应用程序的“逻辑主机”，并且可以包含相对紧密耦合的不同应用程序容器。 

> 只有需要直接共享资源的容器才应该放在一个Pod中。而象Tomcat和MySQL，它们是通过JDBC交换数据，而不是直接共享存储，因此不应该放在同一个Pod中。

Pod是Kubernetes的最小调度单位，同一Pod中的容器始终作为一个整体被Master调度到一个Node上运行，并在**同一节点**的共享上下文中运行。 也就是说，一个Pod的所有容器都运行在同一个节点上，一个Pod绝不跨越两个节点。

每个Pod都与调度它的节点绑定，并保持在那里直到终止（根据重启策略）或删除。如果节点发生故障，则会在群集中的其他可用节点上安排相同的Pod。 

![pods](Kubernetes/pods.svg)

可用下列命令获取Pods的名称：

```bash
$ export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
```

Kubernetes集群中的所有Pods都在同一个共享网络地址空间中，这意味着每个Pod都可以通过其他Pod的IP地址来实现相互访问，它们之间没有NAT网关，同时不管实际节点间的网络拓扑结构如何。

容器应该如何分组到Pod中：当决定是将两个容器放入一个Pod还是两个单独的Pod时，我们需要问自己以下问题：

- 它们需要在相同主机上一起运行还是可以在不同的主机上运行？
- 它们代表的是一个整体还是相互独立的组件？
- 它们必须一起进行扩缩容还是可以分别进行？

![Pod不应该包含多个并不需要运行在同一主机上的容器](E:\supercalifragilisticexpialidociouser.github.io\source\_posts\Kubernetes\Pod不应该包含多个并不需要运行在同一主机上的容器)

可以通过`kubectl run`命令直接创建Pod。

## 控制器——运行Pod

Kubernetes通常不会直接创建Pods，而是通过控制器（Controller）来管理Pod。

为了满足不同业务场景，Kubernetes提供了多种控制器：

- Deployment：可管理Pod的多个副本，并确保Pod按照预期的状态运行。
- ReplicaSet：实现了Pod的多副本管理。实际上，使用Deployment时会自动创建ReplicaSet，也就是说，Deployment是通过ReplicaSet来管理Pod的多个副本。我们通常不需要直接使用ReplicaSet。
- DaemonSet：用于每个Node最多运行一个Pod副本的场景。
- StatefuleSet：能够保证Pod的每个副本在整个生命周期中名称是不变的，并且保证副本按照固定的顺序启动、更新或删除。
- Job：用到运行结束就删除的应用，常用于一次性的任务。其他控制中的Pod通常是长期持续运行，除非手动删除。

## 服务——发现Pod

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

在使用`kubectl`工具管理kubernetes资源时，可用`-n 命名空间`选项来指定资源的命名空间。没有为资源显式指定命名空间，则属于`default`命名空间。

可用`--all-namespaces`选项表示所有命名空间。

# 安装

## Kind

[Kind](https://kind.sigs.k8s.io)是在本地计算机部署Kubernetes的工具，通常用于本地开发和CI。

## Minikube

Minikube是一种轻量级Kubernetes实现，可在本地计算机上创建VM并部署仅包含一个节点的简单集群，它适用于开发和测试。 

首先，安装Kubernetes命令行客户端[kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)：（通常安装在开发本地机器上，而不是安装在集群上）

```bash
$ curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
$ chmod +x ./kubectl
$ sudo mv ./kubectl /usr/local/bin/kubectl
$ kubectl version --client
```

> kubectl可以不需要独立安装。例如下面的两种用法是等价的：
>
> ```bash
> # 独立安装kubectl
> $ kubectl get po -A
> 
> # 由Minikube自动下载合适的kubectl，并执行命令
> $ minikube kubectl -- get po -A
> ```
>
> 

然后，安装VirtualBox、KVM2或Docker。（Minikube需要在VM中运行Kubernetes）

现在，可以安装[Minikube](https://minikube.sigs.k8s.io/docs/start/)：

```bash
$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
$ sudo install minikube-linux-amd64 /usr/local/bin/minikube

# 国内如果下载不了，使用阿里云构建的版本
$ curl -Lo minikube https://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v1.16.0/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
```

显示Minikube版本号：

```bash
$ minikube version
```

最后，启动集群：

```bash
$ minikube start

# 可以用下面参数指定使用阿里云镜像
$ minikube start --image-mirror-country cn \
    --iso-url=https://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/iso/minikube-v1.16.0.iso \
    --registry-mirror=https://huo5y7st.mirror.aliyuncs.com \
    --vm-driver=virtualbox
```

`--image-mirror-country cn`会将`k8s.gcr.io`换成`registry.cn-hangzhou.aliyuncs.com/google_containers`作为安装Kubernetes的容器镜像仓库。

集群启动成功后，可以使用下列命令，打开一个Kubernetes仪表盘：

```bash
$ minikube dashboard
```



## kubeadm

参见：[Creating Highly Available Clusters with kubeadm](https://kubernetes.io/docs/setup/independent/high-availability/)

## 定制安装

参见：[follow-me-install-kubernetes-cluster](https://github.com/opsnull/follow-me-install-kubernetes-cluster)

## 使用Photon OS

Photon OS是一个专注于容器的精简Linux操作系统。它的完全安装中已经包含Kubernetes和Mesos。

## 使用kubekit搭建k8s集群

[Kubekit](https://github.com/Orientsoft/kubekit)是一个部署工具包，它为kubernetes提供离线安装解决方案。您可以使用它将Kubernetes部署到OFFLINE生产环境。

Kubekit将安装

- Docker（1.12.6）
- Kubernetes及其所有组件
- Kubernetes仪表板，默认节点端口：31234

### 安装操作系统

首先官方支持下面两个操作系统，而且都要是最小化安装支持的

- CentOS release 7.3.1611
- CentOS release 7.4.1708

安装完成之后关闭防火墙：

```
systemctl stop firewalld
systemctl disable firewalld
```

关闭selinux：

```
setenforce 0
vim /etc/selinux/config
```

修改为：

```
SELINUX=disabled
```

最好还可以同步一下时间什么的：

```
yum install ntpdate
ntpdate 0.cn.pool.ntp.org
```

### 下载kubekit

```
yum install wget
wget https://kubekit.orientsoft.cn/kubekit-linux64-0.3.tar.gz
```

解压

```bash
tar -zxvf kubekit-linux64-0.3.tar.gz
mv kubekit-release/ kubekit
```

下载离线包：

```
wget https://kubekit.orientsoft.cn/package-1.9.2.tar.gz
tar -zxvf package-1.9.2.tar.gz
mv package kubekit
```

给脚本赋予可执行权限

```
cd kubekit/package/
chmod +x ./*.sh
```

### 安装并初始化master节点

```bash
./kubekit init 192.168.38.166
```

接着ctrl+c退出来，然后重新启动kubekit的dashboard并且放在后台

```
./kubekit server &
```

### 添加一个node

浏览器访问 ip:9000。

创建一个同样安装着centos 1708最小化安装的机器，之后打开修改主机名：

```
hostnamectl set-hostname kubekit-node1
```

接着点击web界面上的add node，输入ssh账号密码等信息，最后选中点击start deploy就可以了。

之后你就会在kubernetes的dashboard看到这个节点的详细信息了。

# 网络管理

## 网络模型

Kubernetes采用基于扁平地址空间的网络模型，集群中的每个Pod都有自己的IP地址，Pod之间不需要配置NAT就能直接通信。另外，同一个Pod中的容器共享Pod的IP，它们能够通过localhost通信。也就是说，每个Pod可以看作一个个独立的系统，面Pod中的容器则可被看作同一系统中的不同进程。

在Kubernetes集群中，Pod可能会频繁地销毁和创建，它们的IP是不固定的。为了解决这个问题，Service提供了访问Pod的抽象层。无论后端的Pod如何变化，Service都作为稳定的前端对外提供服务。同时，Service还提供了高可用和负载均衡功能，Service负责将请求转发给正确的Pod。

无论是Pod的IP，还是Service的集群IP，它们只能在Kubernetes集群中可见，对集群之外的世界，这些IP都是私有的。Kubernetes提供了多种方式对外公布服务（参见“对外公布服务”）。

## 网络方案

Kubernetes采用了Container Networking Interface（CNI）规范，它支持多种容器运行时，不仅仅是Docker。

CNI的插件模型支持不同组织和公司开发的第三方插件，可以灵活选择适合的网络方案。

目前已有多种支持Kubernetes的网络方案（例如：Flannel、Calico、Canal、Weave Net等），它们都实现了CNI规范。用户无论选择哪种方案，得到的网络模型都是一样的。

## 网络政策

网络政策（Network Policy）是Kubernetes的一种资源，它通过标签选择Pods，并指定其他Pods或外界如何与这些Pods通信。

默认情况下，任何来源的网络流量都能够访问Pods，没有任何限制。当为Pods定义了网络政策时，只有政策允许的流量才能访问Pods。

不是所有的Kubernetes网络方案都支持网络政策，比如Flannel就不支持，Calico、Canal是支持的。

## Canal

Canal用Fannel实现Kubernetes集群网络，同时又用Calico实现了网络政策。

### 部署Canal

部署Canal与部署其他Kubernetes网络方案类似，就是在Kubernetes集群初始化（例如`kubeadm init`）之后，通过`kubectl apply`安装。

如果Kubernetes集群已经安装有其他的网络方案，则只能重建当前集群，再部署Canal。

> 要销毁当前集群，最简单的方法是在每个节点上执行`kubeadm reset`。

```bash
$ kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/canal/rbac.yaml
$ kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/canal/canal.yaml
```

部署成功后，Canal作为DaemonSet部署到每个节点，并属于`kube-system`命名空间。

### 应用网络政策

policy.yaml：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
	name: access-httpd
spec:
	podSelector:
		matchLabels:
			run: httpd
	ingress:
	- from:
		- podSelector:
		  	matchLabels:
		  		access: "true"
		ports:
		- protocol: TCP
			port: 80
```

上面的网络政策中的访问规则应用于标签为`run: httpd`的Pods。

在`ingress`属性中定义只有标签为`access: "true"` 的Pod才能访问应用，且只能访问80端口。

除了通过`ingress`属性限制进入的流量，也可以用`egress`属性限制外出的流量。

创建网络政策：

```bash
$ kubectl apply -f policy.yaml
```

查看网络政策：

```bash
$ kubectl get networkpolicy
```

# 集群管理

查看集群信息：`kubectl cluster-info`。

查看集群配置：`kubectl config view`。

## Minikube的集群管理

登录Minikube VM：`minikube ssh …`

暂停Kubernetes而不影响已部署的应用程序：`minikube pause`

停止集群：`minikube stop`

增加默认内存限制（需要重新启动）：`minikube config set memory 16384`

列出Kubernetes插件列表：`minikube addons list`

启用插件：`minikube addons enable metrics-server`

禁用插件：`minikube addons disable metrics-server`

创建另一个运行旧Kubernetes版本的集群：`minikube start -p aged --kubernetes-version=v1.16.1`

删除所有minikube集群：`minikube delete --all`

# 应用部署

## 创建部署

- 用kubectl命令直接创建：

  ```bash
  $ kubectl run foo --image=foo:1.0.0 --port=8080 --replicas=2
  ```

  在命令行中通过形如`--属性名=属性值`的参数指定资源的属性。

- 通过将资源属性写入到YAML格式或JSON格式的定义文件中，并用`kubect`命令带`-f`选项来创建：

  ```bash
  $ kubectl create -f foo.yml
  或
  $ kubectl apply -f foo.yml
  ```

### 部署定义文件

一个YAML格式的定义文件，可以定义多个资源，用`---`分割。

## 删除部署

```bash
$ kubectl delete deployments foo
或者
$ kubectl delete deployments/foo
或者
$ kubectl delete -f foo.yml
```

删除部署也会删除关联的Pod实例。 

## 查询部署

列出`myns`命名空间下所有的部署：

```bash
$ kubectl get deployments -n myns
```

查询名叫`foo`的部署：

```bash
$ kubectl get deployments/foo
```

## 查看部署详情

```bash
$ kubectl describe deployments/foo
```

## 伸缩部署

改变YAML文件中`replicas`属性的数量，然后再执行`kubectl apply -f`。或者使用如下命令：

```bash
$ kubectl scale deployments/foo --replicas=4
```

## 故障转移

模拟`k8s-node2`出现故障（关闭该节点）：

```bash
root@k8s-node2:~# halt -h
```

等待一段时间，Kubernetes会检查到`k8s-node2`不可用。假设原来在`k8s-node2`上运行2个Pods实例，则这些Pods将被标记为`Unknown`状态，并在`k8s-node1` 上新创建2个Pods实例，维持总副本数不变。

当`k8s-node2`恢复后，`Unknown`状态的Pods会被删除，不过已经运行的Pods不会重新调度回`k8s-node2`。

## 用标签控制Pod的位置

默认配置下，Kubernetes会将Pods调度到任何可用的Node。不过有些情况我们希望将Pods部署到指定Node，比如将大量磁盘I/O的Pods部署到配置了SSD的Node上。Kubernetes是通过标签（label）来实现该功能的。

首先，给指定Node加一个标签（这里是`disktype=ssd`）：

```bash
$ kubectl label nodes/k8s-node1 disktype=ssd
```

查看节点的标签：

```bash
$ kubectl get nodes --show-labels
```

然后，编辑部署定义文件的属性`nodeSelector`：

```yaml
spec:
	temlate:
    spec:
			nodeSelector:
				disktype: ssd
```

重新执行命令`kubectl apply -f`应用这个定义文件。

# 存储管理

容器中的磁盘文件是短暂的， 当一个Container崩溃时，kubelet将重新启动它，但文件将丢失 —— 容器以干净状态启动。此外，在Pod中一起运行的容器，通常需要在这些容器之间共享文件。 Kubernetes的卷（Volume）解决了这两个问题。 

从本质上讲，Kubernetes的卷只是一个目录，可能包含一些数据，这点与Docker的卷类似。当卷被挂载到Pod时，Pod中的所有容器都可以访问这个卷。该目录是如何形成的，支持它的介质以及它的内容由所使用的卷类型决定。 另外，Kubernetes

> Kubernetes 卷的类型详见：https://kubernetes.io/docs/concepts/storage/

卷不能挂载其他卷或具有到其他卷的硬链接。 Pod中的每个容器必须独立指定各自的卷的挂载路径。 

卷的使用分两步：首先，使用`.spec.volumes` 字段定义卷。然后，使用`.spec.containers.volumeMounts` 字段为Pod中的每个容器挂载相应的卷。

## emptyDir卷

emptyDir类型的卷是宿主节点上的一个目录（初始总是空的），它的生命周期与Pod一致。当Pod从节点上删除时，卷的内容也会被删除。但如果只是容器被销毁而Pod还在，则卷将保留。因此，emptyDir卷不具备持久性，它只适用于Pod中的容器需要临时共享存储空间的场景。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir: {}
```

> 不能指定emptyDir卷在节点上的位置，可以通过`docker inspect`命令查看容器中卷实际挂载目录。通常是`/var/lib/kubelet/pods/…`这样的目录。

## hostPath卷

hostPath卷与emptyDir卷一个，也是宿主节点上的一个目录，只不过emptyDir卷无法指定目录，而hostPath卷可以自己指定目录（必须是已存在的目录）。另外，当Pod被销毁后，hostPath卷还是会保留。

hostPath卷的缺点是，它的实际位置在宿主节点上，增加了Pod与节点的耦合，一旦宿主节点崩溃，hostPath卷将无法访问。

hostPath卷通常适用于那些需要访问Kubernetes或Docker内部数据的应用。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      # directory location on host
      path: /data
      # this field is optional
      type: Directory
```

## 云存储卷

Kubernetes支持使用AWS、GCE、Azure等公有云上的云硬盘作为卷。

例如：AWS Elastic Block Store

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-ebs
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-ebs
      name: test-volume
  volumes:
  - name: test-volume
    # This AWS EBS volume must already exist.
    awsElasticBlockStore:
      volumeID: <volume-id>
      fsType: ext4
```

## 分布式存储卷

Kubernetes卷可以使用主流的分布式存储，例如Ceph、GlusterFS等。

分布式存储最大特点是不依赖Kubernetes，与Kubernetes集群是分离的，数据被持久化后，即使整个Kubernetes集群崩溃也不会受损。

例如：Cephfs

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cephfs
spec:
  containers:
  - name: cephfs-rw
    image: kubernetes/pause
    volumeMounts:
    - mountPath: "/mnt/cephfs"
      name: cephfs
  volumes:
  - name: cephfs
    cephfs:
      monitors:
      - 10.16.154.78:6789
      - 10.16.154.82:6789
      - 10.16.154.83:6789
      # by default the path is /, but you can override and mount a specific path of the filesystem by using the path attribute
      # path: /some/path/in/side/cephfs
      user: admin
      secretRef:
        name: ceph-secret
      readOnly: true
---
apiVersion: v1
kind: Secret
metadata:
  name: ceph-secret
data:
  key: QVFCMTZWMVZvRjVtRXhBQTVrQ1FzN2JCajhWVUxSdzI2Qzg0SEE9PQ==
```

## PersistentVolume和PersistentVolumeClaim

详见：https://kubernetes.io/docs/concepts/storage/persistent-volumes/

PersistentVolume（PV）是外部存储系统中的一块存储空间，由存储系统管理员创建和维护。与卷一样，PV具有持久性，生命周期独立于Pod。

PersistentVolumeClaim（PVC）是对PV的申请（Claim），用于将PersistentVolume挂载到Pod中 。PVC通常由用户（开发人员）创建和维护。需要为Pod分配存储资源时，用户可以创建一个PVC，指明存储资源的容量大小、访问模式等要求，Kubernetes会查找并提供满足条件的PV。

有个PVC，用户只需要告诉Kubernetes需要什么样的存储资源，而不必关心真正的空间从哪里分配、如何访问等底层细节信息。这些存储Provider的底层信息交给存储系统管理员来处理，从而实现了用户与系统管理员的职责分离。

Kubernetes支持多种类型的PV，例如AWS EBS、Ceph等。

PV有两种供应方式：静态和动态。

### 静态供应

静态供应PV是指，我们提前创建PV，然后再通过PVC申请PV。

下面以NFS为例：

Persistent Volume：mysql-pv.yml

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 5Gi    # PV的容量
  volumeMode: Filesystem
  accessModes:    # 访问模式。有：ReadWriteOnce、ReadOnlyMany、ReadWriteMany三种。
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain    # 回收策略。Retain：手动回收。Recycle：自动清除PV中的数据。Delete：自动删除Storage Provider上的对应存储资源。
  storageClassName: slow   # 为PV设置一个分类
  mountOptions:
    - hard
    - nfsvers=4.1
  nfs:
    path: /nfsdata/mysql-pv
    server: 172.17.0.2
```

创建PV：

```bash
$ kubectl apply -f pv0003.yml
```

Persistent Volume Claim：mysql-pvc.yml

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 8Gi
  storageClassName: slow
  selector:
    matchLabels:   # 请求的PV必须具有此标签
      release: "stable"
    matchExpressions:
      - {key: environment, operator: In, values: [dev]}
```

创建PVC：

```bash
$ kubectl apply -f myclaim.yml
```

可以通过命令`kubectl get pvc`和`kubectl get pv`的输出看到`mysql-pvc`已经绑定到`mysql-pv`了。

接下来，就可以在容器中使用该存储了：（PVC必须与使用它的Pod存在于同一命名空间中 ）

```yaml
kind: Deployment
apiVersion: apps/v1beta1
metadata:
  name: mysql
spec:
	selector:
		matchLabels:
			app: mysql
	template:
		metadata:
			labels:
				app: mysql
		spec:
      containers:
        - name: mysql
          image: mysql:5.6
          env:
          - name: MYSQL_ROOT_PASSWORD
            value: password
          ports:
          - containerPort: 3306
            name: mysql
          volumeMounts:
          - mountPath: "/var/lib/mysql"
            name: mysql-persistent-storage
      volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: mysql-pvc
---
kind: Service
apiVersion: v1
metadata:
	name: mysql
spec:
	ports:
	- port: 3306
	selector:
		app: mysql
```

### 动态供应

动态供应PV是指，如果没有满足PVC条件的PV时，会动态创建PV。

动态供应是通过StorageClass实现的，StorageClass定义了如何创建PV。

> 详见：https://kubernetes.io/docs/concepts/storage/storage-classes/

为启用基于StorageClass的动态供应，群集管理员需要在API服务器上启用DefaultStorageClass的 [admission controller](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#defaultstorageclass) 。 也就是说，`kube-apiserver.service` 文件中，配置`--enable-admission-plugins`参数的值中包含`DefaultStorageClass`。例如：

```shell
…
--enable-admission-plugins=Initializers,NamespaceLifecycle,NodeRestriction,LimitRanger,ServiceAccount,DefaultStorageClass,ResourceQuota
…
```

首先，系统管理员创建StorageClass：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: standard
provisioner: kubernetes.io/aws-ebs
parameters:
	type: gp2
reclaimPolicy: Retain  # 支持Delete和Retain两种，默认是Delete。
mountOptions:
  - debug
```

然后，在PVC申请PV时，只需要指定StorageClass、容量以及访问模式即可：

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: mypvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 8Gi
  storageClassName: standard
```



### 回收PV

当不需要使用PV时，可以删除PVC回收PV：

```bash
$ kubectl delete pvc/myclaim
```

当PVC被删除后，如果`persistentVolumeReclaimPolicy`为`Recycle`或`Delete`时，Kubernetes会启动一个新的Pod——recycler-for-mysql-pvc，这个Pod的作用就是清除PV `mysql-pv`的数据。此时，`mysql-pv`的状态为“Released”，表示已经解除了与`mysql-pvc`的绑定，正在清除数据，不过此时还不可用。当数据清除完毕，`mysql-pv`的状态重新变为“Available”，此时可以被新的PVC申请。

如果`persistentVolumeReclaimPolicy`为`Retain`时，Kubernetes不会启动用于清除数据的Pod，`mysql-pv`中的数据得到保留，但其PV状态会一直处于“Released”，不能被其他PVC申请。这时，如果删除了PV`mysql-pv`，只是删除了`mysql-pv`对象，存储空间中的数据仍然不会被删除。重新创建`mysql-pv`后，它的状态将变为“Available”，此时可以被新的PVC申请。

# 服务管理

## DNS访问服务

在集群中，除了可以通过集群IP访问服务外，Kubernetes还提供了更为方便的DNS访问。

要使用DNS访问服务，要先安装kube-dns组件，它是一个DNS服务器。

每当有新的服务被创建，kube-dns会添加该服务的DNS记录。集群中的Pods可以通过`服务名.命名空间名` 域名形式访问服务。例如：

```bash
$ wget httpd-svc.default:8080
```

如果Pods与服务在同一个命名空间中，可以省略`.命名空间名`部分，直接使用服务名进行访问。

> 服务的完整域名，可以进入它对应的Pod中，查看`/etc/resolv.conf`文件。例如：`httpd-svc.default.svc.cluster.local`。

## 服务对Pods的负载均衡

服务通过标签与一组Pods关联，会将外部流量负载均衡到它所关联的每一个Pod。

## 对外公布服务

### ClusterIP方式

默认情况下，Kubernetes服务的公布类型是`ClusterIP`，即通过集群内部的IP对外公布服务，这种方式公布的服务，只有集群内的节点和Pod可以访问。

### NodePort方式

通过集群节点的静态端口对外提供服务，集群外部可以通过`节点IP:节点端口号`访问服务。

httpd-svc.yml：

```yaml
apiVersion: v1
kind: Service
metadata:
	name: httpd-svc
spec:
	type: NodePort
	selector:
		run: httpd
	ports:
	- protocol: TCP
		nodePort: 30000      （节点上监听的端口。如果未设置，则默认是随机产生一个端口号）
		port: 8080           （服务集群IP上监听的端口）
		targetPort: 80       （目标Pods上监听的端口）
```

创建NodePort类型的服务：

```bash
$ kubectl apply -f httpd-svc.yml
```

也可以直接使用`kubectl expose --type="NodePort"`命令为已有部署创建NodePort服务。

如果使用`kubectl get services/httpd-svc`，就会发现有一个“EXTERNAL-IP”，它就是节点的IP。

这种方式发布的服务，实际上并不是高可用的，因为它是通过某个节点IP来访问的。

### LoadBalancer方式



### ExternalName方式

## Ingress服务

Ingress服务可以简单的理解成k8s内部的nginx, 用作负载均衡器。它的好处有：

- 动态配置服务：按照传统方式，当新增加一个服务时，我们可能需要在流量入口加一个反向代理指向我们新的k8s服务。而如果用了Ingress，只需要配置好这个服务，当服务启动时，会自动注册到Ingress的中，不需要而外的操作。
- 减少不必要的端口暴露：使用Ingress服务后，只有Ingress自身服务可能需要映射出去, 其他服务都不需要用NodePort方式对外公布。

# 配置管理

## 敏感信息

敏感信息使用Secret管理。（详见：https://kubernetes.io/docs/concepts/configuration/secret/）

Secret会以密文的方式存储数据，避免了直接在配置文件中保存敏感信息。

Secret会以卷的形式被挂载到Pod，容器可通过文件的方式使用Secret中的敏感数据。此外，容器也可以环境变量的方式使用这些数据。

### 创建Secret

#### 通过`--from-literal`

```bash
$ kubectl create secret generic mysecret --from-literal=username=admin --from-literal=password=123456
```

每个`--from-literal`定义一条信息。

每条信息的值的内容使用明文，创建secret时，会自动使用base64编码。

#### 通过`--from-file`

```bash
$ echo -n admin > ./username
$ echo -n 123456 > ./password
$ kubectl create secret generic mysecret --from-file=./username --from-file=./password
```

每个文件内容对应一条信息，文件名就是键，文件内容是值。

每文件的内容使用明文，创建secret时，会自动使用base64编码。

#### 通过`--from-env-file`

```bash
$ cat << EOF > env.txt
username=admin
password=123456
EOF
$ kubectl create secret generic mysecret --from-env-file=env.txt
```

文件`env.txt`中每行对应一条信息。

每条信息的值的内容使用明文，创建secret时，会自动使用base64编码。

#### 通过YAML或JSON文件定义

首先，必须自己手动将敏感信息使用base64编码：

```bash
$ echo -n admin | base64
YWRtaW4=
$ echo -n 123456 | base64
MTIzNDU2
```

然后，使用上面编码的信息创建`mysecret.yml`：

```yaml
kind: Secret
apiVersion: v1
metadata:
	name: mysecret
data:
	username: YWRtaW4=
	password: MTIzNDU2
```

最后，调用命令创建`mysecret`：

```bash
$ kubectl apply -f mysecret.yml
```

### 查看Secret

查看Secret：

```bash
$ kubectl get secret/mysecret -o yaml
```

查看Secret详情：

```bash
$ kubectl describe secret/mysecret
```

输出的结果中，只会显示敏感信息占用的字节数，不会显示敏感信息内容。

编辑Secret：

```bash
$ kubectl edit secret/mysecret
```

在编辑界面可以看到经过base64编码后的敏感信息。

可自行通过下列命令解码：

```bash
$ echo -n YWRtaW4= | base64 --decode
admin
$ echo -n MTIzNDU2 | base64 --decode
123456
```

### 使用Secret

#### 通过卷的方式使用

在Pod的定义文件中将Secret配置成卷：mypod.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: mypod
spec:
	containers:
	- name: mypod
	  image: busybox
	  args:
	  	- /bin/sh
	  	- -c
	  	- sleep 10; touch /tmp/healthy; sleep 30000
	  volumeMounts:
	  - name: foo
	    mountPath: "/etc/foo"
	    readOnly: true
	volumes:
	- name: foo
	  secret:
	  	secretName: mysecret
```

Kubernetes会在`mountPath`指定的路径`/etc/foo`下为每条敏感信息创建一个文件，文件名就是信息条目的键（这里是`/etc/foo/username`和`/etc/foo/password`），值是以明文（自动使用base64解码）存放在文件中的内容。

```bash
$ kubectl apply -f mypod.yml
$ kubectl exec -it mypod sh
/ # ls /etc/foo
password username
/ # cat /etc/foo/username
admin
/ # cat /etc/foo/password
123456
```

我们也可以自定义存放数据的文件名和位置：

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: mypod
spec:
	containers:
	- name: mypod
	  image: busybox
	  args:
	  	- /bin/sh
	  	- -c
	  	- sleep 10; touch /tmp/healthy; sleep 30000
	  volumeMounts:
	  - name: foo
	    mountPath: "/etc/foo"
	    readOnly: true
	volumes:
	- name: foo
	  secret:
	  	secretName: mysecret
	  	items:
	  	- key: username
	  	  path: my-group/my-username
	  	- key: password
	  	  path: my-group/my-password
```

以卷方式使用的Secret支持动态更新，即Secret更新后，所有使用该Secret的容器中的数据也会更新。

#### 通过环境变量方式使用

在Pod的定义文件中，将Secret的信息读取到环境变量：mypod.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: mypod
spec:
	containers:
	- name: mypod
	  image: busybox
	  args:
	  	- /bin/sh
	  	- -c
	  	- sleep 10; touch /tmp/healthy; sleep 30000
	  env:
	  - name: SECRET_USERNAME
	    valueFrom:
	    	secretKeyRef:
	    		name: mysecret
	    		key: username
	  - name: SECRET_PASSWORD
	    valueFrom:
	    	secretKeyRef:
	    		name: mysecret
	    		key: password
```

通过环境变量读取Secret很方便，但无法支持Secret动态更新。

## 普通信息

普通信息使用ConfigMap管理。（详见：https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/）

ConfigMap的创建和使用方式与Secret非常类似，主要不同是ConfigMap的数据以明文形式存放。

### 创建ConfigMap

#### 通过`--from-literal`

```bash
$ kubectl create configmap myconfigmap --from-literal=config1=xxx --from-literal=config2=yyy
```

#### 通过`--from-file`

```bash
$ echo -n xxx > ./config1
$ echo -n yyy > ./config2
$ kubectl create configmap myconfigmap --from-file=./config1 --from-file=./config2
$ kubectl get configmap/myconfigmap -o yaml
apiVersion: v1
data:
  config1: xxx
  config2: yyy
kind: ConfigMap
metadata:
  creationTimestamp: 2018-07-20T03:21:10Z
  name: myconfigmap
  namespace: default
  resourceVersion: "311435"
  selfLink: /api/v1/namespaces/default/configmaps/myconfigmap
  uid: f2c0f646-8bcb-11e8-a64f-005056b1d7ee
```

`--from-file`指定的文件，也可以包含多行信息：

```bash
$ cat << EOF > ui.properties
> color.good=purple
> color.bad=yellow
> allow.textmode=true
> how.nice.to.look=fairlyNice
> EOF
$ kubectl create configmap ui-config --from-file=./ui.properties
$ kubectl get configmap/ui-config -o yaml
apiVersion: v1
data:
  ui.properties: |    # 只要有一个文件包含多条信息，就会生成默认以文件名命名的键，整个文件内容（不管内容格式）为值。注意：行尾的“|”符号。
    color.good=purple  # 由于是整个文件内容原封不动照搬，因此这里有等号。
    color.bad=yellow
    allow.textmode=true
    how.nice.to.look=fairlyNice
kind: ConfigMap
metadata:
  creationTimestamp: 2018-07-20T03:02:52Z
  name: ui-config
  namespace: default
  resourceVersion: "310013"
  selfLink: /api/v1/namespaces/default/configmaps/ui-config
  uid: 6455dfa6-8bc9-11e8-a64f-005056b1d7ee
```

可以为加载的文件自定义键：

```bash
$ cat << EOF > logging.conf
> class: logging.handlers.RotatingFileHandler
> formatter: precise
> level: INFO
> filename: %hostname-%timestamp.log
> EOF
$ kubectl create configmap logging-conf --from-file=logging=./logging.conf
$ kubectl get configmap/logging-conf -o yaml
apiVersion: v1
data:
  logging: |
    class: logging.handlers.RotatingFileHandler
    formatter: precise
    level: INFO
    filename: %hostname-%timestamp.log
kind: ConfigMap
metadata:
  creationTimestamp: 2018-07-20T04:15:03Z
  name: logging-conf
  namespace: default
  resourceVersion: "315620"
  selfLink: /api/v1/namespaces/default/configmaps/logging-conf
  uid: 7a0ed236-8bd3-11e8-a64f-005056b1d7ee
```

如果`--from-file`的值是一个目录，则会加载该目录下所有文件：

```bash
$ mkdir bar
$ cd bar
$ echo -n example.com > ./baseurl
$ mv ui.properties bar/
$ kubectl create configmap my-config --from-file=bar
$ kubectl get configmap/my-config -o yaml
apiVersion: v1
data:
  baseurl: |
    example.com
  ui.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true
    how.nice.to.look=fairlyNice
kind: ConfigMap
metadata:
  creationTimestamp: 2018-07-20T03:38:21Z
  name: my-config
  namespace: default
  resourceVersion: "312775"
  selfLink: /api/v1/namespaces/default/configmaps/my-config
  uid: 59b12c48-8bce-11e8-a64f-005056b1d7ee
```

#### 通过`--from-env-file`

```bash
$ cat << EOF > env.txt
config1=xxx
config2=yyy
EOF
$ kubectl create configmap myconfigmap --from-env-file=env.txt
```

当在同一个命令行中，使用多个`--from-env-file`时，只有最后一个有效。

#### 通过YAML或JSON文件定义

创建ConfigMap的YAML定义文件：myconfigmap.yml

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
	name: myconfigmap
data:
	config1: xxx   # 使用明文存储
	config2: yyy
```

### 使用ConfigMap

#### 通过卷的方式使用

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: mypod
spec:
	containers:
	- name: mypod
	  image: busybox
	  args:
	  	- /bin/sh
	  	- -c
	  	- sleep 10; touch /tmp/healthy; sleep 30000
	  volumeMounts:
	  - name: foo
	    mountPath: "/etc/foo"
	    readOnly: true
	volumes:
	- name: foo
	  configMap:
	  	name: logging-conf
```

则：

```bash
$ kubectl apply -f mypod.yml
$ ls /etc/foo
logging.conf
$ cat /etc/foo/logging.conf
class: logging.handlers.RotatingFileHandler
formatter: precise
level: INFO
filename: %hostname-%timestamp.log
```

还可以自定义存放数据的文件名和位置：

```yaml
	volumes:
	- name: foo
	  configMap:
	  	name: logging-conf
	  	items:
	  	- key: logging.conf
	  	  path: myapp/logging.conf
```



#### 通过环境变量方式使用

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: mypod
spec:
	containers:
	- name: mypod
	  image: busybox
	  args:
	  	- /bin/sh
	  	- -c
	  	- sleep 10; touch /tmp/healthy; sleep 30000
	  env:
	  - name: CONFIG1
	    valueFrom:
	    	secretKeyRef:
	    		name: myconfigmap
	    		key: config1
	  - name: CONFIG2
	    valueFrom:
	    	secretKeyRef:
	    		name: myconfigmap
	    		key: config2
```

# DaemonSet

DaemonSet部署的Pods副本会分布到每个节点上，并且每个节点最多只能运行一个副本。

典型应用场景：

- 在集群的每个节点上运行存储守护进程，比如ceph。
- 在每个节点上运行日志收集守护进程，比如logstash。
- 在每个节点上运行监控守护进程，比如 Prometheus Node Exporter。

# 作业管理

Job用于完成一次性任务，比如批处理程序，完成后容器就退出。

Job的`restartPolicy`属性只能设置为`Never`或`OnFailure`。

## Job的并行性

## 定时Job

# 安全防护

# 健康检查

## 默认健康检查机制

Kubernetes默认健康检查机制：当容器进程（由Dockerfile的CMD或ENTRYPOINT指定）返回值为非零时，则认为容器发生故障，Kubernetes就会根据`restartPolicy` 设置重启容器。

## Liveness探测

Liveness探测让用户可以自定义判断容器是否健康的条件。如果探测失败，Kubernetes就会根据`restartPolicy` 设置重启容器。

Liveness探测是在YAML定义文件中由`livenessProbe`设置的。

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-http
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/liveness
    args:
    - /server
    livenessProbe:
      httpGet:
        scheme: HTTPS     （默认是HTTP）
        path: /healthz
        port: 8080
        httpHeaders:
        - name: X-Custom-Header
          value: Awesome
      initialDelaySeconds: 3
      periodSeconds: 3
```

为了执行探测，kubelet向Container中运行的服务器发送HTTP GET请求并侦听端口8080。如果服务器的`/healthz`路径的处理程序返回成功代码（任何大于或等于200且小于400的代码表示成功，任何其他代码表示失败），则kubelet认为Container是活动的并且健康。如果处理程序返回失败代码，则kubelet会终止Container并重新启动它。 

`initialDelaySeconds 10`表示在开始执行第一次Liveness探测之前等待10秒，通常根据应用的启动时间来设置。

`periodSeconds 5`表示第5秒执行一次Liveness探测。Kubernetes如果连续执行3次Liveness探测均失败，则会杀掉容器，重建并重启容器。

详细使用方式，参见：https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/

## Readiness探测

Readiness探测可自定义Kubernetes什么时候可以将容器加入到Service负载均衡池中，对外提供服务。

Readiness的配置方式同Liveness，唯一的区别是使用`readinessProbe`字段代替`livenessProbe` 字段。例如：

```yaml
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

通过`cat`命令检查`/tmp/healthy`文件是否存在。如果命令执行成功，返回值为零，则认为本次Readiness探测成功；否则，返回值为非零，本次Readiness探测失败。

Liveness探测和Readiness探测是独立执行的，二者之间没有依赖，因此，可以单独使用，也可以同时使用。

# 包管理

Helm之于Kubernetes好比yum之于RHEL，或者apt-get之于Ubuntu，Helm 可以理解为 Kubernetes 的包管理工具 。 

## Helm架构

Helm的基本概念：

- Chart：Helm的应用打包格式（类似于apt、yum的软件安装包），它由一系列文件组成，这些文件描述了Kubernetes部署应用时所需要的资源。通常将整个chart打包成tar包，而且标注上版本信息，便于Helm部署。
- Repository：Chart的存储库。
- Release：Chart的运行实例，当Chart被安装到Kubernetes集群，就会生成一个Release。Chart可以多次安装到同一个集群，每次安装都是一个新的Release。例如，如果需要在集群上安装两个MySQL，则可以在群集中运行两个MySQL的Chart，这将运行两个MySQL的Release。

Helm包括两个组件，Helm客户端和Tiller服务端。 

Helm客户端是终端用户使用的命令行工具。Tiller服务端运行在Kubernetes集群中，它会处理Helm客户端的请求，与Kubernetes API Server交互。简单地讲，Helm客户端负责管理Chart，Tiller服务端负责管理Release。

![Helm架构](Kubernetes/Helm架构.jpg)

## 安装Helm

### Helm客户端安装

通常将Helm客户端安装在能够执行`kubectl`命令的节点上。

下载相应版本Helm：https://github.com/helm/helm/releases

例如：

```bash
$ wget https://storage.googleapis.com/kubernetes-helm/helm-v2.9.1-linux-amd64.tar.gz
$ tar -zxvf helm-v2.9.1-linux-amd64.tar.gz
$ mv linux-amd64/helm /usr/local/bin/helm
$ helm help
```

为了提高使用命令行的效率，建议安装helm的bash命令补全脚本：

```bash
$ helm completion bash > .helmrc
$ echo "source .helmrc" >> .bashrc
```

### Tiller服务器安装

Tiller是Helm的服务器部分，通常在您的Kubernetes集群内部运行。但是为了开发，它也可以在本地运行，并配置为与远程Kubernetes集群通信。

安装Tiller只需要：

```bash
$ helm init
```

Tiller本身也是作为容器化应用运行在Kubernetes集群上。 

上面的默认安装，将Tiller安装在`kubectl config view`或`kubectl config current-context`所指定的集群内，并且安装在`kube--system `命名空间中。可以使用`--kube-context `选项指定特定的集群，使用`--tiller-namespace `选项指定要安装到哪个命名空间，使用`--tiller-image `选项指定Tiller的特定镜像。

默认情况下，安装Tiller时，它没有启用身份验证。 

可以使用`kubectl get`命令来查看Tiller的Pod、Service、Deployment等信息。

> 安装过程中，会从`gcr.io/kubernetes-helm/tiller`拉取镜像。如果由于网络问题无法访问镜像地址，则可以通过`-i`或`--tiller-image`选项自己指定tiller镜像。例如：
>
> ```bash
> $ helm init --upgrade -i fishead/gcr.io.kubernetes-helm.tiller:v2.9.1
> ```
>
> 如果是首次安装，可以省略`--upgrade`选项。

#### 基于角色的访问控制

如果您的群集启用了基于角色的访问控制（RBAC），则可能需要先配置服务帐户和角色，然后才能继续使用Helm完成各种操作。

##### 管理整个集群资源

首先，要为Tiller创建一个服务帐户：

```bash
$ kubectl create serviceaccount tiller --namespace kube-system
```

然后，将服务帐户`tiller`绑定到集群角色`cluster-admin`：

```bash
$ kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
```

上面两步，也可以使用YAML定义文件创建：rbac-config.yaml 

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

然后，执行：

```bash
$ kubectl create -f rbac-config.yaml
```

创建完成服务帐户和集群角色绑定后，如果这时Tiller服务器还没安装，则使用下列命令安装Tiller服务器：

```bash
$ helm init --service-account tiller
```

如果已经安装过Tiller服务器，则需要将服务帐户添加到`tiller-deploy`中：

```bash
$ kubectl patch deployments/tiller-deploy -n kube-system -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
```

##### 限制Tiller只管理某个命名空间的资源

在上面的示例中，我们为Tiller提供了对整个群集的管理访问权限。您根本不需要授予Tiller `cluster-admin `访问权限。您可以指定Role和RoleBinding来将Tiller的范围限制到特定的命名空间，而不是指定ClusterRole或ClusterRoleBinding。

假设，我们希望Tiller只能管理`tiller-world`命名空间下的所有资源，则在该命名空间下创建一个服务帐户：

```bash
$ kubectl create serviceaccount tiller --namespace tiller-world
```

然后，定义一个角色，允许Tiller管理`tiller-world`中的所有资源。例如：role-tiller.yaml 

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: tiller-manager
  namespace: tiller-world
rules:
- apiGroups: ["", "batch", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
```

 创建`tiller-manager`角色：

```bash
$ kubectl create -f role-tiller.yaml
```

接着将`tiller-manager`角色与之前创建的服务帐户`tiller`绑定：rolebinding-tiller.yaml 

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: tiller-binding
  namespace: tiller-world
subjects:
- kind: ServiceAccount
  name: tiller
  namespace: tiller-world
roleRef:
  kind: Role
  name: tiller-manager
  apiGroup: rbac.authorization.k8s.io
```

创建`tiller-binding`绑定：

```bash
$ kubectl create -f rolebinding-tiller.yaml
```

之后，可以使用该服务帐户和命名空间安装Tiller服务器：

```bash
$ helm init --service-account tiller --tiller-namespace tiller-world
```

在安装Chart时，也要指定命名空间：

```bash
$ helm install nginx --tiller-namespace tiller-world --namespace tiller-world
```

##### 准许Tiller对其他命名资源的管理

假设，我们在命名空间`myorg-system`中安装Tiller，并允许Tiller在命名空间`myorg-users`中部署资源。



#### 升级Tiller

```bash
$ helm init --upgrade
```

#### 卸载Tiller

由于Tiller将其数据存储在Kubernetes ConfigMaps中，因此您可以安全地删除并重新安装Tiller，而无需担心丢失任何数据。删除Tiller的推荐方法是使用`kubectl delete deployment tiller-deploy --namespace kube-system`，或更简洁的`helm reset`（如果已经有Releases被安装，或者Tiller未安装成功，则还要加上`-f`选项）。 

### 使用TLS/SSL保护Helm

#### 为Tiller创建证书

创建证书签名请求：

```bash
$ cat > tiller-csr.json <<EOF
{
  "CN": "tiller-server",
  "hosts": [
    "127.0.0.1",
    "172.16.160.121",
    "172.16.160.122",
    "172.16.160.123"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "Fujian",
      "L": "Xiamen",
      "O": "k8s",
      "OU": "Maks"
    }
  ]
}
EOF
```

生成证书和私钥：

```bash
$ cfssl gencert -ca=/etc/kubernetes/cert/ca.pem \
  -ca-key=/etc/kubernetes/cert/ca-key.pem \
  -config=/etc/kubernetes/cert/ca-config.json \
  -profile=kubernetes tiller-csr.json | cfssljson -bare tiller
```

#### 为Helm客户端创建证书

创建证书签名请求：

```bash
$ cat > helm-csr.json <<EOF
{
  "CN": "helm-client",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "Fujian",
      "L": "Xiamen",
      "O": "k8s",
      "OU": "Maks"
    }
  ]
}
EOF
```

生成证书和私钥：

```bash
$ cfssl gencert -ca=/etc/kubernetes/cert/ca.pem \
  -ca-key=/etc/kubernetes/cert/ca-key.pem \
  -config=/etc/kubernetes/cert/ca-config.json \
  -profile=kubernetes helm-csr.json | cfssljson -bare helm
```

#### 使用SSL配置安装Tiller

```bash
$ helm init --tiller-tls --tiller-tls-cert ./tiller.pem --tiller-tls-key ./tiller-key.pem --tiller-tls-verify --tls-ca-cert ca.pem --service-account tiller
```

#### 使用SSL配置Helm客户端

方式一：每条helm命令都带上SSL配置。例如：

```bash
$ helm ls --tls --tls-ca-cert ca.pem --tls-cert helm.pem --tls-key helm-key.pem
```

方式二：

将Helm证书和私钥复制到`$HELM_HOME`（通常为`~/.helm`）下：

```bash
$ cp ca.pem $HELM_HOME/ca.pem
$ cp helm.pem $HELM_HOME/cert.pem
$ cp helm-key.pem $HELM_HOME/key.pem
```

这样，每条需要与Tiller交互的helm命令都要带上`--tls`选项（启用TLS/SSL）即可：

```bash
$ helm ls --tls
```

如果在执行Helm命令时，出现“unable to do port forwarding: socat not found”，则表示缺少socat软件包。执行下列命令安装（CentOS）：

```bash
$ yum install socat
```

## 使用Helm

### 查看帮助

```bash
$ helm --help
$ helm init -h
```

### 查找可用的Chart

查看当前可安装的Chart：

```bash
$ helm search
```

关键字搜索Chart：

```bash
$ helm search mysql
```

### 查看Chart详情

```bash
$ helm inspect stable/mariadb
$ helm inspect values stable/mariabd     # 查看Chart的values.yaml的内容
```

### 安装Chart

例如通过官方存储库安装mysql：

```bash
$ helm repo update              # Make sure we get the latest list of charts
$ helm install stable/mysql
```

helm会自动为新创建的Release提供一个名字。如果希望自己指定Release名字，则可以加上`--name`选项：

```bash
$ helm install stable/mysql --name mysql-release
```

一旦安装了某个Chart，就可以在`~/.helm/cache/archive/`中找到该Chart的tar包。

可以通过`--namespace`选项来指定将Release发布到哪个Kubernetes命名空间中。

可以通过`--version`选项选择特定的Chart版本：

```bash
$ helm install stable/mysql --version 0.8.2
```

没指定`--version`选项，则选择最新版本的Chart。



除了通过官方Chart存储库安装Chart外，Helm还支持如下安装：

通过tar包安装：

```bash
$ helm install ./nginx-1.2.3.tgz   # 使用本地的tar包安装
$ helm install https://example.com/charts/nginx-1.2.3.tgz   # 通过远程tar包安装
```

通过本地Chart目录安装 ：

```bash
$ helm install ./nginx
```



### 列出已安装的Release

```bash
$ helm ls
或者
$ helm list
```

### 查看Release的状态

```bash
$ helm status mysql-release
```

### 更新Release

Release发布后可以执行`helm upgrade`对其进行升级：

```bash
$ helm upgrade --set imageTag=5.7.15 mysql-release stable/mysql
```

### 列出Release的所有修订历史

```bash
$ helm history mysql-release
```

### 回滚Release

通过`helm rollback`可以回滚到上面`helm histroy`列出的任意修订版本：

```bash
$ helm rollback mysql-release 1
```

### 删除Release

```bash
$ helm delete mysql-release
```

## Charts

Chart是Helm的应用打包格式，它由一系列文件组成，通常整个Chart被打成tar包。这些文件描述了Kubernetes部署应用时所需要的资源，比如 Service、Deployment、PersistentVolumeClaim、Secret、ConfigMap等。

Chart可以非常简单，只用于部署一个服务；也可以非常复杂，部署整个应用。

### Chart目录结构

```
├── charts/              # 可选：包含该Chart所依赖的其他Chart的目录
├── Chart.yaml           # 包含该Chart信息的YAML文件
├── LICENSE              # 可选：包含该Chart许可信息的文本文件
├── README.md            # 可选：一个人类可读的README文件
├── requirements.yaml    # 可选：列出该Chart依赖的YAML文件
├── templates            # 包含Kubernetes manifest文件的模板
│   ├── deployment.yaml
│   ├── _helpers.tpl     # 包含子模板的定义
│   ├── ingress.yaml
│   ├── NOTES.txt        # 可选：该Chart的简易使用文档
│   └── service.yaml
└── values.yaml          # 包含该Chart默认配置值
```

除了上述列出的文件外，其他任何文件或目录将保留原样。

### 创建Chart

```bash
$ helm create mychart
```

Chart.yaml：

```yaml
apiVersion: The chart API version, always "v1" (required)
name: The name of the chart (required)
version: A SemVer 2 version (https://semver.org/，required)
kubeVersion: A SemVer range of compatible Kubernetes versions (optional)
description: A single-sentence description of this project (optional)
keywords:
  - A list of keywords about this project (optional)
home: The URL of this project's home page (optional)
sources:
  - A list of URLs to source code for this project (optional)
maintainers: # (optional)
  - name: The maintainer's name (required for each maintainer)
    email: The maintainer's email (optional for each maintainer)
    url: A URL for the maintainer (optional for each maintainer)
engine: gotpl # The name of the template engine (optional, defaults to gotpl)
icon: A URL to an SVG or PNG image to be used as an icon (optional).
appVersion: The version of the app that this contains (optional). This needn't be SemVer.
deprecated: Whether this chart is deprecated (optional, boolean)
tillerVersion: The version of Tiller that this chart requires. This should be expressed as a SemVer range: ">2.0.0" (optional)
```

### Chart依赖

在Helm中，一个Chart可能依赖于任意多的其他Charts。这些依赖项可以通过`requirements.yaml`文件动态链接，或者将依赖的Charts复制到`charts/`目录并手动管理。 推荐使用`requirements.yaml`文件来声明依赖项。

在安装过程中，依赖的Charts也会被一起安装。

#### 使用`requirements.yaml`文件管理依赖（推荐）

requirements.yaml：

```yaml
dependencies:
  - name: apache
    version: 1.2.3
    repository: http://example.com/charts    # Note that you must also use "helm repo add" to add that repo locally.
  - name: mysql
    version: 3.2.1
    repository: http://another.example.com/charts
```

一旦配置了依赖项，就可以运行 `helm dependency update` ，它将根据`requirements.yaml` 上的配置，自动将所有依赖的Charts下载到`charts/` 目录中。 

```
├── charts/
│   ├── apache-1.2.3.tgz
│   └── mysql-3.2.1.tgz
```

#### 通过`charts/`目录手动管理依赖

直接将Chart需要的依赖Charts复制到`charts/`即可。依赖Charts可以是Chart存档（如foo-1.2.3.tgz）或解压缩的Chart目录。但它的名称不能以`_`或`..`开头，Chart加载器会忽略这些文件。 

要将依赖项下载到`charts/`目录中，可使用`helm fetch`命令。

### Chart的模板

Chart的所有模板文件保存在`templates/`目录中，Helm通过这些模板文件来创建Kubernetes的资源定义文件。

在Chart模板文件中使用形如`{{…}}`的[Go模板语言](https://golang.org/pkg/text/template/)来编写。

下面是一个`ReplicationController`的模板文件：

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: deis-database
  namespace: deis
  labels:
    heritage: deis
spec:
  replicas: 1
  selector:
    app: deis-database
  template:
    metadata:
      labels:
        app: deis-database
    spec:
      serviceAccount: deis-database
      containers:
        - name: deis-database
          image: {{.Values.imageRegistry}}/postgres:{{.Values.dockerTag}}
          imagePullPolicy: {{.Values.pullPolicy}}
          ports:
            - containerPort: 5432
          env:
            - name: DATABASE_STORAGE
              value: {{default "minio" .Values.storage}}
```

#### 预定义对象

##### 自定义值

在模板中，可以通过`.Values`对象可访问如下方式提供的自定义值：

- 通过`values.yaml` 定义的属性。（优先级最低，通常用于提供默认值）

  ```yaml
  imageRegistry: "quay.io/deis"
  dockerTag: "latest"
  pullPolicy: "Always"
  storage: "s3"
  ```

- 使用`-f`或`--values`选项，为命令`helm install`或`helm upgrade`指定一个YAML文件。它定义的属性将覆盖`values.yaml`中的同名定义。例如：

  ```bash
  $ helm install -f myvalues.yaml -f override.yaml stable/redis
  ```

  如果在同一条命令中，多次使用`-f`指定YAML文件，则这些自己指定的YAML文件将与默认的`values.yaml`合并。并且在多个YAML文件中重复出现的属性，越后面的指定的优先级越高，而`values.yaml`优先级总是最低的。

- 通过使用`--set`或`--set-string`选项定义的属性：

  ```bash
  $ helm install --set-string long_int=1234567890 ./redis
  $ helm upgrade --set foo=bar --set foo=newbar redis ./redis
  ```

  `--set-string`选项强制参数值是字符串。

  如果在同一条命令中，多次使用`--set`或`--set-string`选项指定YAML文件，则越后面的指定的优先级越高。

  ###### 值的作用域

  假设下面是`WordPress`Chart的`values.yaml`文件，并且`WordPress`Chart依赖于`mysql`Chart和`apache`Chart。

  ```yaml
  title: "My WordPress Site"   # 只有WordPress Chart才能访问（通过“.Values.title”访问）
  
  global:  # 这是全局属性，可以被WordPress及它的依赖Chart所访问（通过“.Values.global.app”）
    app: MyWordPress
  
  mysql:  # mysql Chart只能访问这一命名空间下的属性（例如“.Values.password”），WordPress Chart也可以访问该命名空间下的属性（例如“.Values.mysql.password”）。
    global:
      app: AnotherWordPress
    max_connections: 100 
    password: "secret"
  
  apache:
    port: 8080 
  ```

  父级Chart定义的全局属性优先于子级Chart定义的全局属性。也就是说全局属性只能向下传递。因此，通过`.Values.global.app`总是`MyWordPress`。

##### Release对象

- `.Release.Name`：Release的名称。
- `.Release.Time` ：Release最后更新时间，这将匹配Release对象上的Last Released时间 。
- `.Release.Namespace`：Release所在的命名空间。
- `.Release.Service`：管理该Release的服务，通常这是Tiller。
- `.Release.IsUpgrade`：如果当前操作是升级或回滚，则设置为true。 
- `.Release.IsInstall`：如果当前操作是安装，则设置为true。
- `.Release.Revision`：Release的修订号。它从1开始，随着每次`helm upgrade`的执行而递增。 

##### Chart对象

`.Chart`对象用于访问`Chart.yaml`中定义的属性。例如：`.Chart.Version`。

> 自定义属性要放在`values.yaml`中，而不是`Chart.yaml`中。在`Chart.yaml`中只能放置Helm预定义的属性，所有未知属性将被忽略。

##### File对象

##### Capabilities对象

```
{{.Capabilities.KubeVersion}}：用于访问Kubernetes版本。

{{.Capabilities.TillerVersion}}：用于访问Tiller版本。

{{Capabilities.APIVersions.Has "batch/v1"}}：用于访问Kubernetes API版本。
```

#### 子模板

如果存在一些信息多个模板都会用到，则可在`templates/_helpers.tpl`中将其定义为子模板，然后通过`templates`函数引用。

service.yaml：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ template "mychart.fullname" . }}
  labels:
    app: {{ template "mychart.name" . }}
    chart: {{ template "mychart.chart" . }}
    release: {{ .Release.Name }}
    heritage: {{ .Release.Service }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app: {{ template "mychart.name" . }}
    release: {{ .Release.Name }}
```

_helpers.tpl：

```
{{/* vim: set filetype=mustache: */}}
{{/*
Expand the name of the chart.
*/}}
{{- define "mychart.name" -}}
{{- default .Chart.Name .Values.nameOverride | trunc 63 | trimSuffix "-" -}}
{{- end -}}

{{/*
Create a default fully qualified app name.
We truncate at 63 chars because some Kubernetes name fields are limited to this (by the DNS naming spec).
If release name contains chart name it will be used as a full name.
*/}}
{{- define "mychart.fullname" -}}
{{- if .Values.fullnameOverride -}}
{{- .Values.fullnameOverride | trunc 63 | trimSuffix "-" -}}
{{- else -}}
{{- $name := default .Chart.Name .Values.nameOverride -}}
{{- if contains $name .Release.Name -}}
{{- .Release.Name | trunc 63 | trimSuffix "-" -}}
{{- else -}}
{{- printf "%s-%s" .Release.Name $name | trunc 63 | trimSuffix "-" -}}
{{- end -}}
{{- end -}}
{{- end -}}

{{/*
Create chart name and version as used by the chart label.
*/}}
{{- define "mychart.chart" -}}
{{- printf "%s-%s" .Chart.Name .Chart.Version | replace "+" "_" | trunc 63 | trimSuffix "-" -}}
{{- end -}}
```

### 调试Chart

`helm lint`用于检查Chart的所有工件语法是否正确：

```bash
$ helm lint ./mychart
```

`helm install --dry-run --debug`会模拟安装Chart，并输出每个模板生成的YAML文件：

```bash
$ helm install --dry-run --debug mychart
```

### 打包Chart

存储库中的包名：`Chart名称-版本.tgz`。 例如：`nginx-1.2.3.tgz`。

```bash
$ helm package ./nginx
```

该命令也会自动将生成的`nginx-1.2.3.tgz`添加到`local`存储库中。

### Chart存储库

Helm安装时已经默认配置好了两个存储库：stable和local。stable是官方存储库，local是用户本地存储库（位于`~/.helm/repository/local` 下）。

#### 搭建自己的Chart存储库

任何HTTP Server都可以用作Chart的存储库。例如：（假设位于节点：192.168.56.106）

```bash
$ mkdir /var/www
$ docker run -d -p 8080:80 -v /var/www:/usr/local/apache2/htdocs/ httpd
```

然后，通过执行`helm repo index`生成存储库的`index.yaml`文件：

```bash
$ mkdir myrepo
$ mv mychart-0.1.0.tgz myrepo/
$ helm repo index myrepo/ --url http://192.168.56.106:8080/charts
```

Helm会扫描`myrepo`目录中所有tgz包并生成`index.yaml` （记录了当前仓库中所有Chart信息）。`--url`指定新存储库的访问路径。

接着，将`myrepo`目录下的所有tgz包和`index.yaml`文件上传到`192.168.56.106`服务器的`/var/www/charts`目录下。

#### 向Helm注册Chart存储库

用户可以通过`helm repo add`向Helm添加更多存储库，比如企业私有存储库等。

```bash
$ helm repo add newrepo http://192.168.56.106:8080/charts
```

#### 列出所有已注册的Chart存储库

可以通过`helm repo list`查看所有已经向Helm注册的Chart存储库。（所有Chart存储库的本地缓存位置是`~/.helm/repository/cache` 下）

#### 安装新存储库中的Chart

```bash
$ helm install newrepo/mychart
或者
$ helm install --repo http://192.168.56.106:8080/charts/ mychart   # 该方法无需先注册Chart存储库
```

#### 向存储库中添加新的Chart

以后只要向存储库上传新的Chart，那么用`helm repo update`更新一下存储库的`index.yaml`即可。

```bash
$ helm repo update
```

# Dashboard

Kubernetes Dashboard是一个基于Web的应用，它提供了kubectl的绝大部分功能。

> 安装成功后：
>
> 1. 需要将根证书 ca.pem 导入**客户**操作系统（不是Kubernetes集群服务器），并设置永久信任。这样，通过HTTPS访问Dashboard时，就不会出现“不安全”警告。
> 2. 同时为自己的浏览器（不是在Kubernetes集群服务器）生成一个 client 证书，并导入该证书。这样，通过浏览器访问Dashboard时，就不会出现”401 Unauthorized“提示。
>
> 可以通过`kubectl cluster-info `来获取Kubernetes Dashboard的访问地址。
>
> 用浏览器打开Kubernetes Dashboard的访问地址后，选择使用token 或 kubeconfig 进行登陆。

# 集群监控

## Metrics Server

K8S从1.8版本开始，CPU、内存等资源的指标信息可以通过 Metrics API来获取，用户可以直接获取这些指标信息（例如通过执行kubect top命令），HPA使用这些metics信息来实现动态伸缩。 

[Metrics Server](https://github.com/kubernetes-incubator/metrics-server) 实现了Resource Metrics API，是集群范围资源使用数据的聚合器，它从每个节点上的 Kubelet Summary API 中采集指标信息。 

> 原来的Heapster已经废弃。

## Prometheus Operator

# 日志管理

# Istio

# Deis

[Deis](https://deis.com/) 是一个Kubernetes的原生PaaS。