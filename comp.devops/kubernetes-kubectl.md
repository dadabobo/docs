## Kubectl
[Overview of kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)

Kubernetes 命令行工具 `kubectl`, 用它将 API server 的 API 包装成简单的命令集供我们使用.

```bash
kubectl api-version     # 显示API版本信息
kubectl cluster-info    # 显示集群信息
kubectl config          # 修改集群的配置信息
kubectl create          # 通过文件名或标准输入创建一个或多个资源
kubectl delete          # 通过文件名、标准输入、资源的ID或标签删除资源
kubectl describe        # 详细描述某个资源的信息
kubectl exec            # 在一个 pod 的一个容器中执行一个命令
kubectl expose          # 将资源对象暴露为 Kubernetes Service
kubectl get             # 显示一个或多个资源的信息
kubectl label           # 修改某个资源上的标签
kubectl logs            # 打印在 pod 中的容器的日志信息
kubectl run             # 在集群中运行一个独立的镜像
kubectl scale           # 调节 Replication Controller 副本数量
```

#### kubectl commmand options
kubectl [command] [options]
* `annotate`
  `kubectl annotate (-f FILENAME | TYPE NAME | TYPE/NAME) KEY_1=VAL_1 ... KEY_N=VAL_N [--overwrite] [--all] [--resource-version=version] [flags]`	
  Add or update the annotations of one or more resources.
* `api-versions` 
  `kubectl api-versions [flags]`	
  List the API versions that are available.
* `apply`	
  `kubectl apply -f FILENAME [flags]`	
  Apply a configuration change to a resource from a file or stdin.
* `attach`
  `kubectl attach POD -c CONTAINER [-i] [-t] [flags]`
  Attach to a running container either to view the output stream or interact with the container (stdin).
* `autoscale`
  `kubectl autoscale (-f FILENAME | TYPE NAME | TYPE/NAME) [--min=MINPODS] --max=MAXPODS [--cpu-percent=CPU] [flags]`	
  Automatically scale the set of pods that are managed by a replication controller.
* `cluster-info` 
  `kubectl cluster-info [flags]`
  Display endpoint information about the master and services in the cluster.
* `config` 
  `kubectl config SUBCOMMAND [flags]`	
  Modifies kubeconfig files. See the individual subcommands for details.
* `create` 
  `kubectl create -f FILENAME [flags]`
  Create one or more resources from a file or stdin.
* `delete` 
  `kubectl delete (-f FILENAME | TYPE [NAME | /NAME | -l label | --all]) [flags]`
  Delete resources either from a file, stdin, or specifying label selectors, names, resource selectors, or resources.
* `describe` 
  `kubectl describe (-f FILENAME | TYPE [NAME_PREFIX | /NAME | -l label]) [flags]`
  Display the detailed state of one or more resources.
* `edit`
  `kubectl edit (-f FILENAME | TYPE NAME | TYPE/NAME) [flags]`
  Edit and update the definition of one or more resources on the server by using the default editor.
* `exec` 
  `kubectl exec POD [-c CONTAINER] [-i] [-t] [flags] [-- COMMAND [args...]]`
  Execute a command against a container in a pod,
* `explain`
  `kubectl explain [--include-extended-apis=true] [--recursive=false] [flags]`
  Get documentation of various resources. For instance pods, nodes, services, etc.
* `expose` 
  `kubectl expose (-f FILENAME | TYPE NAME | TYPE/NAME) [--port=port] [--protocol=TCP|UDP] [--target-port=number-or-name] [--name=name] [----external-ip=external-ip-of-service] [--type=type] [flags]`
  Expose a replication controller, service, or pod as a new Kubernetes service.
* `get` 
  `kubectl get (-f FILENAME | TYPE [NAME | /NAME | -l label]) [--watch] [--sort-by=FIELD] [[-o | --output]=OUTPUT_FORMAT] [flags]`
  List one or more resources.
* `label` 
  `kubectl label (-f FILENAME | TYPE NAME | TYPE/NAME) KEY_1=VAL_1 ... KEY_N=VAL_N [--overwrite] [--all] [--resource-version=version] [flags]`
  Add or update the labels of one or more resources.
* `logs` 
  `kubectl logs POD [-c CONTAINER] [--follow] [flags]`
  Print the logs for a container in a pod.
* `patch`
  `kubectl patch (-f FILENAME | TYPE NAME | TYPE/NAME) --patch PATCH [flags]`
  Update one or more fields of a resource by using the strategic merge patch process.
* `port-forward`
  `kubectl port-forward POD [LOCAL_PORT:]REMOTE_PORT [...[LOCAL_PORT_N:]REMOTE_PORT_N] [flags]`
  Forward one or more local ports to a pod.
* `proxy`
  `kubectl proxy [--port=PORT] [--www=static-dir] [--www-prefix=prefix] [--api-prefix=prefix] [flags]`
  Run a proxy to the Kubernetes API server.
* `replace`
  `kubectl replace -f FILENAME`
  Replace a resource from a file or stdin.
* `rolling-update`
  `kubectl rolling-update OLD_CONTROLLER_NAME ([NEW_CONTROLLER_NAME] --image=NEW_CONTAINER_IMAGE | -f NEW_CONTROLLER_SPEC) [flags]`
  Perform a rolling update by gradually replacing the specified replication controller and its pods.
* `run`	
  `kubectl run NAME --image=image [--env="key=value"] [--port=port] [--replicas=replicas] [--dry-run=bool] [--overrides=inline-json] [flags]`
  Run a specified image on the cluster.
* `scale` 
  `kubectl scale (-f FILENAME | TYPE NAME | TYPE/NAME) --replicas=COUNT [--resource-version=version] [--current-replicas=count] [flags]`
  Update the size of the specified replication controller.
* `stop` 
  `kubectl stop`
  Deprecated: Instead, see `kubectl delete`.
* `version`
  `kubectl version [--client] [flags]`
  Display the Kubernetes version running on the client and server.

#### kubectl -help
kubectl controls the Kubernetes cluster manager. 
Find more information at https://github.com/kubernetes/kubernetes.

Basic Commands (Beginner):
* `create`
  Create a resource from a file or from stdin.
* `expose`
  Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
* `run`
  Run a particular image on the cluster
* `set`
  Set specific features on objects
* `run-container`
  Run a particular image on the cluster. This command is deprecated, use "run" instead

Basic Commands (Intermediate):
* `get`
  Display one or many resources
* `explain`
  Documentation of resources
* `edit`
  Edit a resource on the server
* `delete`
  Delete resources by filenames, stdin, resources and names, or by resources and label selector

Deploy Commands:
* `rollout`
  Manage the rollout of a resource
* `rolling-update`
  Perform a rolling update of the given ReplicationController
* `scale`
  Set a new size for a Deployment, ReplicaSet, Replication Controller, or Job
* `autoscale`
  Auto-scale a Deployment, ReplicaSet, or ReplicationController

Cluster Management Commands:
* `certificate`
  Modify certificate resources.
* `cluster-info`
   Display cluster info
* `top`
  Display Resource (CPU/Memory/Storage) usage.
* `cordon`
  Mark node as unschedulable
* `uncordon`
  Mark node as schedulable
* `drain`
  Drain node in preparation for maintenance
* `taint`
  Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
* `describe`
  Show details of a specific resource or group of resources
* `logs`
  Print the logs for a container in a pod
* `attach`
  Attach to a running container
* `exec`
  Execute a command in a container
* `port-forward`
  Forward one or more local ports to a pod
* `proxy`
  Run a proxy to the Kubernetes API server
* `cp`
  Copy files and directories to and from containers.
* `auth`
  Inspect authorization

Advanced Commands:
* `apply`
  Apply a configuration to a resource by filename or stdin
* `patch`
  Update field(s) of a resource using strategic merge patch
* `replace`
  Replace a resource by filename or stdin
* `convert`
  Convert config files between different API versions

Settings Commands:
* `label`
  Update the labels on a resource
* `annotate`
  Update the annotations on a resource
* `completion`
  Output shell completion code for the specified shell (bash or zsh)

Other Commands:
* `api-versions`
  Print the supported API versions on the server, in the form of "group/version"
* `config`
  Modify kubeconfig files
* `help`
  Help about any command
* `plugin`
  Runs a command-line plugin
* `version`
  Print the client and server version information


####  Kubectl 自动补全
```bash
# setup autocomplete in bash, bash-completion package should be installed first.
source <(kubectl completion bash)
# setup autocomplete in zsh
source <(kubectl completion zsh) 
```

#### 常用命令
```bash
kubectl get namespace

kebectl get po --all-namespaces
kebectl get po --namespace=kube-system
kubectl get po coredns-xxxx -o yaml --namespace=kube-system

kubectl describe po coredns-xxxx --namespace=kube-system
kubectl logs coredns-xxxx --namespace=kube-system
```

#### Kubectl 上下文和配置
设置 kubectl 命令交互的 kubernetes 集群并修改配置信息。
```bash
$ kubectl config view # 显示合并后的 kubeconfig 配置

# 同时使用多个 kubeconfig 文件并查看合并后的配置
$ KUBECONFIG=~/.kube/config:~/.kube/kubconfig2 kubectl config view

# 获取 e2e 用户的密码
$ kubectl config view -o jsonpath='{.users[?(@.name == "e2e")].user.password}'

$ kubectl config current-context              # 显示当前的上下文
$ kubectl config use-context my-cluster-name  # 设置默认上下文为 my-cluster-name

# 向 kubeconf 中增加支持基本认证的新集群
$ kubectl config set-credentials kubeuser/foo.kubernetes.com --username=kubeuser --password=kubepassword

# 使用指定的用户名和 namespace 设置上下文
$ kubectl config set-context gce --user=cluster-admin --namespace=foo \
  && kubectl config use-context gce
```



#### Kubernetes 对象管理
[Kubernetes Object Management](https://kubernetes.io/docs/concepts/overview/object-management-kubectl/overview/)
`kubectl` 命令行工具支持多种不同的方法来创建和管理Kubernetes对象。
* 命令式命令
  操作活动对象, 开发环境
  `kubectl run nginx --image nginx`
* 命令式对象配置
  个别文件，生产环境
  `kubectl create -f nginx.yaml`
* 声明式对象配置
  文件目录，生产环境 
  `kubectl apply -f configs/`
