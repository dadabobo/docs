### Kubernete 基础
![Kubernetes](./img/k8s/k8s-post-ccm-arch.png)

![Kubernetes](./img/k8s/k8s-overview.png)

##### 
![Kubernetes](./img/k8s/k8s-abbr.png)

##### How Services in a Cluster Map to Functions in Pods
![Kubernetes](./img/k8s/k8s-service-function.png)

#### Kubernetes集群


![Kubernetes](./img/k8s/k8s-module_cluster.png)
* Master 负责集群的管理。Master 协调集群中的所有行为/活动，例应用的运行、修改、更新等。
* 节点（Node）作为Kubernetes集群中的工作节点，可以是VM虚拟机、物理机。
  每个node上都有一个`Kubelet`，用于管理node节点与Kubernetes Master通信。
  每个Node节点上至少还要运行 `container runtime`。
* Node节点使用master公开的 `Kubernetes API` 与主节点进行通信

![Kubernetes](./img/k8s/k8s-module_nodes.png)

![Kubernetes](./img/k8s/k8s-module_rollingupdatesA.png) 
![Kubernetes](./img/k8s/k8s-module_rollingupdatesB.png)


#### 概念
![Kubernetes](./img/k8s/k8s-concept.png)
![Kubernetes](./img/k8s/k8s-minikube-part.png)

