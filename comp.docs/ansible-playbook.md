# Ansible

参考资料
* [Ansible](http://www.ansible.com/)
* [Ansible中文权威指南](http://www.ansible.com.cn/)
* [Ansible_Up-And-Running笔记](http://www.jianshu.com/p/f0cf027225df?hmsr=toutiao.io)
* [自动化工具 ansible中文指南](http://www.aikaiyuan.com/6299.html)
* [Ansible 起步指南](https://linux.cn/article-8112-1.html)
* [Ansible来了](http://ju.outofmemory.cn/entry/67581)
* [Ansible简介](http://www.ansible.cn/thread-7-1-1.html)
* [Ansible完整基础简介](http://chuansong.me/n/1428806851433)


内容
* [概要](#summary)
* [Ansible简介](#getstart)
* [Amsible架构](#ansible-architechture)
* [Ansible Playbok](#ansible-playbook)
* [Ansible的roles介绍和实战](#ansible-palybook-roles)
* [Ansible Galaxy](#ansible-galaxy)
* [Ansible roles示例](#role-example)

---
<span id="summary"></span>
## 概要

<span id="ansible-playbook"></span>
## Ansible Playbook

Playbooks 的格式是YAML,语法做到最小化,意在避免 playbooks 成为一种编程语言或是脚本,但它也并不是一个配置模型或过程的模型.
* `playbook` 由一个或多个 ‘`plays`’ 组成.它的内容是一个以 ‘`plays`’ 为元素的列表.
* 在 `play` 之中,一组机器被映射为定义好的角色.在 ansible 中,`play `的内容,被称为 `tasks`,即任务.在基本层次的应用中,一个任务是一个对 ansible 模块的调用,这在前面章节学习过.
* ‘`plays`’ 好似音符,`playbook` 好似由 ‘`plays`’ 构成的曲谱,通过 `playbook`,可以编排步骤进行多机器的部署,比如在 webservers 组的所有机器上运行一定的步骤, 然后在 database server 组运行一些步骤,最后回到 webservers 组,再运行一些步骤,诸如此类.
* “`plays`” 算是一个体育方面的类比,你可以通过多个 `plays` 告诉你的系统做不同的事情,不仅是定义一种特定的状态或模型.你可以在不同时间运行不同的 `plays`.

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

#### Handlers: 在发生改变时执行的操作
