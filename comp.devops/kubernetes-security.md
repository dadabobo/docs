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


#### Kubenetes 认证
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

## kube-apiserver 应用客户端（kube-controller-manager）的参数 或 kubeconfig 配置文件参数
--root-ca-file                      = /etc/kubernetes/ssl/ca.pem    
                # 用来对 kube-apiserver 证书进行校验，被用于 Service Account。
--service-account-private-key-file  = /etc/kubernetes/ssl/ca-key.pem
                # 用于给 Service Account Token 签名的 PEM 编码的 RSA 或 ECDSA 私钥文件。

# 指定的证书和私钥文件用来签名为 TLS BootStrap 创建的证书和私钥；
--cluster-signing-cert-file         = /etc/kubernetes/ssl/ca.pem
--cluster-signing-key-file          = /etc/kubernetes/ssl/ca-key.pem

## kube-apiserver 应用客户端（kube-scheduler）的参数 或 kubeconfig 配置文件参数
--certificate-authority = /etc/kubernetes/ssl/ca.pem              # 根证书
--client-certificate    = /etc/kubernetes/ssl/scheduler.pem       # 客户端证书
--client-key            = /etc/kubernetes/ssl/scheduler-key.pem   # 客户端私钥

## kube-apiserver 应用客户端（kube-proxy）的参数 或 kubeconfig 配置文件参数
--certificate-authority = /etc/kubernetes/ssl/ca.pem              # 根证书
--client-certificate    = /etc/kubernetes/ssl/kube-proxy.pem      # 客户端证书
--client-key            = /etc/kubernetes/ssl/kube-proxy-key.pem  # 客户端私钥
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
