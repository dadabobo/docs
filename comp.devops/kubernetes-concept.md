## Kubernete Concept
#### 核心原理简单总结
1. **APIserver**
   API通过 apiserver进程提供服务，该进程在master节点上。该进程包括两个端口：本地端口，默认是8080端口和安全端口，默认6443端口；集群内的功能模块通过API server将信息存入Etcd，其他模块通过API server读取这些信息，从而实现模块间的通信。
   * 提供了集群管理的API接口
   * 是集群内各个功能模块之间数据交互和通信的中心枢纽
   * 拥有完备的集群安全机制
2. **Controller Manager（控制器）**
   Controller Manager是集群内部的控制管理中心，负责集群内Node、pod副本、服务端点、命名空间、服务账号、资源定额等的管理并执行自动化修复流程。
   内部包含：
   * Replication Controller：作用是确保任何时候集群中一个资源对象（RC）所关联的Pod都保持一定数量的Pod副本处于正常运行状态。
   * Node controller：负责发现、管理和监控集群中的各个 Node节点。
   * ResourceQuota Controller：提供资源配额管理；
   * Namespace Controller：定时读取Namespace信息，进行修改删除；
   * erviceAccount Controller和Tocken Controller：与安全相关的控制器；
   * Service Controller：监控Service的变化。
   * Endpoints Controller：通过Store来缓存Service和pod信息，监控Service和Pod的变化。
3. **Scheduler（调度模块）**
   Kubernetes scheduler 负责Pod调度的重要功能模块，负责接收Controller Mannager调度的pod （创建的新Pod）按照特定的调度算法策略绑定的集群中合适的Node上，并将绑定信息写入Etcd中。
   默认的调度流程：
   * 预选调度过程，遍历所有的Node节点，筛选出符合要求的候选节点（内置了多种筛选策略）；
   * 确定最优节点，在第一步的基础上采用优先策略计算每个候选节点的积分，积分高的胜出。
4. **Kubelet**
   该进程处理Master节点上下发到本节点的任务，管理Pod及pod中的容器。每个Kubelet都会在API server上注册节点自身的信息，定期向Master节点汇报节点资源的使用情况。Node节点上的Kubelet通过API-server监听到Kubernetes Scheduler产生的Pod绑定事件，然后获取pod清单，下载镜像，并启动容器。
5. **Kube-proxy（代理）**
   Kube-proxy充当kubernetes中Service的负载均衡器和服务代理的角色。service是一组pod的服务抽象，相当于一组pod的LB，负责将请求分发给对应的pod。service会为这个LB提供一个IP，一般称为cluster IP。kube-proxy的作用主要是负责service的实现，具体来说，就是实现了内部从pod到service和外部的从node port向service的访问。


#### Architecture
@import "img/k8s/k8s-post-ccm-arch.png"

@import "img/k8s/k8s-overview.png"

@import "img/k8s/k8s-abbr.png"

#### 集群中服务映射到Pod功能
@import "img/k8s/k8s-service-function.png"

#### Kubernetes集群
@import "img/k8s/k8s-module_cluster.png"

* Master 负责集群的管理。Master 协调集群中的所有行为/活动，例应用的运行、修改、更新等。
* 节点（Node）作为Kubernetes集群中的工作节点，可以是VM虚拟机、物理机。
  每个node上都有一个`Kubelet`，用于管理node节点与Kubernetes Master通信。
  每个Node节点上至少还要运行 `container runtime`。
* Node节点使用master公开的 `Kubernetes API` 与主节点进行通信

@import "img/k8s/k8s-module_nodes.png" {width=450}

@import "img/k8s/k8s-module_rollingupdatesA.png" {width=550}
@import "img/k8s/k8s-module_rollingupdatesB.png" {width=600}
 
##### 概念
@import "img/k8s/k8s-concept.png" {width=600}
@import "img/k8s/k8s-minikube-part.png" {width=600}

#### 资源管理与容器设计模式
Kubernetes通过声明式配置，真正让开发人员能够理解应用的状态，并通过同一份配置可以立马启动一个一模一样的环境，大大提高了应用开发和部署的效率，其中kubernetes设计的多种资源类型可以帮助我们定义应用的运行状态，并使用资源配置来细粒度得明确限制应用的资源使用。

Kubernetes提供了多种资源对象，用户可以根据自己应用的特性加以选择。这些对象有：

类别    |名称 
:------ | :----------- 
资源对象 |Pod、ReplicaSet、ReplicationController、Deployment、StatefulSet、 DaemonSet、Job、CronJob、HorizontalPodAutoscaling
配置对象 |Node、Namespace、Service、Secret、ConfigMap、Ingress、Label、 ThirdPartyResource、 ServiceAccount
存储对象 |Volume、Persistent Volume
策略对象 |SecurityContext、ResourceQuota、LimitRange

在 Kubernetes 系统中，Kubernetes 对象是持久化的条目。Kubernetes 使用这些条目去表示整个集群的状态。特别地，它们描述了如下信息：
* 什么容器化应用在运行（以及在哪个 Node 上）
* 可以被应用使用的资源
* 关于应用如何表现的策略，比如重启策略、升级策略，以及容错策略

Kubernetes 对象是 “目标性记录” -- 一旦创建对象，Kubernetes 系统将持续工作以确保对象存在。通过创建对象，可以有效地告知 Kubernetes 系统，所需要的集群工作负载看起来是什么样子的，这就是 Kubernetes 集群的期望状态。

Kubernetes 中几乎所有重要概念都是资源.
* 集群中的一种资源对象.
* 处于某个命名空间中
* 可以持久化存储到Etcd中
* 资源是有状态的
* 资源是可以关联的
* 资源是可以限定使用配额

---
## 理解Kubernetes对象
[Understanding Kubernetes Objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)

###### kubernetes对象概述
kubernetes中的对象是一些持久化的实体，可以理解为是对集群状态的描述或期望。
包括：
* 集群中哪些node上运行了哪些容器化应用
* 应用的资源是否满足使用
* 应用的执行策略，例如重启策略、更新策略、容错策略等。

kubernetes的对象是一种意图（期望）的记录，kubernetes会始终保持预期创建的对象存在和保持集群运行在预期的状态下。
操作kubernetes对象（增删改查）需要通过kubernetes API，一般有以下几种方式：
* kubectl 命令工具
* Client library的方式，例如 `client-go`

###### Spec and Status
每个kubernetes对象的结构描述都包含spec和status两个部分。
* `spec`：该内容由用户提供，描述用户期望的对象特征及集群状态。
* `status`：该内容由kubernetes集群提供和更新，描述kubernetes对象的实时状态。

任何时候，kubernetes都会控制集群的实时状态 `status` 与用户的预期状态 `spec` 一致。

例如：当你定义 Deployment 的描述文件，指定集群中运行3个实例，那么kubernetes会始终保持集群中运行3个实例，如果任何实例挂掉，kubernetes会自动重建新的实例来保持集群中始终运行用户预期的3个实例。

###### 对象描述文件
当你要创建一个kubernetes对象的时候，需要提供该对象的描述信息spec，来描述你的对象在kubernetes中的预期状态。

一般使用kubernetes API来创建kubernetes对象，其中spec信息可以以JSON的形式存放在request body中，也可以以.yaml文件的形式通过kubectl工具创建。

例如，以下为 Deployment 对象对应的 yaml 文件：
```yaml
piVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

执行 kubectl create 的命令
```bash
# create command
kubectl create -f https://k8s.io/docs/user-guide/nginx-deployment.yaml --record
# output
deployment "nginx-deployment" created
```

###### 必须字段
在对象描述文件.yaml中，必须包含以下字段。
* `apiVersion`：kubernetes API的版本。
* `kind`：kubernetes对象的类型。
* `metadata`：唯一标识该对象的元数据，包括name，UID，可选的namespace。
* `spec`：标识对象的详细信息，不同对象的spec的格式不同，可以嵌套其他对象的字段。

```yaml
apiVersion: v1 # 必须，指定api版本，此值必须在kubectl apiversion中  
kind: Pod      # 必须，指定创建资源的角色/类型  
metadata:      # 必须，资源的元数据/属性  
  name: coredns           # 必须，资源名称
  namespace: kube-system  # 可选，资源命名空间
  labels:                 # 必须，资源标签
    k8s-app: coredns
    kubernetes.io/name: "CoreDNS"
spec:          # 指定该资源的详细内容
  # ...
```


###### Resource
* 名称
* 类型
* Label
* 资源定义模板
* 资源实例
* 资源生命周期
* 事件记录
* 特定的一些动作

Pod
* 名称和IP
* 拥有状态
* 拥有Label
* 一组容器进程
* 一些Volumes

Service
* 名字
* IP地址+Port
* NodePort

ReplicationController
* 标签选择器
* 实例数
* Pod模板
