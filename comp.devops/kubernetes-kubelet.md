### Kubernetets - kubelet

###### Synopsis
Kubelet是在每个节点上运行的主要“节点代理”。Kubelet的工作方式是PodSpec。PodSpec是一个描述一个pod的YAML或JSON对象。kubelet采用通过各种机制（主要通过apiserver）提供的一组PodSpecs，并确保这些PodSpec中描述的容器运行正常。Kubelet不管理不是由Kubernetes创建的容器。

除了来自apiserver的PodSpec之外，容器清单可以通过三种方式提供给Kubelet。
* 文件：在命令行上作为标志传递的路径。这条路径下的文件将被定期监控更新。监控周期默认为20秒，可通过标志进行配置。
* HTTP端点：HTTP端点作为命令行上的参数传递。该端点每20秒检查一次（也可用标志配置）。
* HTTP服务器：kubelet还可以侦听HTTP并响应一个简单的API（当前未指定）提交新的清单。

常用选项
* `--address`
  kubelet 服务监听的地址 (默认: `0.0.0.0`)
* `--port`  
  kubelet 服务监听的端口 (默认:`10250`)
* `--read-only-port`
  只读端口，可以不用验证和授权机制，直接访问 (默认:`10255`)
* `--allow-privileged`
  是否允许容器运行在 privileged 模式 (默认:`false`)
* `--api-servers`
  以逗号分割的 API Server 地址，用于和集群中数据交互  (默认:`[]`)
* `--cadvisor-port`
  当前节点 cadvisor 运行的端口 (默认:`4194`)
* `--pod-manifest-path`
  本地 manifest 文件的路径或者目录 (默认:`""`)
* `file-check-frequency`
  轮询本地 manifest 文件的时间间隔
* `--container-runtime`
  后端容器 runtime，支持 docker 和 rkt (默认:`docker`)
* `--enable-server`
  是否启动 kubelet HTTP server
* `--healthz-bind-address`
  健康检查服务绑定的地址，设置成 0.0.0.0 可以监听在所有网络接口 (默认: `127.0.0.1`)
* `--healthz-port`
  健康检查服务的端口 (默认:`10248`)
* `--hostname-override`
  指定 hostname，如果非空会使用这个值作为节点在集群中的标识
* `--pod-infra-container-image`
  基础镜像地址，每个 pod 最先启动的容器，会配置共享的网络
  (默认: `gcr.io/google_containers/pause-amd64:3.0`)
* `root-dir`
  kubelet 保存数据的目录
* `--runonce`
  从本地 manifest 或者 URL 指定的 manifest 读取并运行结束就退出，和 `--api-servers`、`--enable-server` 参数不兼容


###### Examples
master
```yaml
ExecStart=/usr/bin/docker run \
          --volume=/:/rootfs:ro \
          --volume=/sys:/sys:ro \
          --volume=/etc/kubernetes:/etc/kubernetes:ro \
          --volume=/var/lib/docker/:/var/lib/docker:rw \
          --volume=/var/lib/kubelet/:/var/lib/kubelet:rw \
          --volume=/var/run:/var/run:rw \
          --net=host \
          --pid=host \
          --privileged=true \
          --name=kubelet \
          -d \
          wangwg2/hyperkube-amd64:__RELEASE__ \
            /hyperkube kubelet \
            --address=$private_ipv4 \
            --network-plugin= \
            --pod-infra-container-image=wangwg2/pause-amd64:3.0 \
            --register-schedulable=false \
            --allow-privileged=true \
            --pod-manifest-path=/etc/kubernetes/manifests \
            --hostname-override=$public_ipv4 \
            --cluster_dns=10.100.0.10 \
            --cluster_domain=__DNS_DOMAIN__ \
            --kubeconfig=/etc/kubernetes/master-kubeconfig.yaml
```
* `--address ip`
  The IP address for the Kubelet to serve on (set to 0.0.0.0 for all interfaces) (default 0.0.0.0)
* `--network-plugin string`
  <Warning: Alpha feature> The name of the network plugin to be invoked for various events in kubelet/pod lifecycle
* `--pod-infra-container-image string`
  The image whose network/ipc namespaces containers in each pod will use. (default "k8s.gcr.io/pause-amd64:3.0")
* `--allow-privileged`
  If true, allow containers to request privileged mode.
* `--pod-manifest-path string`
  Path to the directory containing pod manifest files to run, or the path to a single pod manifest file. Files starting with dots will be ignored.
* `--hostname-override string`
  If non-empty, will use this string as identification instead of the actual hostname.
* `--cluster-domain string`
  Domain for this cluster. If set, kubelet will configure all containers to search this domain in addition to the host's search domains
* `--kubeconfig string`
  Path to a kubeconfig file, specifying how to connect to the API server. (default "`/var/lib/kubelet/kubeconfig`")
* `--register-schedulable=false`

node
```yaml
ExecStart=/usr/bin/docker run \
          --volume=/:/rootfs:ro \
          --volume=/sys:/sys:ro \
          --volume=/etc/kubernetes:/etc/kubernetes:ro \
          --volume=/var/lib/docker/:/var/lib/docker:rw \
          --volume=/var/lib/kubelet/:/var/lib/kubelet:shared \
          --volume=/var/run:/var/run:rw \
          --net=host \
          --pid=host \
          --privileged=true \
          --name=kubelet \
          -d \
          wangwg2/hyperkube-amd64:__RELEASE__ \
            /hyperkube kubelet \
            --containerized \
            --address=$private_ipv4 \
            --network-plugin= \
            --allow-privileged=true \
            --pod-manifest-path=/etc/kubernetes/manifests \
            --hostname-override=$public_ipv4 \
            --cluster_dns=10.100.0.10 \
            --cluster_domain=__DNS_DOMAIN__ \
            --kubeconfig=/etc/kubernetes/node-kubeconfig.yaml \
            --tls-cert-file=/etc/kubernetes/ssl/node.pem \
            --tls-private-key-file=/etc/kubernetes/ssl/node-key.pem
```
* `--containerized`
  Experimental support for running kubelet in a container. Intended for testing.
* `--tls-cert-file string`
  File containing x509 Certificate used for serving HTTPS (with intermediate certs, if any, concatenated after server cert). If `--tls-cert-file` and `--tls-private-key-file` are not provided, a self-signed certificate and key are generated for the public address and saved to the directory passed to `--cert-dir`.
* `--tls-private-key-file string`
  File containing x509 private key matching `--tls-cert-file`.


###### Options
* `--address ip`
  The IP address for the Kubelet to serve on (set to 0.0.0.0 for all interfaces) (default 0.0.0.0)
* `--allow-privileged`
  If true, allow containers to request privileged mode.
* `--anonymous-auth`
  Enables anonymous requests to the Kubelet server. Requests that are not rejected by another authentication method are treated as anonymous requests. Anonymous requests have a username of system:anonymous, and a group name of system:unauthenticated. (default true)
* `--authentication-token-webhook`
  Use the TokenReview API to determine authentication for bearer tokens.
* `--authentication-token-webhook-cache-ttl duration`
  The duration to cache responses from the webhook token authenticator. (default 2m0s)
* `--authorization-mode string`
  Authorization mode for Kubelet server. Valid options are AlwaysAllow or Webhook. Webhook mode uses the SubjectAccessReview API to determine authorization. (default "AlwaysAllow")
* `--authorization-webhook-cache-authorized-ttl duration`
  The duration to cache 'authorized' responses from the webhook authorizer. (default 5m0s)
* `--authorization-webhook-cache-unauthorized-ttl duration`
  The duration to cache 'unauthorized' responses from the webhook authorizer. (default 30s)
* `--azure-container-registry-config string`
  Path to the file container Azure container registry configuration information.
* `--bootstrap-checkpoint-path string`
  <Warning: Alpha feature> Path to to the directory where the checkpoints are stored
* `--bootstrap-kubeconfig string`
  Path to a kubeconfig file that will be used to get client certificate for kubelet. If the file specified by --kubeconfig does not exist, the bootstrap kubeconfig is used to request a client certificate from the API server. On success, a kubeconfig file referencing the generated client certificate and key is written to the path specified by --kubeconfig. The client certificate and key file will be stored in the directory pointed by --cert-dir.
* `--cadvisor-port int32`
  The port of the localhost cAdvisor endpoint (set to 0 to disable) (default 4194)
* `--cert-dir string`
  The directory where the TLS certs are located. If --tls-cert-file and --tls-private-key-file are provided, this flag will be ignored. (default `"/var/lib/kubelet/pki"`)
* `--cgroup-driver string`
  Driver that the kubelet uses to manipulate cgroups on the host.  Possible values: 'cgroupfs', 'systemd' (default "cgroupfs")
* `--cgroup-root string`
  Optional root cgroup to use for pods. This is handled by the container runtime on a best effort basis. Default: '', which means use the container runtime default.
* `--cgroups-per-qos`
  Enable creation of QoS cgroup hierarchy, if true top level QoS and pod cgroups are created. (default true)
* `--chaos-chance float`
  If > 0.0, introduce random client errors and latency. Intended for testing.
* `--client-ca-file string`
  If set, any request presenting a client certificate signed by one of the authorities in the client-ca-file is authenticated with an identity corresponding to the CommonName of the client certificate.
* `--cloud-config string`
  The path to the cloud provider configuration file.  Empty string for no configuration file.
* `--cloud-provider string`
  The provider for cloud services. Specify empty string for running with no cloud provider.
* `--cluster-dns stringSlice`
  Comma-separated list of DNS server IP address.  This value is used for containers DNS server in case of Pods with "dnsPolicy=ClusterFirst". Note: all DNS servers appearing in the list MUST serve the same set of records otherwise name resolution within the cluster may not work correctly. There is no guarantee as to which DNS server may be contacted for name resolution.
* `--cluster-domain string`
  Domain for this cluster.  If set, kubelet will configure all containers to search this domain in addition to the host's search domains
* `--cni-bin-dir string`
  <Warning: Alpha feature> The full path of the directory in which to search for CNI plugin binaries. Default: /opt/cni/bin
* `--cni-conf-dir string`
  <Warning: Alpha feature> The full path of the directory in which to search for CNI config files. Default: /etc/cni/net.d
* `--container-runtime string`
  The container runtime to use. Possible values: 'docker', 'rkt'. (default "docker")
* `--container-runtime-endpoint string`
  [Experimental] The endpoint of remote runtime service. Currently unix socket is supported on Linux, and tcp is supported on windows.  Examples:'unix:///var/run/dockershim.sock', 'tcp://localhost:3735' (default "unix:///var/run/dockershim.sock")
* `--containerized`
  Experimental support for running kubelet in a container.  Intended for testing.
* `--contention-profiling`
  Enable lock contention profiling, if profiling is enabled
* `--cpu-cfs-quota`
  Enable CPU CFS quota enforcement for containers that specify CPU limits (default true)
* `--cpu-manager-policy string`
  <Warning: Alpha feature> CPU Manager policy to use. Possible values: 'none', 'static'. Default: 'none' (default "none")
* `--cpu-manager-reconcile-period NodeStatusUpdateFrequency`
  <Warning: Alpha feature> CPU Manager reconciliation period. Examples: '10s', or '1m'. If not supplied, defaults to NodeStatusUpdateFrequency (default 10s)
* `--docker-disable-shared-pid`
  The Container Runtime Interface (CRI) defaults to using a shared PID namespace for containers in a pod when running with Docker 1.13.1 or higher. Setting this flag reverts to the previous behavior of isolated PID namespaces. This ability will be removed in a future Kubernetes release. (default true)
* `--docker-endpoint string`
  Use this for the docker endpoint to communicate with (default "unix:///var/run/docker.sock")
* `--dynamic-config-dir string`
  The Kubelet will use this directory for checkpointing downloaded configurations and tracking configuration health. The Kubelet will create this directory if it does not already exist. The path may be absolute or relative; relative paths start at the Kubelet's current working directory. Providing this flag enables dynamic Kubelet configuration. Presently, you must also enable the DynamicKubeletConfig feature gate to pass this flag.
* `--enable-controller-attach-detach`
  Enables the Attach/Detach controller to manage attachment/detachment of volumes scheduled to this node, and disables kubelet from executing any attach/detach operations (default true)
* `--enable-debugging-handlers`
  Enables server endpoints for log collection and local running of containers and commands (default true)
* `--enable-server`
  Enable the Kubelet's server (default true)
* `--enforce-node-allocatable stringSlice`
  A comma separated list of levels of node allocatable enforcement to be enforced by kubelet. Acceptible options are 'pods', 'system-reserved' & 'kube-reserved'. If the latter two options are specified, '--system-reserved-cgroup' & '--kube-reserved-cgroup' must also be set respectively. See https://kubernetes.io/docs/tasks/administer-cluster/reserve-compute-resources/ for more details. (default [pods])
* `--event-burst int32`
  Maximum size of a bursty event records, temporarily allows event records to burst to this number, while still not exceeding event-qps. Only used if `--event-qps > 0` (default 10)
* `--event-qps int32`
  If > 0, limit event creations per second to this value. If 0, unlimited. (default 5)
* `--eviction-hard mapStringString`
  A set of eviction thresholds (e.g. memory.available<1Gi) that if met would trigger a pod eviction. (default imagefs.available<15%,memory.available<100Mi,nodefs.available<10%,nodefs.inodesFree<5%)
* `--eviction-max-pod-grace-period int32`
  Maximum allowed grace period (in seconds) to use when terminating pods in response to a soft eviction threshold being met.  If negative, defer to pod specified value.
* `--eviction-minimum-reclaim mapStringString`
  A set of minimum reclaims (e.g. imagefs.available=2Gi) that describes the minimum amount of resource the kubelet will reclaim when performing a pod eviction if that resource is under pressure.
* `--eviction-pressure-transition-period duration`
  Duration for which the kubelet has to wait before transitioning out of an eviction pressure condition. (default 5m0s)
* `--eviction-soft mapStringString`
  A set of eviction thresholds (e.g. memory.available<1.5Gi) that if met over a corresponding grace period would trigger a pod eviction.
* `--eviction-soft-grace-period mapStringString`
  A set of eviction grace periods (e.g. memory.available=1m30s) that correspond to how long a soft eviction threshold must hold before triggering a pod eviction.
* `--exit-on-lock-contention`
  Whether kubelet should exit upon lock-file contention.
* `--experimental-allocatable-ignore-eviction`
  When set to 'true', Hard Eviction Thresholds will be ignored while calculating Node Allocatable. See https://kubernetes.io/docs/tasks/administer-cluster/reserve-compute-resources/ for more details. [default=false]
* `--experimental-allowed-unsafe-sysctls stringSlice`
  Comma-separated whitelist of unsafe sysctls or unsafe sysctl patterns (ending in *). Use these at your own risk.
* `--experimental-bootstrap-kubeconfig string`
  deprecated: use --bootstrap-kubeconfig
* `--experimental-check-node-capabilities-before-mount`
  [Experimental] if set true, the kubelet will check the underlying node for required componenets (binaries, etc.) before performing the mount
* `--experimental-kernel-memcg-notification`
  If enabled, the kubelet will integrate with the kernel memcg notification to determine if memory eviction thresholds are crossed rather than polling.
* `--experimental-mounter-path string`
  [Experimental] Path of mounter binary. Leave empty to use the default mount.
* `--experimental-qos-reserved mapStringString`
  A set of ResourceName=Percentage (e.g. memory=50%) pairs that describe how pod resource requests are reserved at the QoS level. Currently only memory is supported. [default=none]
* `--fail-swap-on`
  Makes the Kubelet fail to start if swap is enabled on the node.  (default true)
* `--feature-gates mapStringBool`
  A set of key=value pairs that describe feature gates for alpha/experimental features. Options are:
    `APIListChunking=true|false` (BETA - default=true)
    `APIResponseCompression=true|false` (ALPHA - default=false)
    `Accelerators=true|false` (ALPHA - default=false)
    `AdvancedAuditing=true|false` (BETA - default=true)
    `AllAlpha=true|false` (ALPHA - default=false)
    `AllowExtTrafficLocalEndpoints=true|false` (default=true)
    `AppArmor=true|false` (BETA - default=true)
    `BlockVolume=true|false` (ALPHA - default=false)
    `CPUManager=true|false` (ALPHA - default=false)
    `CSIPersistentVolume=true|false` (ALPHA - default=false)
    `CustomPodDNS=true|false` (ALPHA - default=false)
    `CustomResourceValidation=true|false` (BETA - default=true)
    `DebugContainers=true|false` (ALPHA - default=false)
    `DevicePlugins=true|false` (ALPHA - default=false)
    `DynamicKubeletConfig=true|false` (ALPHA - default=false)
    `EnableEquivalenceClassCache=true|false` (ALPHA - default=false)
    `ExpandPersistentVolumes=true|false` (ALPHA - default=false)
    `ExperimentalCriticalPodAnnotation=true|false` (ALPHA - default=false)
    `ExperimentalHostUserNamespaceDefaulting=true|false` (BETA - default=false)
    `HugePages=true|false` (ALPHA - default=false)
    `Initializers=true|false` (ALPHA - default=false)
    `KubeletConfigFile=true|false` (ALPHA - default=false)
    `LocalStorageCapacityIsolation=true|false` (ALPHA - default=false)
    `MountContainers=true|false` (ALPHA - default=false)
    `MountPropagation=true|false` (ALPHA - default=false)
    `PVCProtection=true|false` (ALPHA - default=false)
    `PersistentLocalVolumes=true|false` (ALPHA - default=false)
    `PodPriority=true|false` (ALPHA - default=false)
    `ResourceLimitsPriorityFunction=true|false` (ALPHA - default=false)
    `RotateKubeletClientCertificate=true|false` (BETA - default=true)
    `RotateKubeletServerCertificate=true|false` (ALPHA - default=false)
    `ServiceNodeExclusion=true|false` (ALPHA - default=false)
    `StreamingProxyRedirects=true|false` (BETA - default=true)
    `SupportIPVSProxyMode=true|false` (BETA - default=false)
    `TaintBasedEvictions=true|false` (ALPHA - default=false)
    `TaintNodesByCondition=true|false` (ALPHA - default=false)
    `VolumeScheduling=true|false` (ALPHA - default=false)
* `--file-check-frequency duration`
  Duration between checking config files for new data (default 20s)
* `--google-json-key string`
  The Google Cloud Platform Service Account JSON Key to use for authentication.
* `--hairpin-mode string`
  How should the kubelet setup hairpin NAT. This allows endpoints of a Service to loadbalance back to themselves if they should try to access their own Service. Valid values are "promiscuous-bridge", "hairpin-veth" and "none". (default "promiscuous-bridge")
* `--healthz-bind-address ip`
  The IP address for the healthz server to serve on. (set to 0.0.0.0 for all interfaces) (default 127.0.0.1)
* `--healthz-port int32`
  The port of the localhost healthz endpoint (set to 0 to disable) (default 10248)
* `--host-ipc-sources stringSlice`
  Comma-separated list of sources from which the Kubelet allows pods to use the host ipc namespace. (default [*])
* `--host-network-sources stringSlice`
  Comma-separated list of sources from which the Kubelet allows pods to use of host network. (default [*])
* `--host-pid-sources stringSlice`
  Comma-separated list of sources from which the Kubelet allows pods to use the host pid namespace. (default [*])
* `--hostname-override string`
  If non-empty, will use this string as identification instead of the actual hostname.
* `--http-check-frequency duration`
  Duration between checking http for new data (default 20s)
* `--image-gc-high-threshold int32`
  The percent of disk usage after which image garbage collection is always run. (default 85)
* `--image-gc-low-threshold int32`
  The percent of disk usage before which image garbage collection is never run. Lowest disk usage to garbage collect to. (default 80)
* `--image-pull-progress-deadline duration`
  If no pulling progress is made before this deadline, the image pulling will be cancelled. (default 1m0s)
* `--image-service-endpoint string`
  [Experimental] The endpoint of remote image service. If not specified, it will be the same with container-runtime-endpoint by default. Currently unix socket is supported on Linux, and tcp is supported on windows.  Examples:'unix:///var/run/dockershim.sock', 'tcp://localhost:3735'
* `--init-config-dir string`
  The Kubelet will look in this directory for the init configuration. The path may be absolute or relative; relative paths start at the Kubelet's current working directory. Omit this argument to use the built-in default configuration values. Presently, you must also enable the KubeletConfigFile feature gate to pass this flag.
* `--iptables-drop-bit int32`
  The bit of the fwmark space to mark packets for dropping. Must be within the range [0, 31]. (default 15)
* `--iptables-masquerade-bit int32`
  The bit of the fwmark space to mark packets for SNAT. Must be within the range [0, 31]. Please match this parameter with corresponding parameter in kube-proxy. (default 14)
* `--kube-api-burst int32`
  Burst to use while talking with kubernetes apiserver (default 10)
* `--kube-api-content-type string`
  Content type of requests sent to apiserver. (default "application/vnd.kubernetes.protobuf")
* `--kube-api-qps int32`
  QPS to use while talking with kubernetes apiserver (default 5)
* `--kube-reserved mapStringString`
  A set of ResourceName=ResourceQuantity (e.g. cpu=200m,memory=500Mi,ephemeral-storage=1Gi) pairs that describe resources reserved for kubernetes system components. Currently cpu, memory and local ephemeral storage for root file system are supported. See http://kubernetes.io/docs/user-guide/compute-resources for more detail. [default=none]
* `--kube-reserved-cgroup string`
  Absolute name of the top level cgroup that is used to manage kubernetes components for which compute resources were reserved via '--kube-reserved' flag. Ex. '/kube-reserved'. [default='']
* `--kubeconfig string`
  Path to a kubeconfig file, specifying how to connect to the API server. (default "`/var/lib/kubelet/kubeconfig`")
* `--kubelet-cgroups string`
  Optional absolute name of cgroups to create and run the Kubelet in.
* `--lock-file string`
  <Warning: Alpha feature> The path to file for kubelet to use as a lock file.
* `--make-iptables-util-chains`
  If true, kubelet will ensure iptables utility rules are present on host. (default true)
* `--manifest-url string`
  URL for accessing the container manifest
* `--manifest-url-header` `--manifest-url-header 'a:hello,b:again,c:world'` `--manifest-url-header 'b:beautiful'`
  Comma-separated list of HTTP headers to use when accessing the manifest URL. Multiple headers with the same name will be added in the same order provided. This flag can be repeatedly invoked. For example: --manifest-url-header 'a:hello,b:again,c:world' --manifest-url-header 'b:beautiful'
* `--max-open-files int`
  Number of files that can be opened by Kubelet process. (default 1000000)
* `--max-pods int32`
  Number of Pods that can run on this Kubelet. (default 110)
* `--minimum-image-ttl-duration duration`
  Minimum age for an unused image before it is garbage collected.  Examples: '300ms', '10s' or '2h45m'. (default 2m0s)
* `--network-plugin string`
  <Warning: Alpha feature> The name of the network plugin to be invoked for various events in kubelet/pod lifecycle
* `--network-plugin-mtu int32`
  <Warning: Alpha feature> The MTU to be passed to the network plugin, to override the default. Set to 0 to use the default 1460 MTU.
* `--node-ip string`
  IP address of the node. If set, kubelet will use this IP address for the node
* `--node-labels mapStringString`
  <Warning: Alpha feature> Labels to add when registering the node in the cluster.  Labels must be key=value pairs separated by ','.
* `--node-status-update-frequency duration`
  Specifies how often kubelet posts node status to master. Note: be cautious when changing the constant, it must work with nodeMonitorGracePeriod in nodecontroller. (default 10s)
* `--oom-score-adj int32`
  The oom-score-adj value for kubelet process. Values must be within the range [-1000, 1000] (default -999)
* `--pod-cidr string`
  The CIDR to use for pod IP addresses, only used in standalone mode.  In cluster mode, this is obtained from the master.
* `--pod-infra-container-image string`
  The image whose network/ipc namespaces containers in each pod will use. (default "k8s.gcr.io/pause-amd64:3.0")
* `--pod-manifest-path string`
  Path to the directory containing pod manifest files to run, or the path to a single pod manifest file. Files starting with dots will be ignored.
* `--pods-per-core int32`
  Number of Pods per core that can run on this Kubelet. The total number of Pods on this Kubelet cannot exceed max-pods, so max-pods will be used if this calculation results in a larger number of Pods allowed on the Kubelet. A value of 0 disables this limit.
* `--port int32`
  The port for the Kubelet to serve on. (default 10250)
* `--protect-kernel-defaults`
  Default kubelet behaviour for kernel tuning. If set, kubelet errors if any of kernel tunables is different than kubelet defaults.
* `--provider-id string`
  Unique identifier for identifying the node in a machine database, i.e cloudprovider
* `--read-only-port int32`
  The read-only port for the Kubelet to serve on with no authentication/authorization (set to 0 to disable) (default 10255)
* `--really-crash-for-testing`
  If true, when panics occur crash. Intended for testing.
* `--register-node`
  Register the node with the apiserver. If --kubeconfig is not provided, this flag is irrelevant, as the Kubelet won't have an apiserver to register with. Default=true. (default true)
* `--register-with-taints []api.Taint`
  Register the node with the given list of taints (comma separated "<key>=<value>:<effect>"). No-op if register-node is false.
* `--registry-burst int32`
  Maximum size of a bursty pulls, temporarily allows pulls to burst to this number, while still not exceeding registry-qps. Only used if  --registry-qps > 0 (default 10)
* `--registry-qps int32`
  If > 0, limit registry pull QPS to this value.  If 0, unlimited. (default 5)
* `--resolv-conf string`
  Resolver configuration file used as the basis for the container DNS resolution configuration. (default "/etc/resolv.conf")
* `--rkt-api-endpoint string`
  The endpoint of the rkt API service to communicate with. Only used if --container-runtime='rkt'. (default "localhost:15441")
* `--rkt-path string`
  Path of rkt binary. Leave empty to use the first rkt in $PATH.  Only used if --container-runtime='rkt'.
* `--root-dir string`
  Directory path for managing kubelet files (volume mounts,etc). (default "/var/lib/kubelet")
* `--rotate-certificates`
  <Warning: Beta feature> Auto rotate the kubelet client certificates by requesting new certificates from the kube-apiserver when the certificate expiration approaches.
* `--runonce`
  If true, exit after spawning pods from local manifests or remote urls. Exclusive with --enable-server
* `--runtime-cgroups string`
  Optional absolute name of cgroups to create and run the runtime in.
* `--runtime-request-timeout duration`
  Timeout of all runtime requests except long running request - pull, logs, exec and attach. When timeout exceeded, kubelet will cancel the request, throw out an error and retry later. (default 2m0s)
* `--seccomp-profile-root string`
  <Warning: Alpha feature> Directory path for seccomp profiles. (default "/var/lib/kubelet/seccomp")
* `--serialize-image-pulls`
  Pull images one at a time. We recommend *not* changing the default value on nodes that run docker daemon with version < 1.9 or an Aufs storage backend. Issue #10959 has more details. (default true)
* `--streaming-connection-idle-timeout duration`
  Maximum time a streaming connection can be idle before the connection is automatically closed. 0 indicates no timeout. Example: '5m' (default 4h0m0s)
* `--sync-frequency duration`
  Max period between synchronizing running containers and config (default 1m0s)
* `--system-cgroups /`
  Optional absolute name of cgroups in which to place all non-kernel processes that are not already inside a cgroup under /. Empty for no container. Rolling back the flag requires a reboot.
* `--system-reserved mapStringString`
  A set of ResourceName=ResourceQuantity (e.g. cpu=200m,memory=500Mi,ephemeral-storage=1Gi) pairs that describe resources reserved for non-kubernetes components. Currently only cpu and memory are supported. See http://kubernetes.io/docs/user-guide/compute-resources for more detail. [default=none]
* `--system-reserved-cgroup string`
  Absolute name of the top level cgroup that is used to manage non-kubernetes components for which compute resources were reserved via '--system-reserved' flag. Ex. '/system-reserved'. [default='']
* `--tls-cert-file string`
  File containing x509 Certificate used for serving HTTPS (with intermediate certs, if any, concatenated after server cert). If --tls-cert-file and --tls-private-key-file are not provided, a self-signed certificate and key are generated for the public address and saved to the directory passed to --cert-dir.
* `--tls-private-key-file string`
  File containing x509 private key matching --tls-cert-file.
* `--version version[=true]`
  Print version information and quit
* `--volume-plugin-dir string`
  <Warning: Alpha feature> The full path of the directory in which to search for additional third party volume plugins (default "/usr/libexec/kubernetes/kubelet-plugins/volume/exec/")
* `--volume-stats-agg-period duration`
  Specifies interval for kubelet to calculate and cache the volume disk usage for all pods and volumes.  To disable volume calculations, set to 0. (default 1m0s)