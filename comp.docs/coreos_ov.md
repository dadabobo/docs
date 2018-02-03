## CoreOS

###### CoreOS
@import "img/coreos/CoreOS_Architecture_Diagram.svg" {width="700px"}

@import "img/coreos/coreos_ov.png" {width="600px"}

@import "img/coreos/coreos_layout.png" {width="600px"}


* 轻量级内核、高效发布方式：
  比普通的Linux服务器要少40%。
  无包管理机制，整个操作系统作为一个单元统一更新。
* 双分区、启动速度快
  CoreOS有两个root分区（A/B分区）
  使用kexec方式重启，简化自检引导环节；初始化过程采用systemd方式，提高并行化，仅需数秒即可完成
* 应用容器化
  所有应用都采用容器化方式加载和运行
  解耦操作系统和应用，使得CoreOS 更像硬件固件，而 docker 负责偏上的应用层。
* 分布式自动化
  完善的集群管理和分布式应用部署
  etcd是一个高可用的键值存储系统，主要用于共享配置和服务发现
  fleet是一个分布式的container部署工具，用于进行集群中任务的提交和管理。

CoreOS中的关键技术
* Etcd   ---- 分布式键值存储系统
  etcd是一个高可用的键值存储系统，主要用于CoreOS集群的配置共享和上层应用的服务发现。
  curl可访问的用户的API（HTTP+JSON）
  可选的SSL客户端证书认证
  使用Raft保证一致性，键值支持TTL，原子化测试与设置
* Fleet  ----  容器化应用部署工具
  fleet 是CoreOS集群服务部署和生命周期管理工具。
  基于 systemd 和 etcd 在集群抽象层面实现了进程调度和管理功能。
* Docker  ----应用容器化标准

* Flannel  ----容器间网络通信的增强技术
  