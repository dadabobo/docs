## Docker Install and Config

###### Docker Install
Ubuntu 阿里云镜像安装  
`curl -sSL http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet | sh -`

ansible 安装  
`ansible-galaxy install --roles-path . angstwad.docker_ubuntu`

###### Docker “registry-mirrors”
我的加速器地址： `https://4ue5z1dy.mirror.aliyuncs.com/ubuntu`  

配置加速器
```bash
echo "DOCKER_OPTS=\"--registry-mirror=https://4ue5z1dy.mirror.aliyuncs.com\"" | sudo tee -a /etc/default/docker
sudo service docker restart
```

建议直接通过daemon config进行配置。(`/etc/docker/daemon.json`)  
`{ "registry-mirrors": ["<your accelerate address>"] }`
示例
`{ "registry-mirrors": ["https://4ue5z1dy.mirror.aliyuncs.com"] }`

---
###### Windows 10 Docker
从windows 10 bash 连接到 Window 10 docker。

windows 10 bash 安装 docker 最新版
```
$ wget https://get.docker.com/builds/Linux/x86_64/docker-latest.tgz
$ tar xzvf docker-latest.tgz
$ mv docker/* /usr/local/bin
```

设置 `DOCKER_HOST` (`~/.bashrc`)  
`$ export DOCKER_HOST=tcp://127.0.0.1:2375`


---
###### Configure automated builds on Docker Hub
在Docker Hub上配置自动构建
* 参考 [k8s镜像：安装kubernetes](https://studygolang.com/articles/10521), 解决访问不了gcr.io问题。 
* [Configure automated builds on Docker Hub](https://docs.docker.com/docker-hub/builds/)

需要在 Docker Hub 与 GitHUB上均有账号。
1. GitHub 开启对 Docker Hub 的读授权
2. 将 Dockerfile 上传 GitHub
3. Docker Hub上创建Automated build
4. 取到本地并push到private Registry

登录到Docker Hub并选择 `Profile > Settings > Linked Accounts & Services`
选择 `Link GitHub` 完成 GitHub 对 Docker Hub 的授权。 

在 GitHub 上新建一个工程(`Dockerfiles`)，clone到本地。
然后在工程中新增： `k8s-dns-kube-dns-amd64/1.14.7/Dockerfile`。 
`Dockerfile`内容为：
```dockerfile
FROM gcr.io/google_containers/k8s-dns-kube-dns-amd64:1.14.7
MAINTAINER wangwg2
LABEL name="k8s-dns-kube-dns-amd64" license="GPLv2" build-date="20180129"
```

登录到Docker Hub并选择 `Profile > Create Automated Build`
选择 Github 上 `Dockerfiles` 中的 `k8s-dns-kube-dns-amd64/1.14.7/Dockerfile`.