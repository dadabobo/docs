### Kubernetes - scheduler
Kubernetes scheduler 是一项策略丰富的拓扑感知工作负载特定功能，可显着影响可用性，性能和容量。调度程序需要考虑个人和集体资源需求，服务质量要求，硬件/软件/政策约束，亲和力和反亲和力规范，数据位置，工作负载间干扰，截止日期等。必要时，工作负载特定的要求将通过API公开。

###### 概要
kube-scheduler负责分配调度Pod到集群内的节点上，它监听kube-apiserver，查询还未分配Node的Pod，然后根据调度策略为这些Pod分配节点（更新Pod的 NodeName 字 段）。

###### 常用选项
* `--master`
  Kubernetes master apiserver 地址
  The address of the Kubernetes API server (overrides any value in kubeconfig)
* `--address`
	`--address` 值必须为 `127.0.0.1`，kube-apiserver 期望 scheduler 和 controller-manager 在同一台机器；
	The IP address to serve on (set to 0.0.0.0 for all interfaces) 
  (default `0.0.0.0`)
* `--leader-elect`
	在启动时设置 `--leader-elect=true` 后，controller manager会使用多节点选主的方式选择主节点。只有主节点才会调用 StartControllers() 启动所有控制器，而其他从节点则仅执行选主算法。
	Start a leader election client and gain leadership before executing the main loop. Enable this when running replicated components for high availability. 
  (default `true`)
* `--kubeconfig`
  kubeconfig 配置文件，在配置文件中包含 master 地址信息和必要的认证信息
  Path to kubeconfig file with authorization and master location information.


###### 选项示例
```yaml
--logtostderr=true                          # 输出到 `stderr`,不输到日志文件。
--v=0                                       # 日志级别
--leader-elect=true                         # 启动选举
--master=http://192.168.99.91:8080          # Kubernetes master apiserver 地址
--address=127.0.0.1                         # 绑定主机 IP 地址，apiserver 与 controller-manager在同一主机 
--kubeconfig=/etc/kubernetes/scheduler.conf # kubeconfig 配置文件，在配置文件中包含 master 地址信息和必要的认证信息
```

###### 命令选项
* `--address string`
  The IP address to serve on (set to 0.0.0.0 for all interfaces)
* `--algorithm-provider string`
  The scheduling algorithm provider to use, one of: ClusterAutoscalerProvider | DefaultProvider
* `--azure-container-registry-config string`
  Path to the file container Azure container registry configuration information.
* `--config string`
  The path to the configuration file.
* `--contention-profiling`
  Enable lock contention profiling, if profiling is enabled
* `--feature-gates mapStringBool`
  A set of key=value pairs that describe feature gates for alpha/experimental features. Options are:
    APIListChunking=true|false (BETA - default=true)
    APIResponseCompression=true|false (ALPHA - default=false)
    Accelerators=true|false (ALPHA - default=false)
    AdvancedAuditing=true|false (BETA - default=true)
    AllAlpha=true|false (ALPHA - default=false)
    AllowExtTrafficLocalEndpoints=true|false (default=true)
    AppArmor=true|false (BETA - default=true)
    BlockVolume=true|false (ALPHA - default=false)
    CPUManager=true|false (ALPHA - default=false)
    CSIPersistentVolume=true|false (ALPHA - default=false)
    CustomPodDNS=true|false (ALPHA - default=false)
    CustomResourceValidation=true|false (BETA - default=true)
    DebugContainers=true|false (ALPHA - default=false)
    DevicePlugins=true|false (ALPHA - default=false)
    DynamicKubeletConfig=true|false (ALPHA - default=false)
    EnableEquivalenceClassCache=true|false (ALPHA - default=false)
    ExpandPersistentVolumes=true|false (ALPHA - default=false)
    ExperimentalCriticalPodAnnotation=true|false (ALPHA - default=false)
    ExperimentalHostUserNamespaceDefaulting=true|false (BETA - default=false)
    HugePages=true|false (ALPHA - default=false)
    Initializers=true|false (ALPHA - default=false)
    KubeletConfigFile=true|false (ALPHA - default=false)
    LocalStorageCapacityIsolation=true|false (ALPHA - default=false)
    MountContainers=true|false (ALPHA - default=false)
    MountPropagation=true|false (ALPHA - default=false)
    PVCProtection=true|false (ALPHA - default=false)
    PersistentLocalVolumes=true|false (ALPHA - default=false)
    PodPriority=true|false (ALPHA - default=false)
    ResourceLimitsPriorityFunction=true|false (ALPHA - default=false)
    RotateKubeletClientCertificate=true|false (BETA - default=true)
    RotateKubeletServerCertificate=true|false (ALPHA - default=false)
    ServiceNodeExclusion=true|false (ALPHA - default=false)
    StreamingProxyRedirects=true|false (BETA - default=true)
    SupportIPVSProxyMode=true|false (BETA - default=false)
    TaintBasedEvictions=true|false (ALPHA - default=false)
    TaintNodesByCondition=true|false (ALPHA - default=false)
    VolumeScheduling=true|false (ALPHA - default=false)
* `--google-json-key string`
  The Google Cloud Platform Service Account JSON Key to use for authentication.
* `--kube-api-burst int32`
  Burst to use while talking with kubernetes apiserver 
  (default 100)
* `--kube-api-content-type string`
  Content type of requests sent to apiserver. 
  (default "application/vnd.kubernetes.protobuf")
* `--kube-api-qps float32`
  QPS to use while talking with kubernetes apiserver 
  (default 50)
* `--kubeconfig string`
  Path to kubeconfig file with authorization and master location information.
* `--leader-elect`
  Start a leader election client and gain leadership before executing the main loop. Enable this when running replicated components for high availability.
* `--leader-elect-lease-duration duration`
  The duration that non-leader candidates will wait after observing a leadership renewal until attempting to acquire leadership of a led but unrenewed leader slot. This is effectively the maximum duration that a leader can be stopped before it is replaced by another candidate. This is only applicable if leader election is enabled. 
  (default 15s)
* `--leader-elect-renew-deadline duration`
  The interval between attempts by the acting master to renew a leadership slot before it stops leading. This must be less than or equal to the lease duration. This is only applicable if leader election is enabled. 
  (default 10s)
* `--leader-elect-resource-lock endpoints`
  The type of resource object that is used for locking during leader election. Supported options are endpoints (default) and `configmaps`. 
  (default "endpoints")
* `--leader-elect-retry-period duration`
  The duration the clients should wait between attempting acquisition and renewal of a leadership. This is only applicable if leader election is enabled. 
  (default 2s)
* `--lock-object-name string`
  Define the name of the lock object. 
  (default "kube-scheduler")
* `--lock-object-namespace string`
  Define the namespace of the lock object. 
  (default "kube-system")
* `--master string`
  The address of the Kubernetes API server (overrides any value in kubeconfig)
* `--policy-config-file string`
  File with scheduler policy configuration. This file is used if policy ConfigMap is not provided or --use-legacy-policy-config==true
* `--policy-configmap string`
  Name of the ConfigMap object that contains scheduler's policy configuration. It must exist in the system namespace before scheduler initialization if --use-legacy-policy-config==false. The config must be provided as the value of an element in 'Data' map with the key='policy.cfg'
* `--policy-configmap-namespace string`
  The namespace where policy ConfigMap is located. The system namespace will be used if this is not provided or is empty.
* `--port int32`
  The port that the scheduler's http service runs on 
  (default 10251)
* `--profiling`
  Enable profiling via web interface host:port/debug/pprof/
* `--scheduler-name string`
  Name of the scheduler, used to select which pods will be processed by this scheduler, based on pod's "spec.SchedulerName". 
  (default "default-scheduler")
* `--use-legacy-policy-config`
  When set to true, scheduler will ignore policy ConfigMap and uses policy config file
* `--version version[=true]`
  Print version information and quit
