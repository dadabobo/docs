---
html:
  embed_local_images: true
  embed_svg: true
  offline: false
  toc: Ansible

print_background: false
---
# Ansible
## Preface
###### 参考资料
* [Ansible](http://www.ansible.com/)
* [Ansible中文权威指南](http://www.ansible.com.cn/)
* [Ansible_Up-And-Running笔记](http://www.jianshu.com/p/f0cf027225df?hmsr=toutiao.io)
* [自动化工具 ansible中文指南](http://www.aikaiyuan.com/6299.html)
* [Ansible 起步指南](https://linux.cn/article-8112-1.html)
* [Ansible来了](http://ju.outofmemory.cn/entry/67581)
* [Ansible简介](http://www.ansible.cn/thread-7-1-1.html)
* [Ansible完整基础简介](http://chuansong.me/n/1428806851433)

###### 内容
* [概要](#summary)
* [ssh公钥认证](#sshca)
* [Ansible命令语法](#command)
* [Ansible简介](#getstart)
* [Amsible架构](#ansible-architechture)
* [Ansible Playbok](#ansible-playbook)
* [Ansible的roles介绍和实战](#ansible-palybook-roles)
* [Ansible Galaxy](#ansible-galaxy)
* [Ansible roles示例](#role-example)
* [ansible.cfg 示例](#ansible-cfg)

---
<span id="summary"></span>
## 概要
###### ansible命令
```
ansible <host-pattern> [-f forks] [-m module_name] [-a args]
```

###### ansible palybook  
`development.yml` -- 基于role配置开发环境  
`ansible-playbook development.yml`

###### ssh 设置
```
ssh-keygen
ssh-copy-id root@目标节点IP
ansible -m ping all

# 本机 vagrant 用户
ssh-copy-id vagrant@localhost
```

###### 配置文件读取的顺序  
* `ANSIBLE_CONFIG` (一个环境变量)
* `ansible.cfg` (位于当前目录中)
* `.ansible.cfg` (位于家目录中)
* `/etc/ansible/ansible.cfg`

###### Inventory
* 缺省： `/etc/ansible/hosts`
* 可以使用 `-i <path>` 命令选项指定路径
* 可以在 `ansible.cfg` 中定义
```
[defaults]
inventory      = /data/ansible/inventory
```

###### ansible.cfg
缺省的配置 (`/etc/ansible/ansible.cfg`)
```
[persistent_connection]
connect_timeout = 30
connect_retries = 30
connect_interval = 1
```

###### 常用命令
使用Facts获取信息  
`ansible hostname -m setup`

<span id="sshca"></span>
## ssh公钥认证
#### ssh 公钥认证 原理
* 在 **控制主机** 创建SSH认证文件  
  `ssh-keygen` 生成 `is_rsa` 和 `id_rsa.pub`
* 将 **控制主机** 的公钥文件 `id_rsa.pub` 添加到 **被控制主机** 的 `~/.ssh/authorized_keys`。

#### 分发ssh登陆认证公钥
方式一： `ssh-copy-id`  
```bash
ssh-copy-id username@remote   
ansible -m ping test
```

方式二： `ansible copy/shell`    
```bash
## 将 公钥文件 分发到 远程主机  
ansible test -m copy -a "src=/home/vagrant/.ssh/id_rsa.pub dest=/tmp/id_rsa.pub" --ask-pass
## 把 公钥文件 添加到 远程服务器中  
ansible test -m shell -a "cat /tmp/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys" -k
ansible -m ping test
```

方式三： `ansible authorized_key`   
`ansible test -m authorized_key -a "user=vagrant key='{{ lookup('file', '/home/vagrant/.ssh/id_rsa.pub') }}' path=/home/vagrant/.ssh/authorized_keys manage_dir=no" --ask-pass -c paramiko`

#### 使用 playbook 分发 ssh key
sshca_hosts
```bash
# hosts
localhost ansible_connection=local
[test]
node-01  ansible_host=192.168.xx.xx ansible_connection=ssh ansible_user=vagrant ansible_ssh_pass="password"
testvbox ansible_host=192.168.xx.xx ansible_connection=ssh ansible_user=vagrant ansible_ssh_pass="password"
```
sshca_hosts
```
[tests]
test01  ansible_host=192.168.99.31
test02  ansible_host=192.168.99.32
test03  ansible_host=192.168.99.33
test    ansible_host=192.168.99.22

[tests:vars]
ansible_connection=ssh
ansible_user=vagrant
ansible_ssh_pass="vagrant"
```

roles/wangwg2.sshca/defaults/main.yml
```yaml
---
# defaults file for wangwg2.sshca
addkey: no
```
roles/wangwg2.sshca/tasks/main.yml
```yaml
---
# tasks file for wangwg2.sshca
- name: Set authorized key took from file
  authorized_key:
    user: vagrant
    state: present
    key: "{{ lookup('file', '/home/vagrant/.ssh/id_rsa.pub') }}"
  when: addkey

- name: Remove authorized key took from file
  authorized_key:
    user: vagrant
    state: absent
    key: "{{ lookup('file', '/home/vagrant/.ssh/id_rsa.pub') }}"
  when: not addkey
```

sshca_pbk.yml
```yaml
## distrubute ssh pub key to remote hosts
- hosts: vbox
  remote_user: vagrant
  become: yes
  roles:
    - wangwg2.sshpubkey
```
运行: `ansible-playbook -i ./sshca_hosts sshca_pbk`


<span id="command"></span>
## Ansible命令语法
Usage: `ansible <host-pattern> [options]`
```
Options:
  -a MODULE_ARGS, --args=MODULE_ARGS
                        module arguments
  --ask-vault-pass      ask for vault password
  -B SECONDS, --background=SECONDS
                        run asynchronously, failing after X seconds
                        (default=N/A)
  -C, --check           don't make any changes; instead, try to predict some
                        of the changes that may occur
  -D, --diff            when changing (small) files and templates, show the
                        differences in those files; works great with --check
  -e EXTRA_VARS, --extra-vars=EXTRA_VARS
                        set additional variables as key=value or YAML/JSON
  -f FORKS, --forks=FORKS
                        specify number of parallel processes to use
                        (default=5)
  -h, --help            show this help message and exit
  -i INVENTORY, --inventory-file=INVENTORY
                        specify inventory host path
                        (default=/data/ansible/hosts) or comma separated host
                        list.
  -l SUBSET, --limit=SUBSET
                        further limit selected hosts to an additional pattern
  --list-hosts          outputs a list of matching hosts; does not execute
                        anything else
  -m MODULE_NAME, --module-name=MODULE_NAME
                        module name to execute (default=command)
  -M MODULE_PATH, --module-path=MODULE_PATH
                        specify path(s) to module library (default=None)
  --new-vault-password-file=NEW_VAULT_PASSWORD_FILE
                        new vault password file for rekey
  -o, --one-line        condense output
  --output=OUTPUT_FILE  output file name for encrypt or decrypt; use - for
                        stdout
  -P POLL_INTERVAL, --poll=POLL_INTERVAL
                        set the poll interval if using -B (default=15)
  --syntax-check        perform a syntax check on the playbook, but do not
                        execute it
  -t TREE, --tree=TREE  log output to this directory
  --vault-password-file=VAULT_PASSWORD_FILE
                        vault password file
  -v, --verbose         verbose mode (-vvv for more, -vvvv to enable
                        connection debugging)
  --version             show program's version number and exit
```

###### Connection Options:
control as whom and how to connect to hosts  
```
-k, --ask-pass      ask for connection password
--private-key=PRIVATE_KEY_FILE, --key-file=PRIVATE_KEY_FILE
                    use this file to authenticate the connection
-u REMOTE_USER, --user=REMOTE_USER
                    connect as this user (default=None)
-c CONNECTION, --connection=CONNECTION
                    connection type to use (default=smart)
-T TIMEOUT, --timeout=TIMEOUT
                    override the connection timeout in seconds
                    (default=10)
--ssh-common-args=SSH_COMMON_ARGS
                    specify common arguments to pass to sftp/scp/ssh (e.g.
                    ProxyCommand)
--sftp-extra-args=SFTP_EXTRA_ARGS
                    specify extra arguments to pass to sftp only (e.g. -f,
                    -l)
--scp-extra-args=SCP_EXTRA_ARGS
                    specify extra arguments to pass to scp only (e.g. -l)
--ssh-extra-args=SSH_EXTRA_ARGS
                    specify extra arguments to pass to ssh only (e.g. -R)
```

###### Privilege Escalation Options:
control how and which user you become as on target hosts
```
-s, --sudo          run operations with sudo (nopasswd) (deprecated, use
                    become)
-U SUDO_USER, --sudo-user=SUDO_USER
                    desired sudo user (default=root) (deprecated, use
                    become)
-S, --su            run operations with su (deprecated, use become)
-R SU_USER, --su-user=SU_USER
                    run operations with su as this user (default=root)
                    (deprecated, use become)
-b, --become        run operations with become (does not imply password
                    prompting)
--become-method=BECOME_METHOD
                    privilege escalation method to use (default=sudo),
                    valid choices: [ sudo | su | pbrun | pfexec | doas |
                    dzdo | ksu ]
--become-user=BECOME_USER
                    run operations as this user (default=root)
--ask-sudo-pass     ask for sudo password (deprecated, use become)
--ask-su-pass       ask for su password (deprecated, use become)
-K, --ask-become-pass
                    ask for privilege escalation password
```

###### Ansible 命令
`ansible <host-pattern> [-f forks] [-m module_name] [-a args]`

常用模块说明
* `command` - 命令模块：这也是默认的模块，也就是不加-m指定模块时默认的模块，这个模块不能使用包含管道的命令。
* `shell` - shell模块：shell模块也是可以执行命令，与comman模块不同的时，command模块不能执行包含管道的命令，而shell可以
* `copy` - copy模块：可以把本机的文件拷贝至被管理的机器，通常用于分发配置文件
* `cron`模块：分发定期任务
* `yum` -	yum模块：顾名思义，该模块可以管理软件的安装和卸载，state=present（安装） adsent（卸载）
* `service` - service模块


---
<span id="getstart"></span>
## Ansible 简介
###### Ansible 介绍
* 大部分服务器自动化及流程解决方案，例如Puppet与Chef，都依赖于特定方案编码、Web UI以及命令行工具等要素的综合体，从而使整套体系正常运转。Ansible则有所不同。尽管也能够支持Web UI，Ansible在Unix管理员的监管范围内同样作用良好，即使用大量通用脚本以及命令行机制。
* Ansible是一款极为灵活的开源工具套件，能够大大简化Unix管理员的自动化配置管理与流程控制方式。它利用推送方式对客户系统加以配置，这样所有工作都可在主服务器端完成。其命令行机制同样非常强大，允许大家利用商业许可Web UI实现授权管理与配置。
* Ansible是一个综合的强大的管理工具,他可以对多台主机安装操作系统,并为这些主机安装不同的应用程序,也可以通知指挥这些主机完成不同的任务.查看多台主机的各种信息的状态等,Ansible都可以通过模块的方式来完成。

Ansible是新出现的自动化运维工具，基于Python开发，集合了众多运维工具（puppet、cfengine、chef、func、fabric）的优点，实现了批量系统配置、批量程序部署、批量运行命令等功能。ansible是基于模块工作的，本身没有批量部署的能力。真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架。主要包括：
* 连接插件connection plugins：负责和被监控端实现通信；
* host inventory：指定操作的主机，是一个配置文件里面定义监控的主机；
* 各种模块核心模块、command模块、自定义模块；
* 借助于插件完成记录日志邮件等功能；
* playbook：剧本执行多个任务时，非必需可以让节点一次性运行多个任务。

###### Ansible 特性
* No agents：不需要再被管理节点上安装客户端，只要有sshd即可
* No server：在服务端不需要启动任何服务，只需要执行命令就行
* No additional PKI：由于不基于ssl，所以也不基于PKI工作
* Modules in any language：基于模块工作，ansible拥有众多的模块，可使用任意语言开发模块；
* YAML：支持YAML语法，使用yaml语言定制剧本playbook；
* SSH by default：默认使用ssh控制各节点，基于SSH工作；
* strong multi-tier solution：可实现多级指挥。

Ansible 优点
* 轻量级，无需在客户端安装agent，更新时，只需在操作机上进行一次更新即可；
* 批量任务执行可以写成脚本，而且不用分发到远程就可以执行；
* 使用python编写，维护更简单，ruby语法过于复杂；
* 支持sudo；


<span id="ansible-architechture"></span>
## Ansible架构
#### Ansible Architecture   
@import "img/ansible/ansible-architechture.png"

Ansible由5个部分组成：
* Ansible：核心
* Modules：包括 Ansible 自带的核心模块及自定义模块
* Plugins：完成模块功能的补充，包括连接插件、邮件插件等
* Playbooks：剧本，定义 Ansible 多任务配置文件，由Ansible自动执行
* Inventory：定义 Ansible 管理主机的清单

Ansible的特性：
* Ansible 无需安装服务端和客户端，只要 SSH 即可。这意味着，任何一台装有 Ansible 的机器都可以成为强大的管理端
* 模块化，调用特定的模块，完成特定的任务
* 基于Python语言实现，由Paramiko、PyYAML和Jinja2三个关键模块；
* 部署简单，轻量级，无需在客户端安装agent，更新时，只需在操作机上进行一次更新即可
* 主从模式，支持自定义模块
* 支持Playbook，幂等性，一个playbook多次执行结果相同

#### Ansible是如何工作的

Ansible没有客户端，因此底层通信依赖于系统软件，Linux系统下基于OpenSSH通信，Windows系统下基于PowerShell，管理端必须是Linux系统，使用者认证通过后在管理节点通过Ansible工具调用各应用模块将指令推送至被管理端执行并在执行完毕后自动删除产生的临时文件。根据Ansible使用过程中的不同角色，我们将其分别：使用者、Ansible工具集、作用对象。

**使用者**  

Ansible使用者来源于多种维度，图中为我们展示了4种角度：
* 第一种方式： CMDB（全称Configuration Management Database，中文名称为配置管理数据库）,CMDB存储和管理企业IT架构中的各项配置信息，其是构建ITIL项目的核心工具，运维人员可以组合CMDB和Ansible，通过CMDB直接下发指令调用Ansible工具集完成操作者所希望达成的目标；
* 第二种方式：PUBLIC/PRIVATE方式，Ansible除了丰富的内置模块外，同时提供丰富的API语言接口，如PHP、PYTHON、PERL等多种当下流行语言，基于PUBLIC（公有云）/PRIVATE（私有云），Ansible以API调用的方式运行；
* 第三种方式：USERS直接使用Ad-Hoc临时命令集调用Ansible工具集来完成任务执行；
* 第四种方式：USERS预先编写好的ANSIBLE PLAYBOOKS，通过执行Playbooks中预先编排好的任务集按序完成任务执行。

**Ansible工具集**  

ansible命令是Ansible的核心工具，ansible命令并非自身完成所有的功能集，其只是Ansible执行任务的调用入口，大家可心理解为“总指挥”，所有命令的执行通过其“调兵遣将”最终完成目标。ansible命令共有哪些兵将可供使唤呢？大家看中间绿色框中：`INVENTORY`（命令执行的目标对象配置文件）、`API`（供第三方程序调用的应用程序编程接口）、`MODULES`（丰富的内置模块）、`PLUGINS`（内置和可自定义的插件）共有这些可供调遣。

**作用对象**

Ansible的作用对象，不仅仅是Linux和非Linux操作系统的主机（HOSTS），同样也可以作用于各类公有云/私有云，商业和非商业设备的网络设施（NETWORKING）。  

同样，如果我们按Ansible工具集的组成来讲，由图1-1可以看出Ansible主要由6部分组成：
* `ANSIBLE PLAYBOOKS`：任务剧本（任务集），编排定义Ansible任务集的配置文件，由Ansible顺序依次执行，通常是JSON格式的YML文件；
* `INVENTORY`：Ansible管理主机的清单；
* `MODULES`：Ansible执行命令的功能模块，多数为内置的核心模块，也可自定义；
* `PLUGINS`：模块功能的补充，如连接类型插件、循环插件、变量插件、过滤插件等，该功能不常用。
* `API`：供第三方程序调用的应用程序编程接口；
* `ANSIBLE`：该部分图中表示的不明显，组合INVENTORY、API、MODULES、PLUGINS的绿框大家可以理解为是ansible命令工具，其为核心执行工具；

Ansible执行任务，这5部分组件相互调用关系如图所示：
@import "img/ansible/ansible-architechture02.png"

使用者使用`Ansible`或`Ansible-playbook`（会额外读取Playbook文件）时，在服务器终端输入Ansible的Ad-Hoc命令集或Playbook后，Ansible会遵循预先编排的规则将`Playbooks`逐条拆解为`Play`，再将`Play`组织成Ansible可识别的任务（`Task`），随后调用任务涉及的所有模块（`Module`）和插件（`Plugin`），根据`Inventory`中定义的主机列表通过`SSH`（Linux默认）将任务集以临时文件或命令的形式传输到远程客户端执行并返回执行结果，如果是临时文件则执行完毕后自动删除。

#### Ansible通信发展史
Ansible主推的卖点是其无需任何Daemon维护进程即可实现相互间的通信，且通信方式是基于业内统一标准的安全可靠的SSH安全连接，同时因为SSH是每台Linux主机系统必装的软件，所以Ansible无需在远程主机端安装任何额外进程，从而实现Agentless（无客户端），既而助力其实现去中心化的思想。尽管稳定、快速、安全的SSH连接是Ansible通信能力的核心，但SSH的连接效率一直被诟病，所以Ansible的通信方式和效率在过去的数年也在不停地改变和提高。基于以上认识，我们先来了解Ansible SSH的工作机制，再来回顾其发展史。

Ansible 执行命令时，通过其底层传输连接模块，将一个或数个文件或者定义一个Play（中文也称剧本）或者Command命令传输到远程服务器/tmp目录的临时文件，并在远程执行这些 Play/Comand命令，然后删除这些临时文件，同时回传整体命令执行结果。这一系列操作在未来的 Ansible 版本中会越来越简单直接，同时快速稳定安全。通过了解其工作机制，及其一直以来秉承的去中心化思想，我们可以总结是Ansible非C/S架构，自身没有Client端，

Ansible底层基于安全可靠的SSH协议通信，但一直诟病于其效率，通信功能作为Ansible最核心的功能之一，官方也一直在做改进，本节我们来了解Ansible通信发展史。
* Paramiko通信模块  
  最起初, Ansible 只使用 Paramiko实现底层通信功能但是，Paramiko只是Python的语法的一个第三方库，发展速度远不及 OpenSSH。与此同时，Paramiko的性能和安全性较OpenSSH 来比稍逊一筹（在其笔者眼里）。  
  在后续发布的新版本中，Ansible 仍继续兼容 Paramiko，甚至在诸如RHEL 5/6等不支持 ControlPersist(只在OpenSSH5.6+版本中支持)的系统中封装其为默认通信模块。
* OpenSSH  
  从Ansible 1.3版本开始，Ansible默认使用 OpenSSH 连接实现各服务器间通信以支持 ControlPersist（持续管理）。Ansible 从0.5版本即支持OpenSSH功能，但直到1.3版本开始才将其设置为默认。  
  多数本地 SSH 配置参数诸如Hosts、公私钥文件等是被默认支持的，但如果希望通过非默认的22端口运行命令等所有操作,则需要在Inventory文件配置ansible_ssh_port的值。OpenSSH 相对 Paramiko 更快更可靠。
* 加速模式  
  据官网介绍：开启加速模式后Ansible通信速度有质的提升，是开启ControlPersist后的SSH的2～6倍，是Paramiko通信速度的10倍。
  尽管加速模式对Ad-Hoc命令不友好，但是 Playbook 通过加速模式会收到更高的性能。加速模式抛弃SSH多次连接的方式，通过 SSH 初始化后，带着 AES key的初始化连接信息通过特定的端口（默认5099，但可配置）执行命令传输文件。使用加速模式唯一需要的额外包是python-keyczar，如此以来几乎所有的常规模块 OpenSSH/Paramiko 均工作在加速模式，但使用sudo的两种情况例外
* Faster OpenSSH in Ansible 1.5+
  Ansible 1.5+版本中的OpenSSH有了非常大的改进，旧版本中实现方式是复制文件至远程服务器后运行，然后删除这些临时文件，而新版本的替代方案是通过OpenSSH发送执行命令，将所有操作附带在SSH连接过程同步实现。该方式只在Ansible 1.5+版本有效，且需在/etc/ansible/ansible.cfg的[ssh_connection]区域开启pipelining=True功能。


---
<span id="ansible-playbook"></span>
## Ansible Playbook

Playbooks 的格式是YAML,语法做到最小化,意在避免 playbooks 成为一种编程语言或是脚本,但它也并不是一个配置模型或过程的模型.
* playbook 由一个或多个 ‘plays’ 组成.它的内容是一个以 ‘plays’ 为元素的列表.
* 在 play 之中,一组机器被映射为定义好的角色.在 ansible 中,play 的内容,被称为 tasks,即任务.在基本层次的应用中,一个任务是一个对 ansible 模块的调用,这在前面章节学习过.
* ‘plays’ 好似音符,playbook 好似由 ‘plays’ 构成的曲谱,通过 playbook,可以编排步骤进行多机器的部署,比如在 webservers 组的所有机器上运行一定的步骤, 然后在 database server 组运行一些步骤,最后回到 webservers 组,再运行一些步骤,诸如此类.
* “plays” 算是一个体育方面的类比,你可以通过多个 plays 告诉你的系统做不同的事情,不仅是定义一种特定的状态或模型.你可以在不同时间运行不同的 plays.

palybook 文件示例
```yaml
---
- hosts: webservers
  vars:
    http_port: 80
    max_clients: 200
  remote_user: root
  tasks:
  - name: ensure apache is at the latest version
    yum: name=httpd state=latest
  - name: write the apache config file
    template: src=/srv/httpd.j2 dest=/etc/httpd.conf
    notify:
    - restart apache
  - name: ensure apache is running (and enable it at boot)
    service: name=httpd state=started enabled=yes
  handlers:
    - name: restart apache
      service: name=httpd state=restarted
```

当模块有太长参数时，可以
```yaml
---
- hosts: webservers
  vars:
    http_port: 80
    max_clients: 200
  remote_user: root
  tasks:
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest
  - name: write the apache config file
    template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf
    notify:
    - restart apache
  - name: ensure apache is running
    service:
      name: httpd
      state: started
  handlers:
    - name: restart apache
      service:
        name: httpd
        state: restarted
```

包含多个play的playbook
```yaml
---
- hosts: webservers
  remote_user: root

  tasks:
  - name: ensure apache is at the latest version
    yum: name=httpd state=latest
  - name: write the apache config file
    template: src=/srv/httpd.j2 dest=/etc/httpd.conf

- hosts: databases
  remote_user: root

  tasks:
  - name: ensure postgresql is at the latest version
    yum: name=postgresql state=latest
  - name: ensure that postgresql is started
    service: name=postgresql state=started
```


#### 主机与用户
你可以为 playbook 中的每一个 play,个别地选择操作的目标机器是哪些,以哪个用户身份去完成要执行的步骤（called tasks）.
hosts 行的内容是一个或多个组或主机的 patterns,以逗号为分隔符。

```yaml
---
- hosts: webservers
  remote_user: yourname
  tasks:
    - service: name=nginx state=started
      become: yes
      become_method: sudo
```

#### Tasks 列表

* 每一个 play 包含了一个 task 列表（任务列表）.一个 task 在其所对应的所有主机上（通过 host pattern 匹配的所有主机）执行完毕之后,下一个 task 才会执行.有一点需要明白的是（很重要）,在一个 play 之中,所有 hosts 会获取相同的任务指令,这是 play 的一个目的所在,也就是将一组选出的 hosts 映射到 task.
* 在运行 playbook 时（从上到下执行）,如果一个 host 执行 task 失败,这个 host 将会从整个 playbook 的 rotation 中移除. 如果发生执行失败的情况,请修正 playbook 中的错误,然后重新执行即可.
* 每个 task 的目标在于执行一个 moudle, 通常是带有特定的参数来执行.在参数中可以使用变量（variables）.
* modules 具有”幂等”性,意思是如果你再一次地执行 moudle（译者注:比如遇到远端系统被意外改动,需要恢复原状）,moudle 只会执行必要的改动,只会改变需要改变的地方.所以重复多次执行 playbook 也很安全.
* 对于 command module 和 shell module,重复执行 playbook,实际上是重复运行同样的命令.如果执行的命令类似于 ‘chmod’ 或者 ‘setsebool’ 这种命令,这没有任何问题.也可以使用一个叫做 ‘creates’ 的 flag 使得这两个 module 变得具有”幂等”特性 （不是必要的）.
* 每一个 task 必须有一个名称 name,这样在运行 playbook 时,从其输出的任务执行信息中可以很好的辨别出是属于哪一个 task 的. 如果没有定义 name,‘action’ 的值将会用作输出信息中标记特定的 task.
* 如果要声明一个 task,以前有一种格式: “action: module options” （可能在一些老的 playbooks 中还能见到）.现在推荐使用更常见的格式:”module: options” ,本文档使用的就是这种格式.

#### Handlers
在发生改变时执行的操作。

----
<span id="ansible-palybook-roles"></span>
## Ansible的roles介绍和实战
* roles 用于层次性、结构化地组织playbook。
* roles 能够根据层次型结构自动装载变量文件、tasks以及handlers等。
* 要使用roles只需要在playbook中使用`include`指令即可。
* 简单来讲，roles就是通过分别将变量(`vars`)、文件(`file`)、任务(`tasks`)、模块(`modules`)及处理器(`handlers`)放置于单独的目录中，并可以便捷地`include`它们的一种机制。
* 角色一般用于基于主机构建服务的场景中，但也可以是用于构建守护进程等场景中。

**创建 roles 的步骤**
1. 创建以roles命名的目录；
2. 在roles目录中分别创建以各角色名称命名的目录，如webservers等。注意：在 roles 必须包括 `site.yml`文件，可以为空；
3. 在每个角色命名的目录中分别创建`files`、`handlers`、`meta`、`tasks`、`templates`和`vars`目录；用不到的目录可以创建为空目录，也可以不创建；
4. 在`playbook`文件中，调用各角色；

**roles 内各目录中可用的文件**
* `tasks`目录：至少应该包含一个名为`main.yml`的文件，其定义了此角色的任务列表；此文件可以使用`include`包含其它的位于此目录中的`task`文件；
* `files`目录：存放由`copy`或`script`等模块调用的文件；
* `templates`目录：`template`模块会自动在此目录中寻找`Jinja2`模板文件；
* `handlers`目录：此目录中应当包含一个`main.yml`文件，用于定义此角色用到的各`handler`；在`handler`中使用`include`包含的其它的`handler`文件也应该位于此目录中；
* `vars`目录：应当包含一个`main.yml`文件，用于定义此角色用到的变量；
* `meta`目录：应当包含一个`main.yml`文件，用于定义此角色的特殊设定及其依赖关系；
* `default`目录：为当前角色设定默认变量时使用此目录；应当包含一个`main.yml`文件。

**建立 `roles`目录结构**
* 建立`roles`目录结构  
  `mkdir -pv roles/{roleA,roleB}/{tasks,handlers,files,vars,templates,meta,default}`  
* 初始化一个新角色的基本文件结构  
  `ansible-galaxy init username.rolename`


## 示例
###### Ansible playbook 目录结构示例
````shell
production                # inventory file for production servers 关于生产环境服务器的清单文件
stage                     # inventory file for stage environment 关于 stage 环境的清单文件

group_vars/
   group1                 # here we assign variables to particular groups 这里我们给特定的组赋值
   group2                 # ""
host_vars/
   hostname1              # if systems need specific variables, put them here 如果系统需要特定的变量,把它们放置在这里.
   hostname2              # ""

library/                  # if any custom modules, put them here (optional) 如果有自定义的模块,放在这里(可选)
filter_plugins/           # if any custom filter plugins, put them here (optional) 如果有自定义的过滤插件,放在这里(可选)

site.yml                  # master playbook 主 playbook
webservers.yml            # playbook for webserver tier Web 服务器的 playbook
dbservers.yml             # playbook for dbserver tier 数据库服务器的 playbook

roles/
    common/               # this hierarchy represents a "role" 这里的结构代表了一个 "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies

    webtier/              # same kind of structure as "common" was above, done for the webtier role
    monitoring/           # ""
    fooapp/               # ""
````

###### role目录示例
```
wamngwg2.roleA    # 角色
  defaults        # 当前角色默认变量
    main.yml
  files           # copy 或 脚本需要的文件
  handlers        #
    main.yml
  meta            # 特殊设定与依赖关系
    main.yml    
  tasks           # 任务
    main.yml
  templates       # 模板文件
  tests           # 角色测试
    inventory
    test.yml
  vars            # 角色相关变量
    main.yml
  .travis.yml     # 持续集成工具 Travis 项目配置文件
  README.md       #
```

###### roles 文件示例    
* `meta/main.yml`  
  `meta/main.yml` 用来配置模块一些元信息，比如支持的平台，Ansible最小依赖版本，这个模块的标签，依赖，作者的信息等等。  
  ````yaml
  galaxy_info:
    author: your name
    description: your description
    company: your company (optional)
    license: BSD
    min_ansible_version: 1.9
    platforms:
    - name: Ubuntu
      versions:
      - all
      #  - trusty
      #  - xenial
    - name: EL
      versions:
      - all
    galaxy_tags: [system, development]
  dependencies: []
  ````

* `task/main.yml`  
  是这个role的入口文件，命令从main.yml开始执行

* `files`  
  `files` 目录是放一些文件用的，比如可以用copy命令，把files目录下的文件复制到服务器上

* `templates`  
  用于jinjia2模板文件

* `defaults/main.yml`  
  默认的配置变量写在`defaults/main.yml`里  

* `handlers/main.yml`   
  一般用来重启服务，比如
  ````yaml
  ---
  # this might be in a file like handlers/handlers.yml
  - name: restart apache
   service: name=apache state=restarted
  ````



---
<span id="ansible-galaxy"></span>
## Ansible Galaxy
“Ansible Galaxy” 指的是一个网站共享和下载 Ansible 角色,也可以是者是帮助 roles 更好的工作的命令行工具。  
你可以使用 social auth 注册和使用 “ansible-galaxy” 下载客户端，”ansible-galaxy”在 Ansible 1.4.2 就已经被包含了。  
ansible-galaxy 有许多不同的子命令

**安装角色**  
很明显从 Ansible Galaxy 网站下载角色
```
ansible-galaxy install username.rolename
ansible-galaxy install --roles-path . username.rolename
```

**构建角色架构**  
也可以用于初始化一个新角色的基本文件结构，节省创建不同的目录和main.yml的时间了。  
`ansible-galaxy init rolename`  
```
rolename/
  .travis.yml
  README.md
  defaults/
  files/
  handlers/
  meta/
  tasks/
  templates/
  tests/
  vars/
```



<span id="role-example"></span>
## roles 示例
建立目录结构：  
 `mkdir -pv roles/{roleA,roleB}/{tasks,handlers,files,vars,templates,meta,default}`  
 或  
`ansible-galaxy init username.rolename`

**hosts**  
```yaml
[http]
192.168.100.131
[mysql]
192.168.100.132
[lamp]
192.168.100.130
```

roles/http/tasks/main.yml
````yaml 
- name: install httpd service
  yum: name=httpd state=present
- name: start httpd service
  service: name=httpd state=started enabled=true
- name: modify httpd config file from template  
  template: src=httpd.conf dest=/etc/httpd/conf/httpd.conf
  tags:
   - modifyhttpconf
  notify:
   - restart httpd service
````

roles/http/handlers/main.yml
````yaml
- name: restart httpd service
  service: name=httpd state=restarted
````

roles/http/vars/main.yml   
````yaml
listen:
  - "{{ansible_all_ipv4_addresses.0}}"
  - 8080
host_fqdn:
  - "{{ansible_nodename}}"
````

site.yml   
````yaml
- hosts: http
  remote_user: root
  roles:
   - { role: http, http_port: 2020,ServerName: "{{ansible_nodename}}"}
````


roles/http/tasks/main.yml  
````yaml
- name: install httpd service
  yum: name=httpd state=present

- name: start httpd service
  service: name=httpd state=started enabled=true

- name: modify httpd config file from template  
  template: src=httpd.conf dest=/etc/httpd/conf/httpd.conf
  tags:
   - modifyhttpconf
  notify:
   - restart httpd service
````

<span id="ansible-cfg"></span>
## ansible.cfg 示例

```bash
# config file for ansible -- http://ansible.com/
# ==============================================

# nearly all parameters can be overridden in ansible-playbook
# or with command line flags. ansible will read ANSIBLE_CONFIG,
# ansible.cfg in the current working directory, .ansible.cfg in
# the home directory or /etc/ansible/ansible.cfg, whichever it
# finds first

[defaults]

# some basic default values...
inventory      = /vagrant/inventory
#inventory      = /etc/ansible/hosts
#library        = /usr/share/my_modules/
#remote_tmp     = $HOME/.ansible/tmp
#local_tmp      = $HOME/.ansible/tmp
#forks          = 5
#poll_interval  = 15
sudo_user      = root
#ask_sudo_pass = True
#ask_pass      = True
#transport      = smart
#remote_port    = 22
#module_lang    = C
#module_set_locale = False

# plays will gather facts by default, which contain information about
# the remote system.
#
# smart - gather by default, but don't regather if already gathered
# implicit - gather by default, turn off with gather_facts: False
# explicit - do not gather by default, must say gather_facts: True
#gathering = implicit

# by default retrieve all facts subsets
# all - gather all subsets
# network - gather min and network facts
# hardware - gather hardware facts (longest facts to retrieve)
# virtual - gather min and virtual facts
# facter - import facts from facter
# ohai - import facts from ohai
# You can combine them using comma (ex: network,virtual)
# You can negate them using ! (ex: !hardware,!facter,!ohai)
# A minimal set of facts is always gathered.
#gather_subset = all

# some hardware related facts are collected
# with a maximum timeout of 10 seconds. This
# option lets you increase or decrease that
# timeout to something more suitable for the
# environment.
# gather_timeout = 10

# additional paths to search for roles in, colon separated
#roles_path    = /etc/ansible/roles

# uncomment this to disable SSH key host checking
host_key_checking = False

# change the default callback
#stdout_callback = skippy
# enable additional callbacks
#callback_whitelist = timer, mail

# Determine whether includes in tasks and handlers are "static" by
# default. As of 2.0, includes are dynamic by default. Setting these
# values to True will make includes behave more like they did in the
# 1.x versions.
#task_includes_static = True
#handler_includes_static = True

# Controls if a missing handler for a notification event is an error or a warning
#error_on_missing_handler = True

# change this for alternative sudo implementations
#sudo_exe = sudo

# What flags to pass to sudo
# WARNING: leaving out the defaults might create unexpected behaviours
#sudo_flags = -H -S -n

# SSH timeout
#timeout = 10

# default user to use for playbooks if user is not specified
# (/usr/bin/ansible will use current user as default)
#remote_user = root

# logging is off by default unless this path is defined
# if so defined, consider logrotate
#log_path = /var/log/ansible.log
log_path = /vagrant/ansible.log

# default module name for /usr/bin/ansible
#module_name = command

# use this shell for commands executed under sudo
# you may need to change this to bin/bash in rare instances
# if sudo is constrained
#executable = /bin/sh

# if inventory variables overlap, does the higher precedence one win
# or are hash values merged together?  The default is 'replace' but
# this can also be set to 'merge'.
#hash_behaviour = replace

# by default, variables from roles will be visible in the global variable
# scope. To prevent this, the following option can be enabled, and only
# tasks and handlers within the role will see the variables there
#private_role_vars = yes

# list any Jinja2 extensions to enable here:
#jinja2_extensions = jinja2.ext.do,jinja2.ext.i18n

# if set, always use this private key file for authentication, same as
# if passing --private-key to ansible or ansible-playbook
#private_key_file = /path/to/file
private_key_file = ~/vagrant.pem

# If set, configures the path to the Vault password file as an alternative to
# specifying --vault-password-file on the command line.
#vault_password_file = /path/to/vault_password_file

# format of string {{ ansible_managed }} available within Jinja2
# templates indicates to users editing templates files will be replaced.
# replacing {file}, {host} and {uid} and strftime codes with proper values.
#ansible_managed = Ansible managed: {file} modified on %Y-%m-%d %H:%M:%S by {uid} on {host}
# {file}, {host}, {uid}, and the timestamp can all interfere with idempotence
# in some situations so the default is a static string:
#ansible_managed = Ansible managed

# by default, ansible-playbook will display "Skipping [host]" if it determines a task
# should not be run on a host.  Set this to "False" if you don't want to see these "Skipping"
# messages. NOTE: the task header will still be shown regardless of whether or not the
# task is skipped.
#display_skipped_hosts = True

# by default, if a task in a playbook does not include a name: field then
# ansible-playbook will construct a header that includes the task's action but
# not the task's args.  This is a security feature because ansible cannot know
# if the *module* considers an argument to be no_log at the time that the
# header is printed.  If your environment doesn't have a problem securing
# stdout from ansible-playbook (or you have manually specified no_log in your
# playbook on all of the tasks where you have secret information) then you can
# safely set this to True to get more informative messages.
#display_args_to_stdout = False

# by default (as of 1.3), Ansible will raise errors when attempting to dereference
# Jinja2 variables that are not set in templates or action lines. Uncomment this line
# to revert the behavior to pre-1.3.
#error_on_undefined_vars = False

# by default (as of 1.6), Ansible may display warnings based on the configuration of the
# system running ansible itself. This may include warnings about 3rd party packages or
# other conditions that should be resolved if possible.
# to disable these warnings, set the following value to False:
#system_warnings = True

# by default (as of 1.4), Ansible may display deprecation warnings for language
# features that should no longer be used and will be removed in future versions.
# to disable these warnings, set the following value to False:
#deprecation_warnings = True

# (as of 1.8), Ansible can optionally warn when usage of the shell and
# command module appear to be simplified by using a default Ansible module
# instead.  These warnings can be silenced by adjusting the following
# setting or adding warn=yes or warn=no to the end of the command line
# parameter string.  This will for example suggest using the git module
# instead of shelling out to the git command.
# command_warnings = False


# set plugin path directories here, separate with colons
#action_plugins     = /usr/share/ansible/plugins/action
#cache_plugins      = /usr/share/ansible/plugins/cache
#callback_plugins   = /usr/share/ansible/plugins/callback
#connection_plugins = /usr/share/ansible/plugins/connection
#lookup_plugins     = /usr/share/ansible/plugins/lookup
#inventory_plugins  = /usr/share/ansible/plugins/inventory
#vars_plugins       = /usr/share/ansible/plugins/vars
#filter_plugins     = /usr/share/ansible/plugins/filter
#test_plugins       = /usr/share/ansible/plugins/test
#strategy_plugins   = /usr/share/ansible/plugins/strategy

# by default callbacks are not loaded for /bin/ansible, enable this if you
# want, for example, a notification or logging callback to also apply to
# /bin/ansible runs
#bin_ansible_callbacks = False


# don't like cows?  that's unfortunate.
# set to 1 if you don't want cowsay support or export ANSIBLE_NOCOWS=1
#nocows = 1
nocows = 1

# set which cowsay stencil you'd like to use by default. When set to 'random',
# a random stencil will be selected for each task. The selection will be filtered
# against the `cow_whitelist` option below.
#cow_selection = default
cow_selection = random

# when using the 'random' option for cowsay, stencils will be restricted to this list.
# it should be formatted as a comma-separated list with no spaces between names.
# NOTE: line continuations here are for formatting purposes only, as the INI parser
#       in python does not support them.
#cow_whitelist=bud-frogs,bunny,cheese,daemon,default,dragon,elephant-in-snake,elephant,eyes,\
#              hellokitty,kitty,luke-koala,meow,milk,moofasa,moose,ren,sheep,small,stegosaurus,\
#              stimpy,supermilker,three-eyes,turkey,turtle,tux,udder,vader-koala,vader,www

# don't like colors either?
# set to 1 if you don't want colors, or export ANSIBLE_NOCOLOR=1
#nocolor = 1

# if set to a persistent type (not 'memory', for example 'redis') fact values
# from previous runs in Ansible will be stored.  This may be useful when
# wanting to use, for example, IP information from one group of servers
# without having to talk to them in the same playbook run to get their
# current IP information.
#fact_caching = memory


# retry files
# When a playbook fails by default a .retry file will be created in ~/
# You can disable this feature by setting retry_files_enabled to False
# and you can change the location of the files by setting retry_files_save_path

#retry_files_enabled = False
#retry_files_save_path = ~/.ansible-retry

# squash actions
# Ansible can optimise actions that call modules with list parameters
# when looping. Instead of calling the module once per with_ item, the
# module is called once with all items at once. Currently this only works
# under limited circumstances, and only with parameters named 'name'.
#squash_actions = apk,apt,dnf,homebrew,package,pacman,pkgng,yum,zypper

# prevents logging of task data, off by default
#no_log = False

# prevents logging of tasks, but only on the targets, data is still logged on the master/controller
#no_target_syslog = False

# controls whether Ansible will raise an error or warning if a task has no
# choice but to create world readable temporary files to execute a module on
# the remote machine.  This option is False by default for security.  Users may
# turn this on to have behaviour more like Ansible prior to 2.1.x.  See
# https://docs.ansible.com/ansible/become.html#becoming-an-unprivileged-user
# for more secure ways to fix this than enabling this option.
#allow_world_readable_tmpfiles = False

# controls the compression level of variables sent to
# worker processes. At the default of 0, no compression
# is used. This value must be an integer from 0 to 9.
#var_compression_level = 9

# controls what compression method is used for new-style ansible modules when
# they are sent to the remote system.  The compression types depend on having
# support compiled into both the controller's python and the client's python.
# The names should match with the python Zipfile compression types:
# * ZIP_STORED (no compression. available everywhere)
# * ZIP_DEFLATED (uses zlib, the default)
# These values may be set per host via the ansible_module_compression inventory
# variable
#module_compression = 'ZIP_DEFLATED'

# This controls the cutoff point (in bytes) on --diff for files
# set to 0 for unlimited (RAM may suffer!).
#max_diff_size = 1048576

[privilege_escalation]
#become=True
#become_method=sudo
#become_user=root
#become_ask_pass=False

[paramiko_connection]

# uncomment this line to cause the paramiko connection plugin to not record new host
# keys encountered.  Increases performance on new host additions.  Setting works independently of the
# host key checking setting above.
#record_host_keys=False

# by default, Ansible requests a pseudo-terminal for commands executed under sudo. Uncomment this
# line to disable this behaviour.
#pty=False

[ssh_connection]

# ssh arguments to use
# Leaving off ControlPersist will result in poor performance, so use
# paramiko on older platforms rather than removing it, -C controls compression use
#ssh_args = -C -o ControlMaster=auto -o ControlPersist=60s

# The path to use for the ControlPath sockets. This defaults to
# "%(directory)s/ansible-ssh-%%h-%%p-%%r", however on some systems with
# very long hostnames or very long path names (caused by long user names or
# deeply nested home directories) this can exceed the character limit on
# file socket names (108 characters for most platforms). In that case, you
# may wish to shorten the string below.
#
# Example:
# control_path = %(directory)s/%%h-%%r
#control_path = %(directory)s/ansible-ssh-%%h-%%p-%%r

# Enabling pipelining reduces the number of SSH operations required to
# execute a module on the remote server. This can result in a significant
# performance improvement when enabled, however when using "sudo:" you must
# first disable 'requiretty' in /etc/sudoers
#
# By default, this option is disabled to preserve compatibility with
# sudoers configurations that have requiretty (the default on many distros).
#
#pipelining = False

# Control the mechanism for transfering files
#   * smart = try sftp and then try scp [default]
#   * True = use scp only
#   * False = use sftp only
#scp_if_ssh = smart

# if False, sftp will not use batch mode to transfer files. This may cause some
# types of file transfer failures impossible to catch however, and should
# only be disabled if your sftp version has problems with batch mode
#sftp_batch_mode = False

[accelerate]
#accelerate_port = 5099
#accelerate_timeout = 30
#accelerate_connect_timeout = 5.0

# The daemon timeout is measured in minutes. This time is measured
# from the last activity to the accelerate daemon.
#accelerate_daemon_timeout = 30

# If set to yes, accelerate_multi_key will allow multiple
# private keys to be uploaded to it, though each user must
# have access to the system via SSH to add a new key. The default
# is "no".
#accelerate_multi_key = yes

[selinux]
# file systems that require special treatment when dealing with security context
# the default behaviour that copies the existing context or uses the user default
# needs to be changed to use the file system dependent context.
#special_context_filesystems=nfs,vboxsf,fuse,ramfs

# Set this to yes to allow libvirt_lxc connections to work without SELinux.
#libvirt_lxc_noseclabel = yes

[colors]
#highlight = white
#verbose = blue
#warn = bright purple
#error = red
#debug = dark gray
#deprecate = purple
#skip = cyan
#unreachable = red
#ok = green
#changed = yellow
#diff_add = green
#diff_remove = red
#diff_lines = cyan
```
