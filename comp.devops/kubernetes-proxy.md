## Proxy
Kubernetes 网络 proxy 在每个节点上运行。这反映了每个节点上Kubernetes API中定义的服务，并且可以通过一组后端进行简单的TCP和UDP流转发或循环TCP和UDP转发。服务集群IP和端口通过由服务代理打开的由Docker-links-compatible 环境变量指定的端口找到。有一个可选的插件为这些集群IP提供集群DNS。用户必须使用apiserver API创建服务来配置代理。

#### 概要
每台机器上都运行一个kube-proxy服务，它监听API server中service和endpoint的变化情况，并通过iptables等来为服务配置负载均衡（仅支持TCP和UDP）。
kube-proxy可以直接运行在物理机上，也可以以static pod或者daemonset的方式运行。

kube-proxy当前支持一下几种实现: 
* userspace：最早的负载均衡方案，它在用户空间监听一个端口，所有服务通过 iptables转发到这个端口，然后在其内部负载均衡到实际的Pod。该方式最主要的问题是效率低，有明显的性能瓶颈。 
* iptables：目前推荐的方案，完全以iptables规则的方式来实现service负载均衡。该方式最主要的问题是在服务多的时候产生太多的iptables规则（社区有人提到过几万条），大规模下也有性能问题
* winuserspace：同userspace，但仅工作在windows上

另外，基于ipvs的方案正在讨论中，大规模情况下可以大幅提升性能，比如slide里面提供的示例将服务延迟从小时缩短到毫秒级。


#### 常用选项
* `--master string`
  Kubernetes master apiserver 地址
  The address of the Kubernetes API server (overrides any value in kubeconfig)
* `--hostname-override string`
  参数值必须与 kubelet 的值一致，否则 kube-proxy 启动后会找不到该 Node，从而不会创建任何 iptables 规则； 
	If non-empty, will use this string as identification instead of the actual hostname.
* `--cluster-cidr string`
  kube-proxy 根据 `--cluster-cidr` 判断集群内部和外部流量，指定 `--cluster-cidr` 或 `-masquerade-all` 选项后 kube-proxy 才会对访问 Service IP 的请求做 SNAT；
	The CIDR range of pods in the cluster. When configured, traffic sent to a Service cluster IP from outside this range will be masqueraded and traffic sent from pods to an external LoadBalancer IP will be directed to the respective cluster IP instead
* `--kubeconfig`
  指定的配置文件嵌入了 kube-apiserver 的地址、用户名、证书、秘钥等请求和认证信息； 
  预定义的 RoleBinding cluster-admin 将User system:kube-proxy 与 Role system:nodeproxier 绑定，该 Role 授予了调用 kube-apiserver Proxy 相关 API 的权限；
	Path to kubeconfig file with authorization information (the master location is set by the master flag).
* `--bind-address`
  主机绑定的IP地址。
  The IP address for the proxy server to serve on (set to `0.0.0.0` for all interfaces) 


#### 选项示例
```yaml
--logtostderr=true                          # 输出到 `stderr`,不输到日志文件。
--v=0                                       # 日志级别
--master=http://192.168.99.91:8080          # Kubernetes master apiserver 地址
--bind-address=192.168.99.91                # 主机绑定的IP地址。
--cluster-cidr=10.254.0.0/16                # kube-proxy 根据此判断集群内部和外部流量
--hostname-override=192.168.99.91           # 参数值必须与 kubelet 的值一致，否则 kube-proxy 启动后会找不到该 Node 
--hostname-override=node1                   # 参数值必须与 kubelet 的值一致，否则 kube-proxy 启动后会找不到该 Node 
# kubeconfig 配置文件
--kubeconfig=/etc/kubernetes/kube-proxy.kubeconfig 
```

#### 命令选项
* `--azure-container-registry-config string`
	Path to the file container Azure container registry configuration information.
* `--bind-address ip`
	The IP address for the proxy server to serve on (set to `0.0.0.0` for all interfaces) 
  (default `0.0.0.0`)
* `--cleanup`
	If true cleanup iptables and ipvs rules and exit.
* `--cleanup-ipvs`
	If true make kube-proxy cleanup ipvs rules before running.  Default is true 
  (default `true`)
* `--cluster-cidr string`
	The CIDR range of pods in the cluster. When configured, traffic sent to a Service cluster IP from outside this range will be masqueraded and traffic sent from pods to an external LoadBalancer IP will be directed to the respective cluster IP instead
* `--config string`
	The path to the configuration file.
* `--config-sync-period duration`
	How often configuration from the apiserver is refreshed.  Must be greater than 0. 
  (default `15m0s`)
* `--conntrack-max-per-core int32`
	Maximum number of NAT connections to track per CPU core (0 to leave the limit as-is and ignore conntrack-min). 
  (default `32768`)
* `--conntrack-min int32`
	Minimum number of conntrack entries to allocate, regardless of conntrack-max-per-core (set conntrack-max-per-core=0 to leave the limit as-is). 
  (default `131072`)
* `--conntrack-tcp-timeout-close-wait duration`
	NAT timeout for TCP connections in the CLOSE_WAIT state 
  (default `1h0m0s`)
* `--conntrack-tcp-timeout-established duration`
	Idle timeout for established TCP connections (0 to leave as-is) 
  (default `24h0m0s`)
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
* `--healthz-bind-address ip`
	The IP address and port for the health check server to serve on (set to 0.0.0.0 for all interfaces) 
  (default `0.0.0.0:10256`)
* `--healthz-port int32`
	The port to bind the health check server. Use 0 to disable. 
  (default `10256`)
* `--hostname-override string`
	If non-empty, will use this string as identification instead of the actual hostname.
* `--iptables-masquerade-bit int32`
	If using the pure iptables proxy, the bit of the fwmark space to mark packets requiring SNAT with.  Must be within the range [0, 31]. 
  (default `14`)
* `--iptables-min-sync-period duration`
	The minimum interval of how often the iptables rules can be refreshed as endpoints and services change (e.g. '5s', '1m', '2h22m').
* `--iptables-sync-period duration`
	The maximum interval of how often iptables rules are refreshed (e.g. '5s', '1m', '2h22m').  Must be greater than 0. 
  (default `30s`)
* `--ipvs-min-sync-period duration`
	The minimum interval of how often the ipvs rules can be refreshed as endpoints and services change (e.g. '5s', '1m', '2h22m').
* `--ipvs-scheduler string`
	The ipvs scheduler type when proxy mode is ipvs
* `--ipvs-sync-period duration`
	The maximum interval of how often ipvs rules are refreshed (e.g. '5s', '1m', '2h22m').  Must be greater than 0. 
  (default `30s`)
* `--kube-api-burst int32`
	Burst to use while talking with kubernetes apiserver 
  (default `10`)
* `--kube-api-content-type string`
	Content type of requests sent to apiserver. 
  (default "application/vnd.kubernetes.protobuf")
* `--kube-api-qps float32`
	QPS to use while talking with kubernetes apiserver 
  (default `5`)
* `--kubeconfig string`
	Path to kubeconfig file with authorization information (the master location is set by the master flag).
* `--masquerade-all`
	If using the pure iptables proxy, SNAT all traffic sent via Service cluster IPs (this not commonly needed)
* `--master string`
	The address of the Kubernetes API server (overrides any value in kubeconfig)
* `--metrics-bind-address ip`
	The IP address and port for the metrics server to serve on (set to 0.0.0.0 for all interfaces) 
  (default 127.0.0.1:10249)
* `--oom-score-adj int32`
	The oom-score-adj value for kube-proxy process. Values must be within the range [-1000, 1000] 
  (default `-999`)
* `--profiling`
	If true enables profiling via web interface on /debug/pprof handler.
* `--proxy-mode ProxyMode`
	Which proxy mode to use: '`userspace`' (older) or '`iptables`' (faster) or '`ipvs`'(experimental)'. If blank, use the best-available proxy (currently iptables).  If the iptables proxy is selected, regardless of how, but the system's kernel or iptables versions are insufficient, this always falls back to the userspace proxy.
* `--proxy-port-range port-range`
	Range of host ports (beginPort-endPort, inclusive) that may be consumed in order to proxy service traffic. If unspecified (0-0) then ports will be randomly chosen.
* `--udp-timeout duration`
	How long an idle UDP connection will be kept open (e.g. '`250ms`', '`2s`').  Must be greater than 0. Only applicable for proxy-mode=userspace 
  (default `250ms`)
* `--version version[=true]`
	Print version information and quit
* `--write-config-to string`
	If set, write the default configuration values to this file and exit.