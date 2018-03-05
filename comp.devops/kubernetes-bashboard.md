### Kubernetes Dashboard
* [Kubernetes Dashboard中的身份认证详解](https://jimmysong.io/posts/kubernetes-dashboard-upgrade/)
* [Kubernetes dashboard1.8.0 WebUI安装与配置](http://blog.csdn.net/A632189007/article/details/78840971)
* [Accessing Dashboard](https://github.com/kubernetes/dashboard/wiki/Accessing-Dashboard---1.7.X-and-above)

访问dashboard 有以下三种方式：
* kubernetes-dashboard 服务暴露了 NodePort，可以使用 http://NodeIP:nodePort 地址访问 dashboard
  只有在单个节点设置中的开发环境中才推荐这种访问仪表板的方式。
* 通过 API server 访问 dashboard（https 6443 端口 和 http 8080端口方式）
  http://192.168.99.91:8080/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
  https://192.168.99.91:6443/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
* 通过 kubectl proxy 访问 dashboard

Service有四种类型：
* ClusterIP：默认类型，自动分配一个仅cluster内部可以访问的虚拟IP 
* NodePort：在ClusterIP基础上为Service在每台机器上绑定一个端口，这样就可以通过 `<NodeIP>:NodePort` 来访问该服务
* LoadBalancer：在NodePort的基础上，借助cloud provider创建一个外部的负载均衡器，并将请求转发到 `<NodeIP>:NodePort` 
* ExternalName：将服务通过DNS CNAME记录方式转发到指定的域名（通过 `spec.externlName` 设定）。需要kube-dns版本在1.7以上。


###### kubectl proxy
kubectl proxy在您的机器和Kubernetes API服务器之间创建代理服务器。默认情况下，它只能在本地访问（从启动它的机器）。
```bash
## 启动本地代理服务器： 127.0.0.1:8001
#  http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
kubectl proxy

## 代理: 192.168.99.91:8086
#  http://192.168.99.91:8086/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
kubectl proxy --address='192.168.99.91' --port=8086 --accept-hosts='^*$'
```


###### NodePort
```yaml
# 默认
kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kube-system
spec:
  ports:
    - port: 8443
      targetPort: 8443
  selector:
    k8s-app: kubernetes-dashboard
  type: NodePort      ## 修改为 NodePort， 默认 ClusterIP
```

* ClusterIP: 
  ```
  $ kubectl -n kube-system get svc kubernetes-dashboard
  NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
  kubernetes-dashboard   ClusterIP   10.254.227.100   <none>        8443/TCP   4s
  ```
* NodePort 
  ```
  $ kubectl -n kube-system get svc kubernetes-dashboard
  NAME                   TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
  kubernetes-dashboard   NodePort   10.254.45.98   <none>        8443:32524/TCP   6m
  ```
  访问 `https://NodeIP:nodePort`
  https://192.168.99.91:32524/

使用的是自签名证书。会显示“您的连接不是私密连接”。

Deployment->spec->template
`spec.containers.args`
```yaml
## 指定证书
args:
  - --tls-key-file=/certs/dashboard.key
  - --tls-cert-file=/certs/dashboard.crt

## 自生成证书 （访问 http://masterip:6443）
args:
  - --auto-generate-certificates
```


### 创建示例账户
[Creating sample user](https://github.com/kubernetes/dashboard/wiki/Creating-sample-user)
在本指南中，我们将了解如何使用Kubernetes的服务帐户机制创建新用户，授予此用户管理权限并使用与此用户绑定的不记名令牌登录到仪表板。

将提供的片段复制到某个xxx.yaml文件并用于kubectl create -f xxx.yaml创建它们。

```yaml
# ------------------- Dashboard Service Account ------------------- #
# 在名称空间 kube-system 中创建 admin-user 服务帐户。
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system

# ------------------ Dashboard ClusterRoleBinding ----------------- #
# Kubernetes版本之间 apiVersion 的 ClusterRoleBinding 资源可能有所不同。
# 从v1.8开始升级为 rbac.authorization.k8s.io/v1。
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
```

获取无记名令牌
`kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')`


### 身份认证

###### RBAC
```yaml
# ------------------- Dashboard Service Account ------------------- #
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kube-system

# ------------------- Dashboard Service Account ------------------- #
---

apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
subjects:
  - kind: ServiceAccount
    name: kubernetes-dashboard
    namespace: kube-system

---

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: kubernetes-dashboard-minimal
  namespace: kube-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: kubernetes-dashboard-minimal
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
```


###### kubernetes-dashboard.yaml
* `spec.containers.args`
  填写 `- --auto-generate-certificates`，用以自动生成dashboard证书，此处不需要填写apiserver地址。
  Uncomment the following line to manually specify Kubernetes API server Host If not specified, Dashboard will attempt to auto discover the API server and connect to it. Uncomment only if the default does not work.
  `- --apiserver-host=http://my-address:port`


@import "./k8s/kubernetes-dashboard.yaml"