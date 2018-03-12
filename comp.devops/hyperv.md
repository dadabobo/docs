## hyperv

参考资料
* [Windows 10 上的 Hyper-V](https://docs.microsoft.com/zh-cn/virtualization/hyper-v-on-windows/)
* [Hyper-V on Windows 10](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/index)
* [Ben Armstrong’s Virtualization Blog](https://blogs.msdn.microsoft.com/virtual_pc_guy/)
* [Virtualization Blog](https://blogs.technet.microsoft.com/virtualization/)
* [Windows NAT (WinNAT) — Capabilities and limitations](https://blogs.technet.microsoft.com/virtualization/2016/05/25/windows-nat-winnat-capabilities-and-limitations/)
* [容器网络](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/manage-containers/container-networking)
* [Windows Container Networking](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/container-networking)
* [Virtualization in Windows Server 2016](https://docs.microsoft.com/zh-cn/windows-server/virtualization/virtualization)
* [删除Windows中隐藏的物理网卡和网络虚拟化失败后的虚拟网卡](http://www.cnblogs.com/qingspace/p/4268993.html)
* [Creating a Vagrant Base Box for HyperV](https://pyranja.github.io/hyperv-box/)  
  [pyranja/box-centos-hyperv](https://github.com/pyranja/box-centos-hyperv/blob/master/centos.packer.json)
* [Hyper-V:虚拟网络配置](http://owlking.blog.163.com/blog/static/2076442201182121329405/)
* [Hyper-v相关PowerShell命令参考](http://www.aichengxu.com/shell/3325499.htm)
* [mwrock/packer-templates](https://github.com/mwrock/packer-templates)
* [dwickern/packer-ubuntu](https://github.com/dwickern/packer-ubuntu)


## 问题技巧

#### Windows 10 Docker 设置共享驱动器问题解决
报错，将网络 `vEthernet (DockerNAT)` 改为专用网络(private).   
`Set-NetConnectionProfile -InterfaceAlias "vEthernet (DockerNAT)" -NetworkCategory Private`


## Hyper-V 虚拟机管理

**返回 hyper-v 模块命令列表**  
`Get-Command -Module hyper-v | Out-GridView`

**使用 PowerShell 启用 Hyper-V**
* 以管理员身份打开 PowerShell 控制台。
* `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All`
* 安装完成后，需要重新启动计算机。


#### 为虚拟机连接 Internet
Hyper-V 有三种类型的虚拟交换机 -- 外部、内部和专用。 创建外部交换机以与在其上运行的虚拟机共享计算机网络。
此练习演示了如何创建虚拟交换机。

完成后，Hyper-V 主机将拥有一个虚拟交换机，该虚拟交换机可通过为计算机连接网络来为虚拟机连接 Internet。

**使用 PowerShell 创建虚拟交换机**
* 使用 `Get-NetAdapter` 返回连接到 Windows 10 系统的网络适配器列表。  
  `Get-NetAdapter`
* 选择要用于 Hyper-V 交换机的网络适配器并将一个实例放入名为 `$net` 的变量中。  
  `$net = Get-NetAdapter -Name 'Ethernet'`
* 执行以下命令来创建新的 Hyper-V 虚拟交换机。
  `New-VMSwitch -Name "External VM Switch" -AllowManagementOS $True -NetAdapterName $net.Name`


#### 笔记本电脑上的虚拟网络

**NAT 网络**

网络地址转换 (NAT) 使用内部 Hyper-V 虚拟交换机对主机的 IP 地址与端口进行组合，以使虚拟机访问计算机的网络。
这会提供一些有用的属性：
* NAT 会将外部 IP 地址和端口映射到更大的内部 IP 地址集，以转换 IP 地址。
* NAT 允许多个虚拟机托管需要相同（内部）通信端口的应用程序，方法是将它们映射到唯一的外部端口。
* NAT 使用内部交换机—创建内部交换机不会要求使用网络连接，且往往对计算机网络的干扰较小。

若要设置 NAT 网络并将其连接到虚拟机，请遵循 NAT 网络用户指南。

**双交换机方法**

如果你在笔记本电脑上运行 Windows 10 Hyper-V，并且经常在无线网络和有线网络之间切换，那么你可能想要为以太网和无线网卡创建一个虚拟交换机。 使用此配置，可以更改这些交换机之间的虚拟机，具体取决于该笔记本电脑连接网络的方式。 虚拟机不会在有线和无线之间自动切换。


---

## Windows Hyper-V 网络

#### Hyper-V:虚拟网络配置
* 外部"虚拟网络，是Hyper-V通过将"Microsoft虚拟交换机协议"绑定在主机网卡上实现的。  
  如果虚拟机选择"外部"虚拟网络，则虚拟机"相当"于网络中的一台计算机，是可以与物理网络中的其他计算机、主机互相访问。
* "内部"虚拟网络，只允许虚拟机与主机互相访问，不能访问外部，外部也不能访问"内部"的虚拟机。
* "专用"虚拟网络，只允许虚拟机之间互相访问，与物理主机也不能互相访问。
在同一个物理主机中，"内部"、"外部"、"专用"虚拟网络，相当于物理网络中的不同的"交换机"，它们之间没有网络关系。

#### 网络命令
* `Get-NetAdapter` 查询网络适配器
* `Get-NetIPInterface` 查看网卡信息。`Set-NetIPInterface`
* `Get-NetIPaddress -AddressFamily IPv4` 查看正在使用的 IP 地址
* `Get-NetIPConfiguration` 查看网络配置
* `Get-VMswitch ` 查看虚拟交换机
* `Get-VMNetworkAdapter –all` 查看虚拟网络适配器
* `Get-VMNetworkAdapter –managementos`
*

#### Window中各类网络设备和网络连接
* 物理网卡
* Hyper-V Virtual Ethernet Adapter 虚拟网卡；
* Microsoft Network Adapter Multiplexor 网卡组 (通过命令lbfoadmin可以对网卡组进行管理)
* 在显示隐藏设备后，还可见Hyper-V Virtual Switch Extension Adapter 虚拟交换机等。

常用命令
* `Get-VMNetworkAdapter`、`Remove-VMNetworkAadapter`
* `New-VMSwitch`、`Get-VMswitch`、`Remove-VMSwitch`、`Rename-VMSwitch`
* `New-NetNat`、`Get-NetNat`、`Remove-NetNAT`

```
Get-Netadapter
Get-VMNetworkAdapter –all
Get-VMNetworkAdapter –managementos
Remove-VMNetworkAadapter –managementos –name
```


---------

<span id="winnat"></span>
## 设置 NAT 网络
Windows 10 Hyper-V 允许虚拟网络的本机网络地址转换 (NAT)。
本指南将引导你完成：
* 创建 NAT 网络
* 将现有虚拟机连接到新网络
* 确认虚拟机正确连接

要求：
* Windows 10 周年更新或更高版本
* 已启用 Hyper-V（单击此处 查看相关说明）

>注意：目前，每台主机可以创建一个 NAT 网络。 有关 Windows NAT (WinNAT) 实现、功能和限制的更多详细信息，请参考 [WinNAT 功能和限制博客](https://blogs.technet.microsoft.com/virtualization/2016/05/25/windows-nat-winnat-capabilities-and-limitations/)

#### NAT 概述
NAT 使用主计算机的 IP 地址和端口通过内部 Hyper-V 虚拟开关向虚拟机授予对网络资源的访问权限。

网络地址转换 (NAT) 是一种网络模式，旨在通过将一个外部 IP 地址和端口映射到更大的内部 IP 地址集来转换 IP 地址。 基本上，NAT 使用流量表将流量从一个外部（主机）IP 地址和端口号路由到与网络上的终结点（虚拟机、计算机和容器等）关联的正确内部 IP 地址

此外，NAT 允许多个虚拟机托管需要相同（内部）通信端口的应用程序，方法是将它们映射到唯一的外部端口。

出于所有这些原因，NAT 网络对于容器技术是很常见的（请参阅[容器网络](https://msdn.microsoft.com/en-us/virtualization/windowscontainers/management/container_networking)）。


#### 创建 NAT 虚拟网络
以管理员身份打开 PowerShell 控制台。
1. 创建内部开关  
    `New-VMSwitch -SwitchName "SwitchName" -SwitchType Internal`
2. 使用 `New-NetIPAddress` 配置 NAT 网关。(常规命令)  
    `New-NetIPAddress -IPAddress <NAT GatewayIP> -PrefixLength <NAT SubnetPrefixLength> -InterfaceIndex <ifIndex>`
3. 使用 `New-NetNat` 配置 NAT 网络。(常规命令)  
   `New-NetNat -Name <NATOutsideName> -InternalIPInterfaceAddressPrefix <NAT subnet prefix>`

**`New-NetIPAddress` 配置NAT网关**  
`New-NetIPAddress -IPAddress` <NAT Gateway IP> `-PrefixLength` <NAT SubnetPrefixLength> -InterfaceIndex <ifIndex>`  
若要配置网关，你将需要一些有关你的网络的信息：
* `IPAddress` - NAT 网关 IP 指定要用作 NAT 网关 IP 的 IPv4 或 IPv6 地址。  
  常规形式将为 `a.b.c.1`; 通用网关 IP 为 `192.168.0.1`
* `PrefixLength` - NAT 子网前缀长度定义的 NAT 本地子网大小（子网掩码）。
  子网前缀长度将为 0 到 32 之间的整数值。  
  0 将映射整个 Internet，32 将只允许一个映射的 IP。 常用值的范围为 24 到 12，具体取决于需要附加到 NAT 的 IP 数。
  常用 `PrefixLength` 为 24 -- 这是子网掩码 `255.255.255.0`
* `InterfaceIndex` - ifIndex 是上述步骤中创建的虚拟交换机的接口索引。  
  你可以通过运行以下命令查找接口索引 `Get-NetAdapter`

示例：  
`New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 24`

**`New-NetNat` 配置NAT网络**  
`New-NetNat -Name <NATOutsideName> -InternalIPInterfaceAddressPrefix <NAT subnet prefix>`  

若要配置网关，你将需要提供一些有关网络和 NAT 网关的信息：
* `Name` - NATOutsideName 描述 NAT 网络的名称。 将使用此参数删除 NAT 网络。
* `InternalIPInterfaceAddressPrefix` - NAT 子网前缀同时描述上述 NAT 网关 IP 前缀和上述 NAT 子网前缀长度。  
  常规形式将为 `a.b.c.0/NAT` 子网前缀长度

综上所述，对于本示例，我们将使用 `192.168.0.0/24`  
`New-NetNat -Name MyNATnetwork -InternalIPInterfaceAddressPrefix 192.168.0.0/24`

**创建NAT命令总结**
```
New-VMSwitch -SwitchName "VMNAT" -SwitchType Internal
Get-NetAdapter
New-NetIPAddress -IPAddress 192.168.55.1 -PrefixLength 24 -InterfaceIndex 53
New-NetNat -Name MyNATWork -InternalIPInterfaceAddressPrefix 192.168.55.0/24
```



#### 连接虚拟机
若要将虚拟机连接到新的 NAT 网络，请使用“VM 设置”菜单将你在 NAT 网络设置部分的第一步中创建的内部交换机连接到虚拟机。

由于 WinNAT 本身不会将 IP 地址分配给某个终结点（例如，VM），因此，你将需要从 VM 内手动完成此操作，即设置 NAT 内部前缀范围内的 IP 地址、设置默认网关 IP 地址，以及设置 DNS 服务器信息。 唯一需要注意的是终结点何时连接到容器。 在这种情况下，主机网络服务 (HNS) 分配并使用主机计算服务 (HCS) 直接向容器分配 IP 地址、网关 IP 和 DNS 信息。

#### 配置示例：将 VM 和容器连接到 NAT 网络

如果你需要将多个 VM 和容器连接到单个 NAT，则需要确保 NAT 内部子网前缀大小足以包含由不同的应用程序或服务（例如用于 Windows 的 Docker 和 Windows 容器 – HNS）分配的 IP 范围。 这将需要应用程序级别的 IP 分配和网络配置，或者必须由管理员完成手动配置，从而保证不会在同一主机上重用现有 IP 分配。

**用于 Windows 的 Docker（Linux VM）和 Windows 容器**

下面的解决方案将允许用于 Windows 的 Docker（运行 Linux 容器的 Linux VM）和 Windows 容器使用单独的内部 vSwitch 共享同一个 WinNAT 实例。 Linux 和 Windows 容器之间的连接将起作用。

用户已通过名为“VMNAT”的内部 vSwitch 将 VM 连接到 NAT 网络，现在想要使用 Docker 引擎安装 Windows 容器功能
```bash
PS C:\> Get-NetNat “VMNAT”| Remove-NetNat
(this will remove the NAT but keep the internal vSwitch).
Install Windows Container Feature
DO NOT START Docker Service (daemon)
Edit the arguments passed to the docker daemon (dockerd) by adding –fixed-cidr=<container prefix> parameter. This tells docker to create a default nat network with the IP subnet <container prefix> (e.g. 192.168.1.0/24) so that HNS can allocate IPs from this prefix.

PS C:\> Start-Service Docker; Stop-Service Docker
PS C:\> Get-NetNat | Remove-NetNAT
 (again, this will remove the NAT but keep the internal vSwitch)
PS C:\> New-NetNat -Name SharedNAT -InternalIPInterfaceAddressPrefix <shared prefix>
PS C:\> Start-Service docker
```

Docker/HNS 将 IP 分配给 的 Windows 容器，管理员将 IP 分配给 差异集的 VM 以及
用户已通过运行 docker 引擎安装了 Windows 容器功能，现在想要将 VM 连接到 NAT 网络

```
PS C:\> Stop-Service docker
PS C:\> Get-ContainerNetwork | Remove-ContainerNetwork -force
PS C:\> Get-NetNat | Remove-NetNat (this will remove the NAT but keep the internal vSwitch)
Edit the arguments passed to the docker daemon (dockerd) by adding -b “none” option to the end of docker daemon (dockerd) command to tell docker not to create a default NAT network.
PS C:\> New-ContainerNetwork –name nat –Mode NAT –subnetprefix <container prefix> (create a new NAT and internal vSwitch – HNS will allocate IPs to container endpoints attached to this network from the <container prefix>)
PS C:\> Get-Netnat | Remove-NetNAT (again, this will remove the NAT but keep the internal vSwitch)
PS C:\> New-NetNat -Name SharedNAT -InternalIPInterfaceAddressPrefix <shared prefix>
PS C:\> New-VirtualSwitch -Type internal (attach VMs to this new vSwitch)
PS C:\> Start-Service docker
```


#### 多个应用程序使用相同的 NAT

某些情况下需要多个应用程序或服务使用同一个 NAT。 在这种情况下，必须遵循以下工作流，以便多个应用程序/服务可以使用更大的 NAT 内部子网前缀

*我们将以与 Windows 容器功能共存于同一主机上的 Docker 4 Windows - Docker Beta - Linux VM 作为示例，对其进行详细介绍。 此工作流可能会随时发生变化*

1. C:> `net stop docker`
2. 停止 Docker4Windows MobyLinux VM
3. PS C:> `Get-ContainerNetwork | Remove-ContainerNetwork -force`  
4. PS C:> `Get-NetNat | Remove-NetNat`  
  删除任何先前存在的容器网络（即删除 vSwitch、删除 NetNat、清理）
5. `New-ContainerNetwork -Name nat -Mode NAT –subnetprefix 10.0.76.0/24`（此子网将用于 Windows 容器功能）创建名为 nat 的内部 vSwitch  
  创建名为“nat”、IP 前缀为 10.0.76.0/24 的 NAT 网络
6. `Remove-NetNAT`  
  删除 DockerNAT 和 nat NAT 网络（保留内部 vSwitch）
7. `New-NetNat -Name DockerNAT -InternalIPInterfaceAddressPrefix 10.0.0.0/17`（这将创建较大的 NAT 网络，以供 D4W 和容器共享）  
  创建名为“DockerNAT”的 NAT 网络，且该网络采用较大的前缀 10.0.0.0/17
8. 运行 Docker4Windows (MobyLinux.ps1)  
  创建内部 vSwitch DockerNAT  
  创建名为“DockerNAT”、IP 前缀为 10.0.75.0/24 的 NAT 网络
9. `Net start docker`  
  Docker 将使用用户定义的 NAT 网络作为默认网络连接到 Windows 容器

最终，你将具有两个内部 vSwitch – 一个名为 DockerNAT，另一个名为 nat。 你仅将拥有一个 NAT 网络 (10.0.0.0/17)，可通过运行 Get-NetNat 确认。 Windows 容器的 IP 地址将由 Windows 主机网络服务 (HNS) 从 10.0.76.0/24 子网分配。 基于现有 MobyLinux.ps1 脚本，将从 10.0.75.0/24 子网分配 Docker 4 Windows 的 IP 地址。

#### 疑难解答

**不支持多个 NAT 网络**

本指南假设主机上没有其他 NAT。 但是，应用程序或服务将要求使用一个 NAT，并且有可能创建一个 NAT，使之作为安装的一部分。 由于 Windows (WinNAT) 仅支持一个内部 NAT 子网前缀，因此尝试创建多个 NAT 将使系统处于未知状态。

若想知道这是否是个问题，请确保你只有一个 NAT：
`Get-NetNat`
如果已存在一个 NAT，请将其删除
`Get-NetNat | Remove-NetNat`

请确保应用程序或功能（例如 Windows 容器）仅有一个“内部”vmSwitch。 记录 vSwitch 的名称
`Get-VMSwitch`

检查是否仍存在从旧 NAT 向适配器分配的专用 IP 地址（例如 NAT 默认网关 IP 地址 – 通常为 *.1）
`Get-NetIPAddress -InterfaceAlias "vEthernet(<name of vSwitch>)"`

如果旧专用 IP 地址仍在使用中，请将其删除
`Remove-NetIPAddress -InterfaceAlias "vEthernet(<name of vSwitch>)" -IPAddress <IPAddress>`

**删除多个 NAT**

我们看到无意中创建了多个 NAT 网络的报告。 这是由于最近的版本（包括 Windows Server 2016 Technical Preview 5 和 Windows 10 Insider Preview 版本）存在一个 bug。 如果你看到多个 NAT 网络，在运行 `docker network ls` 或 `Get-ContainerNetwork` 之后，请在提升的 PowerShell 中执行以下操作：

```
PS> $KeyPath = "HKLM:\SYSTEM\CurrentControlSet\Services\vmsmp\parameters\SwitchList"
PS> $keys = get-childitem $KeyPath
PS> foreach($key in $keys)
PS> {
PS>    if ($key.GetValue("FriendlyName") -eq 'nat')
PS>    {
PS>       $newKeyPath = $KeyPath+"\"+$key.PSChildName
PS>       Remove-Item -Path $newKeyPath -Recurse
PS>    }
PS> }
PS> remove-netnat -Confirm:$false
PS> Get-ContainerNetwork | Remove-ContainerNetwork
PS>    Get-VmSwitch -Name nat | Remove-VmSwitch (_failure is expected_)
PS>    Stop-Service docker
PS> Set-Service docker -StartupType Disabled
Reboot Host
PS> Get-NetNat | Remove-NetNat
PS> Set-Service docker -StartupType automaticac
PS> Start-Service docker
```

如有必要，请参阅 setup guide for multiple applications using the same NAT（使用同一个 NAT 的多个应用程序的安装指南）以重新生成你的 NAT 环境。


---
### 命令执行示例
* `Get-NetAdapter` 查询网络适配器
* `Get-NetIPInterface` 查看网卡信息。`Set-NetIPInterface`
* `Get-NetIPaddress -AddressFamily IPv4` 查看正在使用的 IP 地址
* `Get-NetIPConfiguration` 查看网络配置
* `Get-VMswitch ` 查看虚拟交换机
* `Get-VMNetworkAdapter –all` 查看虚拟网络适配器
* `Get-VMNetworkAdapter –managementos`


`Get-NetAdapter` 查询网络适配器
```
Name                      InterfaceDescription                    ifIndex Status       MacAddress             LinkSpeed
----                      --------------------                    ------- ------       ----------             ---------
vEthernet (DockerNAT)     Hyper-V Virtual Ethernet Adapter #2          54 Up           00-15-5D-63-01-0A        10 Gbps
vEthernet (packer-hype... Hyper-V Virtual Ethernet Adapter             50 Up           00-15-5D-63-01-03        10 Gbps
WLAN                      Broadcom 802.11 网络适配器提供无线局...       3 Disconnected 9C-B7-0D-B9-22-31        72 Mbps
以太网                    Intel(R) 82579LM Gigabit Network Con...      12 Up           D4-BE-D9-21-E8-5A       100 Mbps
VirtualBox Host-Only ...4 VirtualBox Host-Only Ethernet Adap...#4      16 Up           0A-00-27-00-00-10         1 Gbps
```

`Get-NetIPInterface` 查看网卡信息
```
ifIndex InterfaceAlias                  AddressFamily NlMtu(Bytes) InterfaceMetric Dhcp     ConnectionState PolicyStore
------- --------------                  ------------- ------------ --------------- ----     --------------- -----------
54      vEthernet (DockerNAT)           IPv6                  1500              15 Disabled Connected       ActiveStore
50      vEthernet (packer-hyperv-iso)   IPv6                  1500              15 Enabled  Connected       ActiveStore
16      VirtualBox Host-Only Network #4 IPv6                  1500              25 Enabled  Connected       ActiveStore
1       Loopback Pseudo-Interface 1     IPv6            4294967295              75 Disabled Connected       ActiveStore
13      本地连接* 3                     IPv6                  1500              25 Disabled Disconnected    ActiveStore
14      本地连接* 1                     IPv6                  1500              25 Disabled Disconnected    ActiveStore
12      以太网                          IPv6                  1500              35 Enabled  Connected       ActiveStore
3       WLAN                            IPv6                  1500              55 Enabled  Disconnected    ActiveStore
54      vEthernet (DockerNAT)           IPv4                  1500              15 Disabled Connected       ActiveStore
50      vEthernet (packer-hyperv-iso)   IPv4                  1500              15 Disabled Connected       ActiveStore
16      VirtualBox Host-Only Network #4 IPv4                  1500              25 Disabled Connected       ActiveStore
1       Loopback Pseudo-Interface 1     IPv4            4294967295              75 Disabled Connected       ActiveStore
13      本地连接* 3                     IPv4                  1500              25 Enabled  Disconnected    ActiveStore
14      本地连接* 1                     IPv4                  1500              25 Enabled  Disconnected    ActiveStore
12      以太网                          IPv4                  1500              35 Disabled Connected       ActiveStore
3       WLAN                            IPv4                  1500              55 Enabled  Disconnected    ActiveStore
```

`Get-VMswitch ` 查看虚拟交换机
```
Name              SwitchType NetAdapterInterfaceDescription
----              ---------- ------------------------------
packer-hyperv-iso Internal
DockerNAT         Internal
```


`Get-VMNetworkAdapter -all` 查看虚拟网络适配器
```
Name              IsManagementOs VMName      SwitchName        MacAddress   Status                      IPAddresses
----              -------------- ------      ----------        ----------   ------                      -----------
DockerNAT         True                       DockerNAT         00155D63010A {Ok}
packer-hyperv-iso True                       packer-hyperv-iso 00155D630103 {Ok}
网络适配器        False          MobyLinuxVM DockerNAT         00155D63010B {Degraded, ProtocolVersion} {}
```

`Get-VMNetworkAdapter –managementos`
```
Name              IsManagementOs VMName SwitchName        MacAddress   Status IPAddresses
----              -------------- ------ ----------        ----------   ------ -----------
packer-hyperv-iso True                  packer-hyperv-iso 00155D630103 {Ok}
DockerNAT         True                  DockerNAT         00155D63010A {Ok}
```


`Get-NetIPConfiguration` 查看网络配置
```
InterfaceAlias       : 以太网
InterfaceIndex       : 12
InterfaceDescription : Intel(R) 82579LM Gigabit Network Connection
NetProfile.Name      : 网络  2
IPv4Address          : 10.21.49.229
IPv6DefaultGateway   :
IPv4DefaultGateway   : 10.21.49.1
DNSServer            : 192.168.6.42
                       10.1.1.7

InterfaceAlias       : VirtualBox Host-Only Network #4
InterfaceIndex       : 16\\\
InterfaceDescription : VirtualBox Host-Only Ethernet Adapter #4
IPv4Address          : 192.168.99.1
IPv6DefaultGateway   :
IPv4DefaultGateway   :
DNSServer            : fec0:0:0:ffff::1
                       fec0:0:0:ffff::2
                       fec0:0:0:ffff::3

InterfaceAlias       : vEthernet (packer-hyperv-iso)
InterfaceIndex       : 50
InterfaceDescription : Hyper-V Virtual Ethernet Adapter
NetProfile.Name      : 未识别的网络
IPv4Address          : 192.168.137.1
IPv6DefaultGateway   :
IPv4DefaultGateway   :
DNSServer            : fec0:0:0:ffff::1
                       fec0:0:0:ffff::2
                       fec0:0:0:ffff::3

InterfaceAlias       : vEthernet (DockerNAT)
InterfaceIndex       : 54
InterfaceDescription : Hyper-V Virtual Ethernet Adapter #2
NetProfile.Name      : 未识别的网络
IPv4Address          : 10.0.75.1
IPv6DefaultGateway   :
IPv4DefaultGateway   :
DNSServer            : fec0:0:0:ffff::1
                       fec0:0:0:ffff::2
                       fec0:0:0:ffff::3

InterfaceAlias       : WLAN
InterfaceIndex       : 3
InterfaceDescription : Broadcom 802.11 网络适配器提供无线局域网。
NetAdapter.Status    : Disconnected
```



`Get-NetIPaddress -AddressFamily IPv4` 查看正在使用的 IP 地址
```
IPAddress         : 10.0.75.1
InterfaceIndex    : 54
InterfaceAlias    : vEthernet (DockerNAT)
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Preferred
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 192.168.137.1
InterfaceIndex    : 50
InterfaceAlias    : vEthernet (packer-hyperv-iso)
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Preferred
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 192.168.99.1
InterfaceIndex    : 16
InterfaceAlias    : VirtualBox Host-Only Network #4
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Preferred
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 127.0.0.1
InterfaceIndex    : 1
InterfaceAlias    : Loopback Pseudo-Interface 1
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 8
PrefixOrigin      : WellKnown
SuffixOrigin      : WellKnown
AddressState      : Preferred
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 169.254.222.86
InterfaceIndex    : 13
InterfaceAlias    : 本地连接* 3
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 16
PrefixOrigin      : WellKnown
SuffixOrigin      : Link
AddressState      : Tentative
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 169.254.196.179
InterfaceIndex    : 14
InterfaceAlias    : 本地连接* 1
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 16
PrefixOrigin      : WellKnown
SuffixOrigin      : Link
AddressState      : Tentative
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 10.21.49.229
InterfaceIndex    : 12
InterfaceAlias    : 以太网
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Preferred
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 169.254.170.200
InterfaceIndex    : 3
InterfaceAlias    : WLAN
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 16
PrefixOrigin      : WellKnown
SuffixOrigin      : Link
AddressState      : Tentative
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore
```
