## Kubeconfig
* [Organizing Cluster Access Using kubeconfig Files](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)
* [Configure Access to Multiple Clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)
* [创建 kubeconfig 文件](https://jimmysong.io/kubernetes-handbook/practice/create-kubeconfig.html)

使用kubeconfig文件来组织关于集群，用户，命名空间和身份验证机制的信息。`kubectl`命令行工具使用kubeconfig文件，找到它需要选择的一个集群，与集群的API服务器进行通信。
用于配置对群集的访问的文件称为 `kubeconfig` 文件。这是引用配置文件的通用方式。这并不意味着有一个名为的文件kubeconfig。
默认情况下，kubectl 在 `$HOME/.kube` 目录中查找指定config的文件。您可以通过设置 `KUBECONFIG` 环境变量或设置 `--kubeconfig` 标志来指定其他kubeconfig文件。
* 支持多个群集，用户和认证机制
* 每个上下文有三个参数：集群，命名空间和用户。

#### 定义群集，用户和上下文
两个集群，一个用于开发工作(`development`)，另一个用于临时工作(`scratch`)。
* 在`development`集群中，前端开发人员在名为空间中工作`frontend`，而您的存储开发人员在名为`storage`的空间中工作。
* 在您的`scratch`群集中，开发人员使用默认的命名空间，或者他们认为合适的时候创建辅助命名空间。
* 访问开发群集(`development`)需要通过证书进行身份验证。访问临时群集(`scratch`)需要通过用户名和密码进行认证。

配置文件(`config-demo`)描述集群，用户和上下文。文件具有描述`两个群集`，`两个用户`和`三个上下文`的框架。
```yaml
apiVersion: v1
kind: Config
preferences: {}

clusters:
- cluster:
  name: development
- cluster:
  name: scratch

users:
- name: developer
- name: experimenter

contexts:
- context:
  name: dev-frontend
- context:
  name: dev-storage
- context:
  name: exp-scratch
```
使用 kubectl 配置 kubeconfig
```bash
## 将群集详细信息添加到您的配置文件中
kubectl config --kubeconfig=config-demo set-cluster development --server=https://1.2.3.4 --certificate-authority=fake-ca-file
kubectl config --kubeconfig=config-demo set-cluster scratch --server=https://5.6.7.8 --insecure-skip-tls-verify

## 将用户详细信息添加到配置文件中
kubectl config --kubeconfig=config-demo set-credentials developer --client-certificate=fake-cert-file --client-key=fake-key-seefile
kubectl config --kubeconfig=config-demo set-credentials experimenter --username=exp --password=some-password

## 将上下文详细信息添加到配置文件中
kubectl config --kubeconfig=config-demo set-context dev-frontend --cluster=development --namespace=frontend --user=developer
kubectl config --kubeconfig=config-demo set-context dev-storage --cluster=development --namespace=storage --user=developer
kubectl config --kubeconfig=config-demo set-context exp-scratch --cluster=scratch --namespace=default --user=experimenter
```

打开 kubeconfig文件(`config-demo`)以查看添加的详细信息
`kubectl config --kubeconfig=config-demo view`

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority: fake-ca-file
    server: https://1.2.3.4
  name: development
- cluster:
    insecure-skip-tls-verify: true
    server: https://5.6.7.8
  name: scratch
contexts:
- context:
    cluster: development
    namespace: frontend
    user: developer
  name: dev-frontend
- context:
    cluster: development
    namespace: storage
    user: developer
  name: dev-storage
- context:
    cluster: scratch
    namespace: default
    user: experimenter
  name: exp-scratch
current-context: ""
kind: Config
preferences: {}
users:
- name: developer
  user:
    client-certificate: fake-cert-file
    client-key: fake-key-file
- name: experimenter
  user:
    password: some-password
    username: exp
```
每个上下文都是一个三元组（集群，用户，命名空间）。
例如， `dev-frontend`上下文说，使用 `developer` 用户的凭据访问 `development` 群集的 `frontend` 名称空间。

设置当前上下文
```bash
kubectl config --kubeconfig=config-demo use-context dev-frontend

# 要仅查看与当前上下文相关的配置信息，请使用该--minify标志。
kubectl config --kubeconfig=config-demo view --minify
```

#### kubeconfig 示例解析
```yaml
apiVersion: v1
kind: Config
preferences:
  colors: true
```
```yaml  
## -------------- clusetr -------------
clusters:
- cluster:
    api-version: v1
    server: http://cow.org:8080
  name: cow-cluster
- cluster:
    certificate-authority: path_to_my_cafile
    server: https://horse.org:4443
  name: horse-cluster
- cluster:
    insecure-skip-tls-verify: true
    server: https://pig.org:443
  name: pig-cluster
```
* `cluster` 中包含 kubernetes 集群的端点数据，包括 kubernetes apiserver 的完整 url 以及集群的证书颁发机构或者当集群的服务证书未被系统信任的证书颁发机构签名时，设置`insecure-skip-tls-verify: true`。
* `cluster` 的名称（昵称）作为该 kubeconfig 文件中的集群字典的 key。 您可以使用 `kubectl config set-cluster` 添加或修改 `cluster` 条目。

```yaml
## -------------- user -------------
users:
- name: blue-user
  user:
    token: blue-token
- name: green-user
  user:
    client-certificate: path_to_my_client_cert
    client-key: path_to_my_client_key
```
* `user` 定义用于向 kubernetes 集群进行身份验证的客户端凭据。在加载/合并 kubeconfig 之后，user 将有一个名称（昵称）作为用户条目列表中的 key。 可用凭证有 `client-certificate`、`client-key`、`token` 和` username/password`。 `username/password` 和 `token` 是二者只能选择一个，但 `client-certificate` 和 `client-key` 可以分别与它们组合。
* 您可以使用 `kubectl config set-credentials` 添加或者修改 `user` 条目。

```yaml
## -------------- context -------------
contexts:
- context:
    cluster: horse-cluster
    namespace: chisel-ns
    user: green-user
  name: federal-context
- context:
    cluster: pig-cluster
    namespace: saw-ns
    user: black-user
  name: queen-anne-context
```
* `context` 定义了一个命名的 `cluster`、`user`、`namespace` 元组，用于使用提供的认证信息和命名空间将请求发送到指定的集群。 三个都是可选的；仅使用 `cluster`、`user`、`namespace` 之一指定上下文，或指定 none。 未指定的值或在加载的 kubeconfig 中没有相应条目的命名值（例如，如果为上述 kubeconfig 文件指定了 pink-user 的上下文）将被替换为默认值。 有关覆盖/合并行为，请参阅下面的 加载和合并规则。
* 您可以使用 `kubectl config set-context` 添加或修改上下文条目。

```yaml
current-context: federal-context
```
* `current-context` 是昵称或者说是作为 `cluster`、`user`、`namespace` 元组的 ”key“，当 `kubectl` 从该文件中加载配置的时候会被默认使用。您可以在 `kubectl` 命令行里覆盖这些值，通过分别传入` —context=CONTEXT`、 `—cluster=CLUSTER`、`--user=USER` 和 `--namespace=NAMESPACE` 。
* 您可以使用 `kubectl config use-context` 更改 `current-context`。

 
---
#### k8s-centos kubeconfig
文件清单
```yaml
admin.kubeconfig          # (~/.kube/config) 
kubelet.kubeconfig        # (同 admin.kubeconfig)
bootstrap.kubeconfig
kube-proxy.kubeconfig
scheduler.kubeconfig
```

###### TLS Bootstrapping Token
创建 TLS Bootstrapping Token (`token.csv`)
```bash
# export BOOTSTRAP_TOKEN=$(head -c 16 /dev/urandom | od -An -t x | tr -d ' ')
export BOOTSTRAP_TOKEN="9c64d78dbd5afd42316e32d922e2da47"
cat > token.csv <<EOF
${BOOTSTRAP_TOKEN},kubelet-bootstrap,10001,"system:kubelet-bootstrap"
EOF
```

###### kubeconfig
创建 kubectl kubeconfig 文件 (`admin.kubeconfig`、`kubelet.kubeconfig`，`~/.kube/config`)
生成的 `kubeconfig` 被保存到 `~/.kube/config` 文件；
```bash
export KUBE_APISERVER="https://192.168.99.91:6443"
# 设置集群参数
kubectl config set-cluster kubernetes --certificate-authority=/etc/kubernetes/ssl/ca.pem \
  --embed-certs=true --server=${KUBE_APISERVER}
# 设置客户端认证参数
kubectl config set-credentials admin --client-certificate=/etc/kubernetes/ssl/admin.pem \
  --embed-certs=true --client-key=/etc/kubernetes/ssl/admin-key.pem
# 设置上下文参数
kubectl config set-context kubernetes --cluster=kubernetes --user=admin
# 设置默认上下文
kubectl config use-context kubernetes
```

###### bootstrapping
创建 kubelet bootstrapping kubeconfig 文件 (`bootstrap.kubeconfig`)
```bash
cd /etc/kubernetes
export BOOTSTRAP_TOKEN="9c64d78dbd5afd42316e32d922e2da47"
export KUBE_APISERVER="https://192.168.99.91:6443"
# 设置集群参数
kubectl config set-cluster kubernetes --certificate-authority=/etc/kubernetes/ssl/ca.pem 
  --embed-certs=true --server=${KUBE_APISERVER} --kubeconfig=bootstrap.kubeconfig
# 设置客户端认证参数
kubectl config set-credentials kubelet-bootstrap --token=${BOOTSTRAP_TOKEN} \
  --kubeconfig=bootstrap.kubeconfig
# 设置上下文参数
kubectl config set-context default --cluster=kubernetes \
  --user=kubelet-bootstrap --kubeconfig=bootstrap.kubeconfig
# 设置默认上下文
kubectl config use-context default --kubeconfig=bootstrap.kubeconfig
```

###### kube-proxy 
创建 kube-proxy kubeconfig 文件 (`kube-proxy.kubeconfig`)
```bash
export KUBE_APISERVER="https://192.168.99.91:6443"
# 设置集群参数
kubectl config set-cluster kubernetes --certificate-authority=/etc/kubernetes/ssl/ca.pem \
  --embed-certs=true --server=${KUBE_APISERVER} --kubeconfig=kube-proxy.kubeconfig
# 设置客户端认证参数
kubectl config set-credentials kube-proxy --client-certificate=/etc/kubernetes/ssl/kube-proxy.pem \
  --client-key=/etc/kubernetes/ssl/kube-proxy-key.pem 
  --embed-certs=true --kubeconfig=kube-proxy.kubeconfig
# 设置上下文参数
kubectl config set-context default --cluster=kubernetes \
  --user=kube-proxy --kubeconfig=kube-proxy.kubeconfig
# 设置默认上下文
kubectl config use-context default --kubeconfig=kube-proxy.kubeconfig
```

###### scheduler 
创建 scheduler kubeconfig 文件 (`scheduler.conf`)
```bash
export KUBE_APISERVER="https://192.168.99.91:6443"
# 设置集群参数
kubectl config set-cluster kubernetes --certificate-authority=/etc/kubernetes/ssl/ca.pem \
  --embed-certs=true --server=${KUBE_APISERVER} --kubeconfig=scheduler.conf
# 设置客户端认证参数
kubectl config set-credentials system:kube-scheduler \
  --client-certificate=/etc/kubernetes/ssl/scheduler.pem \
  --client-key=/etc/kubernetes/ssl/scheduler-key.pem \
  --embed-certs=true --kubeconfig=scheduler.conf
# 设置上下文参数
kubectl config set-context system:kube-scheduler@kubernetes \
  --cluster=kubernetes --user=system:kube-scheduler --kubeconfig=scheduler.conf
# 设置默认上下文
kubectl config use-context system:kube-scheduler@kubernetes --kubeconfig=scheduler.conf
```
