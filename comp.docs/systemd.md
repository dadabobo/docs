## systemd.md

[Systemd 简体中文](https://wiki.archlinux.org/index.php/Systemd_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
[浅析Linux初始化init系统:Systemd](https://www.ibm.com/developerworks/cn/linux/1407_liuming_init3/index.html)

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

#### 概要
* `runlevel` 到 `target` 的改变  
    在`systemd`的管理体系里面，以前的运行级别（`runlevel`）的概念被新的运行目标（`target`）所取代。  
    `tartget`的命名类似于`multi-user.target`等这种形式
* `/etc/systemd/system` 配置目录
* `/lib/systemd/system/` 实际配置。相关文件目录连接到 `/etc/systemd/system` 下。

#### runlevel 到 target的改变

在systemd的管理体系里面，以前的运行级别（`runlevel`）的概念被新的运行目标（`target`）所取代。
tartget的命名类似于`multi-user.target`等这种形式，比如原来的运行级别3（`runlevel3`）就对应新的多用户目标（`multi-user.target`），`run level 5`就相当于`graphical.target`。

在systemd的管理体系里面，默认的target（相当于以前的默认运行级别）是通过软链来实现。如：  
`ln -s /lib/systemd/system/runlevel3.target /etc/systemd/system/default.target`  

在`/lib/systemd/system/` 下面定义`runlevelX.target`文件目的主要是为了能够兼容以前的运行级别level的管理方法。
事实上`/lib/systemd/system/runlevel3.target`，同样是被软连接到`multi-user.target`。

>注：opensuse下是在`/usr/lib/systemd/system/`目录下。

#### 单元控制（unit）
在systemd管理体系里，称呼需要管理的daemon为单元（unit）。对于单元（unit）的管理是通过命令systemctl来进行控制的。例如显示当前的处于运行状态的unit(即daemon)，如：
```bash
$ systemctl
$ systemctl --all
$ systemctl list-units --type=sokect
$ systemctl list-units --type=service
```
注:type后面可以接的类型可以通过help查看 `systemctl -t help`

#### systemctl用法及示例
```
使某服务自动启动     systemctl enable httpd.service
使某服务不自动启动    systemctl disable httpd.service
检查服务状态         systemctl status httpd.service （服务详细信息）
                    systemctl is-active httpd.service （仅显示是否 Active)
加入自定义服务       systemctl load test.service
删除服务             停掉应用，删除相应的配置文件
显示所有已启动的服务  systemctl list-units --type=service
启动某服务           systemctl start httpd.service
停止某服务           systemctl stop httpd.service
重启某服务           systemctl restart httpd.service
```

>注：`systemctl`后的服务名可以到`/lib/systemd/system/`目录查看，`opensuse`发行版是位于`/usr/lib/systemd/system`下。


#### service配置文件

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
