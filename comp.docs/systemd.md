## systemd.md

* [Systemd 简体中文](https://wiki.archlinux.org/index.php/Systemd_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
* [浅析Linux初始化init系统:Systemd](https://www.ibm.com/developerworks/cn/linux/1407_liuming_init3/index.html)
* [Understanding and Using Systemd](https://www.linux.com/learn/understanding-and-using-systemd)
* [Systemd服务简介](https://linux.cn/article-3352-1.html)
* [systemd的运行级别与服务管理命令简介](https://linux.cn/article-4505-1.html)
* [走进Linux之systemd启动过程](https://linux.cn/article-5457-1.html)
* [systemd System and Service Manager](https://www.freedesktop.org/wiki/Software/systemd/)
* [systemctl 命令完全指南](https://linux.cn/article-5926-1.html#3_15197)

systemd 是 Linux 下的一款系统和服务管理器，兼容 SysV 和 LSB 的启动脚本。
systemd 的特性有：
* 支持并行化任务；
* 同时采用 socket 式与 D-Bus 总线式激活服务；
* 按需启动守护进程（daemon）；
* 利用 Linux 的 cgroups 监视进程；
* 支持快照和系统恢复；
* 维护挂载点和自动挂载点；
* 各服务间基于依赖关系进行精密控制。

在目前很多 linux 的新发行版本里，系统对于`daemon`的启动管理方法不再采用 `SystemV` 形式，而是使用了 `sytemd`的架构来管理`daemon`的启动。

###### 概要
* `runlevel` 到 `target` 的改变  
    在`systemd`的管理体系里面，以前的运行级别（`runlevel`）的概念被新的运行目标（`target`）所取代。  
    `tartget`的命名类似于`multi-user.target`等这种形式
* `/etc/systemd/system` 配置目录
* `/lib/systemd/system/` 实际配置。相关文件目录连接到 `/etc/systemd/system` 下。

###### runlevel 到 target的改变

在systemd的管理体系里面，以前的运行级别（`runlevel`）的概念被新的运行目标（`target`）所取代。
tartget的命名类似于`multi-user.target`等这种形式，比如原来的运行级别3（`runlevel3`）就对应新的多用户目标（`multi-user.target`），`run level 5`就相当于`graphical.target`。

在systemd的管理体系里面，默认的target（相当于以前的默认运行级别）是通过软链来实现。如：  
`ln -s /lib/systemd/system/runlevel3.target /etc/systemd/system/default.target`  

在`/lib/systemd/system/` 下面定义`runlevelX.target`文件目的主要是为了能够兼容以前的运行级别level的管理方法。
事实上`/lib/systemd/system/runlevel3.target`，同样是被软连接到`multi-user.target`。

>注：opensuse下是在`/usr/lib/systemd/system/`目录下。

###### 单元控制（unit）
在systemd管理体系里，称呼需要管理的daemon为单元（unit）。对于单元（unit）的管理是通过命令systemctl来进行控制的。例如显示当前的处于运行状态的unit(即daemon)，如：
```bash
$ systemctl
$ systemctl --all
$ systemctl list-units --type=sokect
$ systemctl list-units --type=service
```
注:type后面可以接的类型可以通过help查看 `systemctl -t help`

----
#### systemd 基本工具
###### systemctl用法及示例
```bash
## 激活服务并在启动时启用或禁用服务（即系统启动时自动启动服务）
# 使某服务自动启动
systemctl enable httpd.service
# 使某服务不自动启动
systemctl disable httpd.service
# 检查服务状态（仅显示是否 Active）
systemctl is-active httpd.service
# 检查某个单元（如 httpd.service）是否启用
systemctl is-enabled httpd.service
# 加入自定义服务
systemctl load test.service
# 删除服务: 停掉应用，删除相应的配置文件

## 启动、重启、停止、重载服务以及检查服务状态
# 启动某服务
systemctl start httpd.service
# 停止某服务
systemctl stop httpd.service
# 重启某服务
systemctl restart httpd.service
# 重载某服务
systemctl reload httpd.service
# 检查服务状态
systemctl status httpd.service 

# 列出所有可用单元
systemctl list-unit-files
# 列出所有运行中单元
systemctl list-units
# 列出所有失败单元
systemctl --failed
# 显示所有已启动的服务
systemctl list-units --type=service
# 列出所有服务（包括启用的和禁用的）
systemctl list-unit-files --type=service
```

>注：`systemctl`后的服务名可以到`/lib/systemd/system/`目录查看，`opensuse`发行版是位于`/usr/lib/systemd/system`下。

###### 分析systemd 
```bash
# 分析systemd启动进程
systemd-analyze
# 分析启动时各个进程花费的时间
systemd-analyze blame
# 分析启动时的关键链
systemd-analyze critical-chain
```

###### 使用Systemctl控制并管理服务
```bash
# 列出所有服务（包括启用的和禁用的）
systemctl list-unit-files --type=service
#  Linux中如何启动、重启、停止、重载服务以及检查服务（如 httpd.service）状态
systemctl start httpd.service
systemctl restart httpd.service
systemctl stop httpd.service
systemctl reload httpd.service
systemctl status httpd.service
# 如何激活服务并在启动时启用或禁用服务（即系统启动时自动启动服务）
systemctl is-active httpd.service
systemctl enable httpd.service
systemctl disable httpd.service
# 如何屏蔽（让它不能启动）或显示服务（如 httpd.service）
systemctl mask httpd.service
systemctl unmask httpd.service
# 使用systemctl命令杀死服务
systemctl kill httpd
systemctl status httpd
```

###### 使用Systemctl控制并管理挂载点
```bash
# 列出所有系统挂载点
systemctl list-unit-files --type=mount
# 挂载、卸载、重新挂载、重载系统挂载点并检查系统中挂载点状态
systemctl start tmp.mount
systemctl stop tmp.mount
systemctl restart tmp.mount
systemctl reload tmp.mount
systemctl status tmp.mount
# 在启动时激活、启用或禁用挂载点（系统启动时自动挂载）
systemctl is-active tmp.mount
systemctl enable tmp.mount
systemctl disable  tmp.mount
# 在Linux中屏蔽（让它不能启用）或可见挂载点
systemctl mask tmp.mount
```

###### 使用Systemctl控制并管理套接口
```bash
# 列出所有可用系统套接口
systemctl list-unit-files --type=socket
# 在Linux中启动、重启、停止、重载套接口并检查其状态
systemctl start cups.socket
systemctl restart cups.socket
systemctl stop cups.socket
systemctl reload cups.socket
systemctl status cups.socket
# 在启动时激活套接口，并启用或禁用它（系统启动时自启动）
systemctl is-active cups.socket
systemctl enable cups.socket
systemctl disable cups.socket
# 屏蔽（使它不能启动）或显示套接口
systemctl mask cups.socket
```

###### 服务的CPU利用率（分配额）
```bash
# 获取当前某个服务的CPU分配额（如httpd）
systemctl show -p CPUShares httpd.service
# 将某个服务（httpd.service）的CPU分配份额限制为2000 CPUShares/
systemctl set-property httpd.service CPUShares=2000
systemctl show -p CPUShares httpd.service
# 当你为某个服务设置CPUShares，会自动创建一个以服务名命名的目录（如 httpd.service）.
# 里面包含了一个名为90-CPUShares.conf的文件, 该文件含有CPUShare限制信息.
```

```bash
# 检查某个服务的所有配置细节
systemctl show httpd
# 分析某个服务（httpd）的关键链
systemd-analyze critical-chain httpd.service
# 获取某个服务（httpd）的依赖性列表
systemctl list-dependencies httpd.service
# 按等级列出控制组
systemd-cgls
# 按CPU、内存、输入和输出列出控制组
systemd-cgtop
```


###### 控制系统运行等级
```bash
# 启动系统救援模式
systemctl rescue
# 进入紧急模式
systemctl emergency
# 列出当前使用的运行等级
systemctl get-default
# 启动运行等级5，即图形模式
systemctl isolate runlevel5.target
systemctl isolate graphical.target
# 启动运行等级3，即多用户模式（命令行）
systemctl isolate runlevel3.target
systemctl isolate multiuser.target
# 设置多用户模式或图形模式为默认运行等级
systemctl set-default runlevel3.target
systemctl set-default runlevel5.target
```

###### 重启、停止、挂起、休眠系统或使系统进入混合睡眠
```bash
systemctl reboot
systemctl halt
systemctl suspend
systemctl hibernate
systemctl hybrid-sleep
```

----
#### 编写单元文件


###### service配置文件

还以上面提到的`httpd.service`配置为例，`httpd.service`文件里可以找到如下行：
```
[Install]
WantedBy=multi-user.target
```
则表明在多用户目标（`multi-user.target`，相当于level3）时自动启动。如果想在runlevel 5下也自启动，则可以将配置改为如下：
```
[Install]
WantedBy=multi-user.target graphical.target
```
一旦设定了自动启动（enbale），就在`/etc/systemd/system/.wants/`下面建了一个`httpd.service`的软连接，连接
到`/lib/systemd/system/`下的相应服务那里 。所以显示自动启动状态的unit （类似于`chekconfig --list`命令的结果），可以通过下面的方法：  
`#ls /etc/systemd/system/multi-user.target.wants/`  

---
#### Log
systemd 提供了自己的日志系统（logging system），称为 journal。使用 systemd 日志，无需额外安装日志服务（syslog）。读取日志的命令： `journalctl`

默认情况下（当 Storage= 在文件 /etc/systemd/journald.conf 中被设置为 auto），日志记录将被写入 /var/log/journal/。该目录是 systemd 软件包的一部分。若被删除，systemd 不会自动创建它，直到下次升级软件包时重建该目录。如果该目录缺失，systemd 会将日志记录写入 /run/systemd/journal。这意味着，系统重启后日志将丢失。 

```bash
# 显示本次启动后的所有日志
journalctl -b
journalctl -b -0   # 显示本次启动的信息
journalctl -b -1   # 显示上次启动的信息
journalctl -b -2   # 显示上上次启动的信息

# 只显示错误、冲突和重要告警信息 
journalctl -p err..alert
# 显示从某个日期 ( 或时间 ) 开始的消息: 
journalctl --since="2012-10-30 18:17:16"
# 显示从某个时间 ( 例如 20分钟前 ) 的消息: 
journalctl --since "20 min ago"
# 显示最新信息
journalctl -f
# 显示特定程序的所有消息: 
journalctl /usr/lib/systemd/systemd
# 显示特定进程的所有消息: 
journalctl _PID=1
# 显示指定单元的所有消息：
journalctl -u netcfg
# 显示内核环缓存消息:
journalctl -k
# Show auth.log equivalent by filtering on syslog facility
journalctl -f -l SYSLOG_FACILITY=10
# 显示特定标识的所有消息
journalctl --identifier=systemd
journalctl --identifier=coreos-cloudinit
journalctl -t ignition

# 配合 syslog 使用
systemctl enable syslog-ng
# 清理日志使总大小小于 100M: 
journalctl --vacuum-size=100M
# 清理最早两周前的日志. 
journalctl --vacuum-time=2weeks
```

