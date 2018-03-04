### Kubenetes 安全机制
###### 证书认证
```yaml
## kube-apiserver 参数
--client-ca-file        = /etc/kubernetes/ssl/ca.pem              # 根证书（客户端CA证书）
--tls-cert-file         = /etc/kubernetes/ssl/kubernetes.pem      # 服务端私钥
--tls-private-key-file  = /etc/kubernetes/ssl/kubernetes-key.pem  # 服务端证书

## kube-apiserver 应用客户端（kubectl）的参数 或 kubeconfig 配置文件参数
--certificate-authority = /etc/kubernetes/ssl/ca.pem              # 根证书
--client-certificate    = /etc/kubernetes/ssl/admin.pem           # 客户端证书
--client-key            = /etc/kubernetes/ssl/admin-key.pem       # 客户端私钥

## kube-apiserver 应用客户端（scheduler）的参数 或 kubeconfig 配置文件参数
--certificate-authority = /etc/kubernetes/ssl/ca.pem              # 根证书
--client-certificate    = /etc/kubernetes/ssl/scheduler.pem       # 客户端证书
--client-key            = /etc/kubernetes/ssl/scheduler-key.pem   # 客户端私钥


```

###### Token 认证方式
```yaml
## kube-apiserver 参数
--token-auth-file = /etc/kubernetes/token.csv                     # Token 文件

## Token文件: token.csv 
#  格式： token,user,uid,"group1,group2" （token,用户名,用户UID,组名-可选）
9c64d78dbd5afd42316e32d922e2da47,kubelet-bootstrap,10001,"system:kubelet-bootstrap"

## curl 访问方式
#  HTTP请求中，增加一个Header字段：Authorization，将它的值设置为：Bearer SOMETOKEN
curl $APISERVER/api --header "Authorization: Bearer 9c64d78dbd5afd42316e32d922e2da47" --insecure
curl https://192.168.99.91:6443/api --header "Authorization: Bearer 9c64d78dbd5afd42316e32d922e2da47" --insecure
```

###### 基本认证 Basic auth
```yaml
## kube-apiserver 参数
--basic_auth_file =                                               # 基本认证文件

## 基本认证文件 user.csv
#  格式： password,user,uid,"group1,group2" （口令,用户名,用户UID,组名-可选）
password,user,uid,"group1,group2"

## curl 访问方式： 
#  HTTP请求中，增加一个Header字段：Authorization，将它的值设置为：Basic BASE64ENCODED(USER:PASSWORD)
upass = base64 "Thomas:Thomas"
curl $APISERVER/api --header "Authorization: Basic $upass" --insecure
```

###### 引导Token (Bootstrap Tokens)
引导Token是动态生成的，存储在kube-system namespace的`Secret`中，用来部署新的 Kubernetes集群。

启动引导令牌是一种简单的持有者令牌（Bearer Token），这种令牌是在新建集群或者在现有集群中添加新加新节点时使用的。 它被设计成能够支持 kubeadm，但是也可以被用在其他 context 中以便用户在不使用 kubeadm 的情况下启动集群。它也被设计成可以通过 RBAC 策略，结合 Kubelet TLS Bootstrapping 系统进行工作。

启动引导令牌被定义成一个特定类型的 secrets(bootstrap.kubernetes.io/token)，并存在于 kube-system 命名空间中。然后这些 secrets 会被 API 服务器上的启动引导的认证器读取。 过期的令牌与 TokenCleaner 会被控制管理器一起清除。令牌也会被用于创建特定 configmap 的签名， 而这个 configmap 会通过启动引导签名控制器在 “discovery” 过程中使用。

启动引导令牌使用 `abcdef.0123456789abcdef` 的形式。更加规范地说，它们必须符合正则表达式 `[a-z0-9]{6}\.[a-z0-9]{16}`。
令牌的第一部分是 “Token ID” ，它是公共信息。它被用于引用一个用于认证的令牌而不会泄漏令牌的保密部分。 
第二部分是 “Token Secret”，它应该只能被信任方共享。

使用引导Token需要API Server启动时配置 --experimental-bootstrap-tokenauth ，并且Controller Manager开启TokenCleaner -controllers=*,tokencleaner,bootstrapsigner 。
在使用kubeadm部署Kubernetes时，kubeadm会自动创建默认token，可通过 kubeadm token list 命令查询。

```
--enable-bootstrap-token-auth               # 启动引导令牌认证（Bootstrap Tokens）
```

###### Service Account
ServiceAccount是Kubernetes自动生成的，并会自动挂载到容器 的 /run/secrets/kubernetes.io/serviceaccount 目录中。
在认证时，ServiceAccount的用户名格式为 system:serviceaccount:(NAMESPACE): (SERVICEACCOUNT) ，并从属于两个 group： system:serviceaccounts 和 system:serviceaccounts:(NAMESPACE) 。

Service account为Pod中的进程提供身份信息。

当您（真人用户）访问集群（例如使用kubectl命令）时，apiserver 会将您认证为一个特定的 User Account（目前通常是admin，除非您的系统管理员自定义了集群配置）。Pod 容器中的进程也可以与 apiserver 联系。 当它们在联系 apiserver 的时候，它们会被认证为一个特定的 Service Account（例如default）。


* `--service-account-key-file=/etc/kubernetes/ssl/ca-key.pem`
  服务账号文件，包含PEM编码的x509 RSA或ECDSA私钥或公钥的文件，用于验证ServiceAccount令牌。如果未指定则使用 `--tls-private-key-file`。指定的文件可以包含多个键，并且可以使用不同的文件多次指定该标志。
  File containing PEM-encoded x509 RSA or ECDSA private or public keys, used to verify ServiceAccount tokens. If unspecified,--tls-private-key-file is used. The specified file can contain multiple keys, and the flag can be specified multiple times with different files.

```yaml
## kube-apiserver
--enable-bootstrap-token-auth               # 启动引导令牌认证（Bootstrap Tokens）

--service-account-key-file=/etc/kubernetes/ssl/ca-key.pem

##-------------------------------------------------------------------
## kube-controller-manager
# 用来对 kube-apiserver 证书进行校验，被用于 Service Account。
--root-ca-file=/etc/kubernetes/ssl/ca.pem   

# 用于给 Service Account Token 签名的 PEM 编码的 RSA 或 ECDSA 私钥文件。
--service-account-private-key-file=/etc/kubernetes/ssl/ca-key.pem

# 指定的证书和私钥文件用来签名为 TLS BootStrap 创建的证书和私钥；
--cluster-signing-cert-file=/etc/kubernetes/ssl/ca.pem
--cluster-signing-key-file=/etc/kubernetes/ssl/ca-key.pem
```


----
### Kubernete Concept
###### Kubernetes核心原理简单总结
1. kubernetes APIserver
   API通过 apiserver进程提供服务，该进程在master节点上。该进程包括两个端口：本地端口，默认是8080端口和安全端口，默认6443端口；集群内的功能模块通过API server将信息存入Etcd，其他模块通过API server读取这些信息，从而实现模块间的通信。
   * 提供了集群管理的API接口
   * 是集群内各个功能模块之间数据交互和通信的中心枢纽
   * 拥有完备的集群安全机制
2. Controller Manager（控制器）
   Controller Manager是集群内部的控制管理中心，负责集群内Node、pod副本、服务端点、命名空间、服务账号、资源定额等的管理并执行自动化修复流程。
   内部包含：
   * Replication Controller：作用是确保任何时候集群中一个资源对象（RC）所关联的Pod都保持一定数量的Pod副本处于正常运行状态。
   * Node controller：负责发现、管理和监控集群中的各个 Node节点。
   * ResourceQuota Controller：提供资源配额管理；
   * Namespace Controller：定时读取Namespace信息，进行修改删除；
   * erviceAccount Controller和Tocken Controller：与安全相关的控制器；
   * Service Controller：监控Service的变化。
   * Endpoints Controller：通过Store来缓存Service和pod信息，监控Service和Pod的变化。
3. Kubernetes Scheduler（调度模块）
   Kubernetes scheduler 负责Pod调度的重要功能模块，负责接收Controller Mannager调度的pod （创建的新Pod）按照特定的调度算法策略绑定的集群中合适的Node上，并将绑定信息写入Etcd中。
   默认的调度流程：
   * 预选调度过程，遍历所有的Node节点，筛选出符合要求的候选节点（内置了多种筛选策略）；
   * 确定最优节点，在第一步的基础上采用优先策略计算每个候选节点的积分，积分高的胜出。
4. Kubelet
   该进程处理Master节点上下发到本节点的任务，管理Pod及pod中的容器。每个Kubelet都会在API server上注册节点自身的信息，定期向Master节点汇报节点资源的使用情况。Node节点上的Kubelet通过API-server监听到Kubernetes Scheduler产生的Pod绑定事件，然后获取pod清单，下载镜像，并启动容器。
5. Kube-proxy（代理）
   Kube-proxy充当kubernetes中Service的负载均衡器和服务代理的角色。service是一组pod的服务抽象，相当于一组pod的LB，负责将请求分发给对应的pod。service会为这个LB提供一个IP，一般称为cluster IP。kube-proxy的作用主要是负责service的实现，具体来说，就是实现了内部从pod到service和外部的从node port向service的访问。


###### Architecture
@import "img/k8s/k8s-post-ccm-arch.png"

@import "img/k8s/k8s-overview.png"

@import "img/k8s/k8s-abbr.png"

###### How Services in a Cluster Map to Functions in Pods
@import "img/k8s/k8s-service-function.png"

###### Kubernetes集群
@import "img/k8s/k8s-module_cluster.png"

* Master 负责集群的管理。Master 协调集群中的所有行为/活动，例应用的运行、修改、更新等。
* 节点（Node）作为Kubernetes集群中的工作节点，可以是VM虚拟机、物理机。
  每个node上都有一个`Kubelet`，用于管理node节点与Kubernetes Master通信。
  每个Node节点上至少还要运行 `container runtime`。
* Node节点使用master公开的 `Kubernetes API` 与主节点进行通信

@import "img/k8s/k8s-module_nodes.png" {width=450}

@import "img/k8s/k8s-module_rollingupdatesA.png" {width=550}
@import "img/k8s/k8s-module_rollingupdatesB.png" {width=600}
 
###### 概念
@import "img/k8s/k8s-concept.png" {width=600}
@import "img/k8s/k8s-minikube-part.png" {width=600}

###### Kubernetes中的资源管理与容器设计模式
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

### 理解Kubernetes对象
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


###### Diagram
```plantuml
@startuml
class Resource {
  名称
  类型
  Label
  资源定义模板
  资源实例
  资源生命周期
  事件记录
  特定的一些动作
}

class Label {
  key,value
}

class Master {
  Etcd存储服务(可选)
  API Server进程
  ControllerManager服务进程
  Scheduler服务进程
}

class Node {
  名称和IP
  系统资源信息
  运行DockerEngine服务
  运行K8s的一个守护进程kubelet
  运行K8s的负载均衡器kube-proxy
  拥有状态
}

class Pod {
  名称和IP
  拥有状态
  拥有Label
  一组容器进程
  一些Volumes
}


class Service {
  名字
  IP地址+Port
  NodePort
}

class ReplicationController {
  标签选择器
  实例数
  Pod模板
}

class Namespace {

}


@enduml
```



