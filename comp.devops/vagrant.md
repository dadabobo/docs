## Vagrant

Vagrant是一个基于Ruby的工具，用于创建和部署虚拟化开发环境。Vagrant用于创建和配置轻量、可再制和便携的开发环境。

Vagrant的基本工作原理
* 首先，通过读取配置文件（Vagrantfile），获知用户需要的环境的操作系统、网络配置、基础软件等信息；
* 然后，调用虚拟化管理软件的API（VMWare Fusion，Oracle VirtualBox, AWS, OpenStack等）为用户创建好基础环境；
* 最后，调用用户定义的安装脚本（shell，puppet，chef）安装好相应的服务和软件包；

Vagrant的主要应用场景
* 开发环境部署
* 测试环境部署

Vagrant的主要概念
* **Provider**
  指的是为Vagrant提供虚拟化支持的具体软件，比如vmware或virtual box。
* **Box**
  代表虚拟机镜像。
* **Project**
  一个目录和目录中的Vagrantfile就组成了vagrant的一个项目，项目下可以有子项目。
  子项目中的Vagrantfile配置将继承和重写父项目的配置。
* **Vagrantfile**
  Vagrant的配置文件，使用Ruby的语法描述。
* **Provisioning**
  指的是虚拟机实例启动后，所需要完成的基础配置工作。
  Vagrant支持使用shell，puppet，chef来完成provisioning工作。
* **Plugin**
  Vagrant提供了插件机制，可以扩展对宿主机OS, GuestOS，Provider，Provisioner的支持。

`Vagrantfile`示例
```ruby
Vagrant.configure("2") do |config|
	config.vm.box = "hashicorp/precise64"
end	  
```

#### Multimachine Cluters
Vagrant支持管理多个虚拟机。  

`config.vm.define` 配置指令可以在一个`Vagrantfile`中定义新的机器。

`Vagrantfile`示例
```ruby
Vagrant::Config.run do |config|
  config.vm.box = "precise64"

  config.vm.define "web" do |web|
    web.vm.forwarded_port 80, 8080
    web.vm.provision :shell, path: "provision.sh"
    web.vm.network :hostonly, "192.168.33.10"
  end

  config.vm.define "db" do |db|
    db.vm.provision :shell, path: "db_provision.sh"
    db.vm.network :hostonly, "192.168.33.11"
  end
end
```


**Networking**  
[vagrant doc](https://www.vagrantup.com/docs/networking/)

`config.vm.network "forwarded_port", guest: 80, host: 8080`

Forwarded Ports 
`config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true` 
`config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: 127.0.0.1, auto_correct: true`

Private Networks  

DHCP
`config.vm.network "private_network", type: "dhcp"`  
Static IP
`config.vm.network "private_network", ip: "192.168.50.4", auto_config: false`
IPv6
`config.vm.network "private_network", ip: "fde4:8dba:82e1::c4"`

Public Networks  
DHCP  
`config.vm.network "public_network"`    
`config.vm.network "public_network", use_dhcp_assigned_default_route: true`    
Static IP  
`config.vm.network "public_network", ip: "192.168.0.17"`
Default Network Interface  
`config.vm.network "public_network", bridge: "en1: Wi-Fi (AirPort)"`  
```
config.vm.network "public_network", bridge: [
  "en1: Wi-Fi (AirPort)",
  "en6: Broadcom NetXtreme Gigabit Ethernet Controller",
]
```
Disable Auto-Configuration  
`config.vm.network "public_network", auto_config: false`  

Default Router 

Multiple Networks 
Multiple networks can be defined by having multiple `config.vm.network` calls within the Vagrantfile. The exact meaning of this can differ for each provider, but in general the order specifies the order in which the networks are enabled.


#### Vagrant plugin
* [ Available Vagrant Plugins](https://github.com/hashicorp/vagrant/wiki/Available-Vagrant-Plugins)
* `vagrant-share` Vagrant系统的共享，支持HTTP sharing/SSH sharing / General sharing
* `vagrant-vbguest` 支持 vboxfs类型的共享目录
* `vagrant-hostmanager` 自动更新绑定IP的配置文件
* `vagrant-winnfsd` NFS support for Windows hosts
* `vagrant-berkshelf` 实现和 Berkshelf cookbook manager 的通信
* `vagrant-omnibus` 检查Chef 安装版本是否匹配
* `vagrant-puppet-install` 检查Puppet 安装版本是否匹配
* `vagrant-ansible-local` vagrant plugin to provision VM with ansible in local mode
* `vagrant-sshfs` SSHFS synced folder implementation for Vagrant.

Install Vagrant plugin
```
vagrant plugin install vagrant-share
vagrant plugin install vagrant-hostmanager
```

