## kubernetes-vagrant-coreos-cluster
Kubernetes cluster (for testing purposes) made easy with Vagrant and CoreOS.
参考 [pires/kubernetes-vagrant-coreos-cluster](https://github.com/pires/kubernetes-vagrant-coreos-cluster).



---
##### tips

##### 下载慢

###### docker images
```
manifests/master-apiserver-rbac.yaml
manifests/master-apiserver.yaml
manifests/master-controller-manager.yaml
manifests/master-proxy.yaml
manifests/master-scheduler.yaml
master.yaml/node.yaml
```

###### kubectl 
预先下载到当前目录。
https://storage.googleapis.com/kubernetes-release/release/v${KUBERNETES_VERSION}/bin/linux/amd64/kubectl

修改 `setup.tmpl`
```bash
  if [ "$doinstall" == "true" ]; then 
    echo "Downloading and installing ${PLATFORM} version of 'kubectl' v${KUBERNETES_VERSION} into ${targetDir}. This may take a couple minutes, depending on your internet speed.."
    [ -d ${targetDir} ] || sudo mkdir -p ${targetDir}
    ##----- 修改前 ----
    # sudo -E curl -s -k -L -o ${targetDir}/kubectl "${KUB_URL}"
    ##----- 修改后 ----
    sudo -E cp /vagrant/kubectl ${targetDir}/kubectl
    ##------END------
    [ -x ${targetDir}/kubectl ] || sudo chmod +x ${targetDir}/kubectl
  fi
```

----

###### Window 重定向问题
`Vagrantfile` （修改前）
```ruby
  if DNS_PROVIDER == "kube-dns"
    # create dns-deployment.yaml file
    dnsFile = "#{__dir__}/temp/dns-deployment.yaml"
    # ...略....
  else if DNS_PROVIDER == "coredns"
      system "#{__dir__}/plugins/dns/coredns/deploy.sh 10.100.0.10/24 #{DNS_DOMAIN} #{__dir__}/plugins/dns/coredns/coredns.yaml.sed > #{__dir__}/temp/coredns-deployment.yaml"
    end
  end
```

`plugins/dns/coredns/deploy.sh`  （修改前）
```bash
#!/bin/bash

# Deploys CoreDNS to a cluster currently running Kube-DNS.

SERVICE_CIDR=$1
CLUSTER_DOMAIN=${2:-cluster.local}
YAML_TEMPLATE=${3:-`pwd`/coredns.yaml.sed}
YAML=${4:-`pwd`/coredns.yaml}

if [[ -z $SERVICE_CIDR ]]; then
	echo "Usage: $0 SERVICE-CIDR [ CLUSTER-DOMAIN ] [ YAML-TEMPLATE ] [ YAML ]"
	exit 1
fi

CLUSTER_DNS_IP="10.100.0.10"

sed -e s/CLUSTER_DNS_IP/$CLUSTER_DNS_IP/g -e s/CLUSTER_DOMAIN/$CLUSTER_DOMAIN/g -e s?SERVICE_CIDR?$SERVICE_CIDR?g $YAML_TEMPLATE
```


`Vagrantfile` (修改后)
```ruby
  if DNS_PROVIDER == "kube-dns"
    # create dns-deployment.yaml file
    dnsFile = "#{__dir__}/temp/dns-deployment.yaml"
    # ...略....
  else if DNS_PROVIDER == "coredns"
      system "cp #{__dir__}/plugins/dns/coredns/coredns.yaml.sed #{__dir__}/temp/coredns-deployment.yaml"
      system "#{__dir__}/plugins/dns/coredns/deploy.sh 10.100.0.10/24 #{DNS_DOMAIN} #{__dir__}/temp/coredns-deployment.yaml"
    end
  end
```

`plugins/dns/coredns/deploy.sh` (修改后)
```bash
#!/bin/bash

# Deploys CoreDNS to a cluster currently running Kube-DNS.

SERVICE_CIDR=$1
CLUSTER_DOMAIN=${2:-cluster.local}
YAML=${3:-`pwd`/coredns.yaml}

if [[ -z $SERVICE_CIDR ]]; then
	echo "Usage: $0 SERVICE-CIDR [ CLUSTER-DOMAIN ] [ YAML-TEMPLATE ] [ YAML ]"
	exit 1
fi

CLUSTER_DNS_IP="10.100.0.10"

sed -i s/CLUSTER_DNS_IP/$CLUSTER_DNS_IP/g -e s/CLUSTER_DOMAIN/$CLUSTER_DOMAIN/g -e s?SERVICE_CIDR?$SERVICE_CIDR?g $YAML
```




