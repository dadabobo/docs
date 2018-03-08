## Kubeadmin
* [Using kubeadm to Create a Cluster](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/)
* [Overview of kubeadm](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm/)
* [Installing kubeadm](https://kubernetes.io/docs/setup/independent/install-kubeadm/)

#### 使用kubeadm创建群集
kubeadm是一个工具包，可帮助您以简单，合理安全和可扩展的方式引导最佳实践Kubernetes群集。它还支持为您管理Bootstrap令牌并升级/降级群集。

kubeadm的目标是建立一个通过Kubernetes一致性测试的最小可行集群 ，但安装其他功能集群所不具备的其他附加组件则超出了范围。

它在设计上并未为您安装网络解决方案，这意味着您必须安装自己使用的第三方符合CNI的网络解决方案kubectl apply。

kubeadm希望用户带来一台机器执行，类型无关紧要，可以是Linux笔记本电脑，虚拟机，物理/云服务器或Raspberry Pi。这使得kubeadm非常适合与不同种类的配置系统（例如Terraform，Ansible等）集成。

kubeadm被设计成一种简单的方式，让新用户开始试用Kubernetes，可能是第一次，这是现有用户轻松测试他们的应用程序并将它们拼接在一起的方式，也是其他生态系统中的构建块和/或具有更大范围的安装工具。

您可以在支持安装deb或rpm软件包的操作系统上非常轻松地安装kubeadm。SIG集群生命周期 kubeadm的负责SIG为您 提供了预先构建的这些软件包，但您也可以在其他操作系统上使用。

kubeadm的整体功能状态为Beta，即将在2018 年推向 General Availability（GA）。一些子功能（如自托管或配置文件API）仍在积极开发中。随着工具的发展，创建集群的实现可能会稍微改变，但总体实现应该相当稳定。根据kubeadm alpha定义，任何命令 都在alpha级别上受支持。


#### 环境说明
```
系统: ubuntu/xenial64
test  : 192.168.99.30
test01: 192.168.99.31
test01: 192.168.99.32
test01: 192.168.99.33
```

#### 在主机上安装kubeadm
支持的系统： Ubuntu 16.04+， Debian 9， RHEL 7， Container Linux (tested with 1576.4.0) 等。
* 安装 docker
* 安装 kubeadm, kubelet and kubectl


#### 初始化 master
`kubeadm init`

#### 安装 pod 网络

#### 加入 nodes
