## Kubernetes 安全认证

#### Service Account
`ServiceAccount` 是 Kubernetes 自动生成的，并会自动挂载到容器的 `/run/secrets/kubernetes.io/serviceaccount` 目录中。

在认证时，`ServiceAccount` 的用户名格式为 `system:serviceaccount:(NAMESPACE): (SERVICEACCOUNT)` ，并从属于两个 `group:system:serviceaccounts` 和 `system:serviceaccounts:(NAMESPACE)` 。

Service account 为 Pod 中的进程提供身份信息。

当您（真人用户）访问集群（例如使用 `kubectl` 命令）时，apiserver 会将您认证为一个特定的 `User Account`（目前通常是 `admin`，除非您的系统管理员自定义了集群配置）。Pod 容器中的进程也可以与 apiserver 联系。 当它们在联系 apiserver 的时候，它们会被认证为一个特定的 `Service Account`（例如default）。

* `--service-account-key-file=/etc/kubernetes/ssl/ca-key.pem`
  服务账号文件，包含PEM编码的x509 RSA或ECDSA私钥或公钥的文件，用于验证 `ServiceAccount` 令牌。如果未指定则使用 `--tls-private-key-file`。指定的文件可以包含多个键，并且可以使用不同的文件多次指定该标志。
  File containing PEM-encoded x509 RSA or ECDSA private or public keys, used to verify ServiceAccount tokens. If unspecified, `--tls-private-key-file` is used. The specified file can contain multiple keys, and the flag can be specified multiple times with different files.

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

---
#### Service Accounts集群管理指南
###### User Accounts和Service Accounts
基于以下原因，Kubernetes区分了User Accounts和Service Accounts：
* User Accounts针对人，Service Accounts针对运行在Pod的进程；
* User Accounts是全局的，其名字必须在一个集群的所有Namespace中是唯一的。未来的用户资源将不被命令，但是Service Accounts是可以被命名的。
* 通常情况下，集群的User Accounts可以从一个企业数据库同步。在企业数据库中，新建的账户需要特殊权限，而且绑定到复杂业务流程。新建Service Accounts可以更加轻量级，允许集群用户为特殊任务创建Service Accounts（比如，最小权限规则）。
* 对人类和Service Accounts的审核注意事项是不同的。
* 复杂系统的配置包含对系统组件的各种Service Accounts的定义。因为Service Accounts
* 可以创建ad-hoc，可以命名，配置是便携式的。

###### Service Accounts 自动化
三个独立的组件合作实现对Service Accounts的自动化。
* Service Accounts Admission Controller
* Token控制器
* Service Accounts控制器

###### Service Accounts Admission Controller
Pod的修改是Admission Controller插件实现的，该插件是apiserver的一部分。插件的创建和更新，会同步修改Pod。当插件状态是active（大多版本中，默认是active），创建或者修改Pod会遵循以下流程：
* 如果该Pod没有`ServiceAccount`集，将`ServiceAccount`设为default
* `ServiceAccount`必须有存在的Pod引用，否则拒绝该`ServiceAccount`
* 如果该Pod不包含任何ImagePullSecrets，然后该`ServiceAccount`的`ImagesPullSecrets`会被加入Pod。
* 添加一个volume到该Pod，包含API访问的令牌。
* 添加一个volume到该Pod的每一个容器，挂载在`/var/run/secrets/kubernetes.io/serviceaccount`

###### Token Controller
TokenController做为 controller-manager 的一部分异步运行。
* 检查`serviceAccount`的创建，并且创建一个关联的`Secret`，允许API访问。
* 检查`serviceAccount`的删除，并且删除所有相关`ServiceAccountToken Secrets`
* 检查额外Secret，确保引用的`ServiceAccount`的存在，并且如果有必要则添加一个Token到该`Secret`。
* 检查`Secret`的删除，如果有必要，从相关的`ServiceAccount`中移除参考信息。

###### 创建额外的 API Token
一个控制循坏要确保每一个Service Accounts存在一个API Token。为一个Service Accounts创建一个额外的API Token，类型是ServiceAccountToken，含有一个注释去引用到对应的个Service Accounts。该控制器使用如下的Token去更新：

`secret.json`
```json
{
  "kind": "Secret",
  "apiVersion": "v1",
  "metadata": {
    "name": "mysecretname",
    "annotations": {
      "kubernetes.io/service-account.name": "myserviceaccount"
    }
  },
  "type": "kubernetes.io/service-account-token"
}
```
```bash
kubectl create -f ./secret.json
kubectl describe secret mysecretname
```

###### 删除、废弃一个个Service Accounts Token
`kubectl delete secret mysecretname`

###### Service Account Controller
Service Account Controller管理Namespace中的ServiceAccount，确保每一个“default”的ServiceAccount存在于每一个活动空间中。


---
## Kubenetes 证书认证

#### 证书认证图解
@import "img/k8s/k8s-https-ca.png"

#### API Server 的 TLS 证书
API Server 启用 HTTPS 需要 TLS 证书。
如果启用了 HTTPS 服务，又没有指定 `-–tls-cert-file` 和 `-–tls-private-key-file` 参数，就会在 `/var/run/kubernetes` 生成一个自签名证书以及 `Key`

API Server 的 TLS  证书、私钥与签发证书的CA： `kubernetes.pem`,`kubernetes-key.pem`,`kubernetes-ca.pem`
```yaml
## kube-apiserver 参数
--tls-cert-file         = /etc/kubernetes/ssl/kubernetes.pem      # apiserver 证书
--tls-private-key-file  = /etc/kubernetes/ssl/kubernetes-key.pem  # apiserver 私钥

## kube-apiserver 应用客户端（kubectl/kube-scheduler/kube-proxy）的参数 或 kubeconfig 配置文件参数
--certificate-authority = /etc/kubernetes/ssl/kubernetes-ca.pem   # 签发 apiserver 证书 的 CA
```

#### API Server 客户证书认证
API Server 客户证书签发CA： `client-ca.pem`。所有客户端证书都应该由这一 CA 签发。

> API Server 证书签发 CA 与 客户证书签发 CA 可以一致或不一致。
> `kubernetes-ca.pem` 与 `client-ca.pem` 可以一致（`ca.pem`）

客户端证书与私钥
* kubectl:   `admin.pem`,`admin-key.pem`
* kube-scheduler : `scheduler.pem`,`scheduler-key.pem`
* kube-proxy: `kube-proxy.pem`,`kube-proxy-key.pem`

Kubernetes API Server 客户证书认证配置
```yaml
## kube-apiserver 参数
--client-ca-file        = /etc/kubernetes/ssl/client-ca.pem       # 签发 客户端证书 的 CA

## kube-apiserver 应用客户端（kubectl）的参数 或 kubeconfig 配置文件参数
--client-certificate    = /etc/kubernetes/ssl/admin.pem           # 客户端（kubectl）证书
--client-key            = /etc/kubernetes/ssl/admin-key.pem       # 客户端（kubectl）私钥

## kube-apiserver 应用客户端（kube-scheduler）的参数 或 kubeconfig 配置文件参数
--client-certificate    = /etc/kubernetes/ssl/scheduler.pem       # 客户端（kube-scheduler）证书
--client-key            = /etc/kubernetes/ssl/scheduler-key.pem   # 客户端（kube-scheduler）私钥

## kube-apiserver 应用客户端（kube-proxy）的参数 或 kubeconfig 配置文件参数
--client-certificate    = /etc/kubernetes/ssl/kube-proxy.pem      # 客户端（kube-proxy）证书
--client-key            = /etc/kubernetes/ssl/kube-proxy-key.pem  # 客户端（kube-proxy）私钥
```

```plantuml
@startuml
interface "HTTPS" as https

rectangle "kube-apiserver"  as apiserver {
  rectangle "--bind-address\n--secure-port\n--tls-cert-file\n--tls-private-key-file\n--client-ca-file\n--token-auth-file" as apis #Azure
}

rectangle "kube-apiserver client"  as apiclient {
  rectangle "--certificate-authority\n--client-certificate\n--client-key" as apic #Azure

  rectangle "kubectl" as kc
  rectangle "kube-scheduler" as ks
  rectangle "kube-controller-manager" as cm
  rectangle "kube-proxy" as kp

  apic -- ks #Moccasin
  apic -- kc #Moccasin
  apic -- cm #Moccasin
  apic -- kp #Moccasin
}

apiserver -left-> https 
https -- apiclient 
@enduml
```


#### 请求 Header 的证书认证（或者：认证代理）
API server 参数
* `–requestheader-allowed-names stringSlice`
* `–requestheader-username-headers` 中指定的 Header 中包含用户名，这一参数的列表确定了允许有效的 `Common Name`，如果这一参数的列表为空，则所有通过 `–requestheader-client-ca-file` 校验的都允许通过。
* `–requestheader-client-ca-file string`
  针对收到的请求，在信任 `-–requestheader-username-headers` 中指定的 Header 里面包含的用户名之前，首先会用这一 CA 对客户证书进行验证。

另外一个设置 Kubernetes 认证的方式就是认证代理。如果你对如何向 API Server 发送用户名和组有很多想法，可以设置一个代理，这一代理会使用 HTTP Header 将用户名和组发送给 API Server。

文档中简单的解释了一下工作方式。代理使用一个客户端证书表明身份，`–requestheader-client-ca-file` 告知 API Server，该证书所属的 CA。

API Server 有太多认证方式了（客户端认证、认证代理、Token 等等），让人很迷惑。建议用户尽量少的同时使用认证方式，便于管理、使用和除错。

#### service account 私钥（不是 CA 签发的）
API Server 参数
* `–service-account-key-file`
  PEM 编码的 X509 RSA 或者 ECDSA 的私钥或者公钥，用于检验 ServiceAccount 的 token。如果没指定的话，会使用 `–tls-private-key-file` 替代。文件中可以包含多个 Key，这一参数可以重复指定多个文件。

Controller Manager 参数
* `–service-account-private-key-file`
  PEM 编码的 X509 RSA 或者 ECDSA Key，用于签署 Service Account Token。

Controller Manager 使用私钥签署 Service Account Token。跟 Kubernetes 中使用的其他私钥不同的是，这个私钥是不支持同一 CA 验证的，因此上，需要给每个 Controller Manager 指定一致的私钥文件。

这个 Key 也不需要什么 CA 来做签署，生成很容易：
`openssl genrsa -out private.key 4096`

然后分发给每个 Controller Manager 和 API Server 就可以了。

使用和 `–tls-private-key-file` 一致的文件是可以工作的——只要你给每个 API Server 用的都是同一个 TLS Key（一般都这么做的吧？）。（这里我假设你运行的一个有高可用支持的，多个 API Server 和多个 Controller Manager同时运行的集群）

如果两个不同的 Controller Manager 用了两个不同的 Key，那就杯具了，他们会用各自的 Key 来生成 Token，最终导致无效判定。我觉得这点不太合理，Kubernetes 应该和其他方面一样，使用 CA 进行管理。通过对源码的阅读，我觉得原因可能是 `jwt-go` 不支持 CA。


#### Kubelet 证书认证
```plantuml
@startuml
interface "HTTPS" as https

rectangle "kubelet"  as kubelet {
  rectangle "--tls-cert-file\n--tls-private-key-file\n--client-ca-file" #Azure
}

rectangle "kube-apiserver"  as api {
  rectangle "--kubelet-certificate-authority\n-–kubelet-client-certificate\n-–kubelet-client-key" #Azure
}

api -- https 
https <- kubelet
@enduml
```

API Server 参数
* `–kubelet-certificate-authority`
  签发 kubelet 服务端证书 的 CA。
* `–kubelet-client-certificate`
  kebelet 客户端证书
* `–kubelet-client-key`
  kebelet 客户端证书私钥

Kubelet 参数
* `–client-ca-file` 
  签发 kubelet 客户端证书的 CA
  请求中的客户端证书如果是由文件中的 CA 签署的，那么他的 `Common Name` 就会被用作 `ID` 进行认证。
* `–tls-cert-file`
  用来提供 HTTPS 服务的 x509 证书（其中也可包含中间人证书）。如果不提供 `–tls-cert-file` 和 `–tls-private-key-file`，就会为主机地址生成一个自签名的证书和对应的 Key，并保存到 `–cert-dir` 目录里。
* `–tls-private-key-file`
  `–tls-cert-file` 对应的 Key
  校验 kubelet 的请求是有用的，因为 Kubelet 的职责就是在主机上执行代码。

这里实际上有两个 CA，这里不准备深入描述，情况和 API Server 是一样的，Kubelet 用 TLS 来进行认证，也支持客户证书认证。
另外还要告知 API Server，用什么 CA 检查 Kubelet 的 TLS，另外用什么证书来跟 Kubelet 通信。

> 再说一次，这两个 CA 是可以不同的。


---
## 认证示例
###### 证书认证
```yaml
## kube-apiserver 参数
--tls-cert-file         = /etc/kubernetes/ssl/kubernetes.pem      # apiserver 私钥
--tls-private-key-file  = /etc/kubernetes/ssl/kubernetes-key.pem  # apiserver 证书
--client-ca-file        = /etc/kubernetes/ssl/admin-ca.pem        # 签发 客户端证书 的 CA

## kube-apiserver 应用客户端（kubectl）的参数 或 kubeconfig 配置文件参数
--certificate-authority = /etc/kubernetes/ssl/kubernetes-ca.pem   # 签发 apiserver 证书 的 CA
--client-certificate    = /etc/kubernetes/ssl/admin.pem           # 客户端（kubectl）证书
--client-key            = /etc/kubernetes/ssl/admin-key.pem       # 客户端（kubectl）私钥

## kube-apiserver 应用客户端（kube-scheduler）的参数 或 kubeconfig 配置文件参数
--certificate-authority = /etc/kubernetes/ssl/kubernetes-ca.pem   # 签发 apiserver 证书 的 CA
--client-certificate    = /etc/kubernetes/ssl/scheduler.pem       # 客户端（kube-scheduler）证书
--client-key            = /etc/kubernetes/ssl/scheduler-key.pem   # 客户端（kube-scheduler）私钥

## kube-apiserver 应用客户端（kube-proxy）的参数 或 kubeconfig 配置文件参数
--certificate-authority = /etc/kubernetes/ssl/ca.pem              # 签发 apiserver 证书 的 CA
--client-certificate    = /etc/kubernetes/ssl/kube-proxy.pem      # 客户端（kube-proxy）证书
--client-key            = /etc/kubernetes/ssl/kube-proxy-key.pem  # 客户端（kube-proxy）私钥
```

```yaml
## kube-apiserver 应用客户端（kube-controller-manager）的参数 或 kubeconfig 配置文件参数
--root-ca-file                      = /etc/kubernetes/ssl/ca.pem    
                # 用来对 kube-apiserver 证书进行校验，被用于 Service Account。
--service-account-private-key-file  = /etc/kubernetes/ssl/ca-key.pem
                # 用于给 Service Account Token 签名的 PEM 编码的 RSA 或 ECDSA 私钥文件。

# 指定的证书和私钥文件用来签名为 TLS BootStrap 创建的证书和私钥；
--cluster-signing-cert-file         = /etc/kubernetes/ssl/ca.pem
--cluster-signing-key-file          = /etc/kubernetes/ssl/ca-key.pem
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
