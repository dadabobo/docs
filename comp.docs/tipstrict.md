## tips


#### IntelliJ IDEA License Server v1.3
jetbrains license server on docker  
[woailuoli993/jblse](https://hub.docker.com/r/woailuoli993/jblse/)

认证

```
docker pull woailuoli993/jblse:0.2.0
docker run -d --name="jblse" -p 20701:20701 woailuoli993/jblse:0.2.0
docker run -d --name="jblse" -p 20701:20701 woailuoli993/jblse:0.2.0 -u coldplay
docker run -it --rm -p 20701:20701 woailuoli993/jblse:latest
```



#### Vagrant

Vagrant 1.9.4 有问题
[vagrant --help displays a rubygems error #8519 ](https://github.com/mitchellh/vagrant/issues/8519)


#### VirutalBox

Current setup: Virtualbox 5.1.20 with Vagrant 1.9.3
Previous working setup: Virtualbox 5.1.18 with Vagrant 1.9.3
[Vagrant was unable to mount VirtualBox shared folders](http://stackoverflow.com/questions/43492322/vagrant-was-unable-to-mount-virtualbox-shared-folders)

[mount.vboxsf symlink broken](https://www.virtualbox.org/ticket/16670)

```
$ ls -lh /sbin/mount.vboxsf 
lrwxrwxrwx 1 root root 49 Apr 19 14:05 /sbin/mount.vboxsf -> /opt/VBoxGuestAdditions-5.1.20/other

sudo ln -sf /opt/VBoxGuestAdditions-5.1.20/lib/VBoxGuestAdditions/mount.vboxsf /sbin/mount.vboxsf
```


#### Puppet安装
安装 puppet
```
echo "Installing puppet"
wget https://apt.puppetlabs.com/puppetlabs-release-pc1-xenial.deb
sudo dpkg -i puppetlabs-release-pc1-xenial.deb

#sudo dpkg -i packages/puppetlabs-release-pc1-xenial.deb
sudo apt update -y
sudo apt install -y puppet-agent
```

报错信息
```
vagrant@work:~$ sudo apt update -y
Ign:1 http://apt.puppetlabs.com xenial InRelease                                                
Hit:2 http://cn.archive.ubuntu.com/ubuntu xenial InRelease                                      
Hit:3 http://cn.archive.ubuntu.com/ubuntu xenial-updates InRelease                                                
Hit:4 http://cn.archive.ubuntu.com/ubuntu xenial-backports InRelease                                              
Hit:5 http://cn.archive.ubuntu.com/ubuntu xenial-security InRelease                                               
Get:6 http://apt.puppetlabs.com xenial Release [13.3 kB]                                                          
Hit:7 http://ppa.launchpad.net/ansible/ansible/ubuntu xenial InRelease                                     
Hit:8 http://repo.saltstack.com/apt/ubuntu/16.04/amd64/latest xenial InRelease                             
Get:9 http://apt.puppetlabs.com xenial Release.gpg [836 B]
Get:10 http://apt.puppetlabs.com xenial/PC1 amd64 Packages [14.0 kB]
Get:11 http://apt.puppetlabs.com xenial/PC1 i386 Packages [13.3 kB]                                               
Err:11 http://apt.puppetlabs.com xenial/PC1 i386 Packages                                                         
  Hash Sum mismatch
Get:12 http://apt.puppetlabs.com xenial/PC1 all Packages [7,797 B]                                                
Err:12 http://apt.puppetlabs.com xenial/PC1 all Packages                                                          
Fetched 49.3 kB in 4min 10s (197 B/s)                                                                             
Reading package lists... Done
E: Failed to fetch http://apt.puppetlabs.com/dists/xenial/PC1/binary-i386/Packages.gz  Hash Sum mismatch
E: Failed to fetch http://apt.puppetlabs.com/dists/xenial/PC1/binary-all/Packages.gz  
E: Some index files failed to download. They have been ignored, or old ones used instead.
```
