## Kubernetes Tricks

#### Dashboard 访问问题
[Kubernetes dashboard1.8.0 WebUI安装与配置](http://blog.csdn.net/A632189007/article/details/78840971)
[Accessing Dashboard](https://github.com/kubernetes/dashboard/wiki/Accessing-Dashboard---1.7.X-and-above)

#### Dashboard界面
```
https://masterip:6443/ui 
https://MasterIP:6443/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
```

#### Dashboard system:anonymous问题
访问dashboard网页时，可能出现下面这种报错：
```
{
  "kind": "Status",
  "apiVersion": "v1",
  "metadata": {
    
  },
  "status": "Failure",
  "message": "services \"https:kubernetes-dashboard:\" is forbidden: User \"system:anonymous\" cannot get services/proxy in the namespace \"kube-system\"",
  "reason": "Forbidden",
  "details": {
    "name": "https:kubernetes-dashboard:",
    "kind": "services"
  },
  "code": 403
```

Kubernetes API Server新增了`–anonymous-auth`选项，允许匿名请求访问secure port。没有被其他authentication方法拒绝的请求即Anonymous requests， 这样的匿名请求的`username`为`system:anonymous`, 归属的组为`system:unauthenticated`。并且该选线是默认的。这样一来，当采用chrome浏览器访问dashboard UI时很可能无法弹出用户名、密码输入对话框，导致后续authorization失败。为了保证用户名、密码输入对话框的弹出，需要将`–anonymous-auth`设置为`false`。

解决方法： kube-apiserver 启动选项
```yaml
--anonymous-auth=false
```


#### Unauthorized问题
解决了上面那个问题之后，再度访问dashboard页面，发现还是有问题，出现下面这个问题：
新建 `/etc/kubernetes/basic_auth_file` 文件，并在其中添加：
```
admin,admin,1002
```

kube-apiserver 启动选项
```yaml
--basic-auth-file=/etc/kubernetes/basic_auth_file
```

最后在kubernetes上执行下面这条命令：
```
kubectl create clusterrolebinding login-dashboard-admin --clusterrole=cluster-admin --user=admin
```

如果安装的docker版本为1.13及以上，并且网络畅通，flannel、etcd都正常，但还是会出现getsockopt: connection timed out'的错误，则可能是iptables配置问题。具体问题：
```
Error: 'dial tcp 10.233.50.3:8443: getsockopt: connection timed out
```
docker从1.13版本开始，可能将iptables FORWARD chain的默认策略设置为DROP，从而导致ping其他Node上的Pod IP失败，遇到这种问题时，需要手动设置策略为ACCEPT：
```
sudo iptables -P FORWARD ACCEPT
```

###### 