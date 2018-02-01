## Bash on Windows Subsystem for Linux

参考资料
* [Bash on Linux on Windows](https://msdn.microsoft.com/en-us/commandline/wsl/about)
* [Windows 10 Creator’s Update: What’s new in Bash/WSL & Windows Console](http://seattlewebsitedesigns.com/item/1080-windows-10-creators-update-whats-new-in-bashwsl-windows-console.html)

安装 `lxrun /install`  
卸载 `lxrun /uninstall`  
设置 Bash 默认用户： `lxrun /setdefaultuser`

安装后  
* 修改 `/etc/hosts`
* 修改 `/etc/apt/sources.list`

#### install ansible
```bash
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```


安装常用开发包  
`sudo apt-get install build-essential`  
`sudo apt-get install libssl-dev libcurl4-gnutls-dev libexpat1-dev gettext`

**git**
apt 直接安装 git (2.7.4):   
`sudo apt install git`

安装最新版 [github](https://github.com/git/git)  
下载：  
`wget https://github.com/git/git/archive/v2.12.2.tar.gz`  
`wget https://www.kernel.org/pub/software/scm/git/git-2.12.2.tar.gz`  

安装：  
`sudo make prefix=/usr/local install`

配置git
```
git config --global user.name "wangwg2"
git config --global user.email wangwg2@msn.com
git config --global core.editor vi
git config --global color.ui true
```
[git-ssh 配置和使用](https://segmentfault.com/a/1190000002645623)
1. 创建SSH key `ssh-keygen -t rsa -C "username@msn.com"`
2. GitHub设置。登录github --> `Settings` --> `SSH key` --> 拷贝`id_rsa.pub`

**jdk**
apt 安装 openjdk (8u131):  
`sudo apt install openjdk-8-jdk`

下载安装
* 下载jdk
* 解压到 /usr/lib/jvm，建立连接
   `sudo ln -s jdk1.8.0_11 java-8`
* 设置环境变量
  ```
  export JAVA_HOME=/usr/lib/jvm/java-8
  export JRE_HOME=${JAVA_HOME}/jre
  export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
  export PATH=${JAVA_HOME}/bin:$PATH
  ```
* 配置默认JDK版本
  ```
  sudo update-alternatives --install /usr/bin/java java
  sudo update-alternatives --install /usr/bin/javac javac
  sudo update-alternatives --config java
  ```

**maven**
apt 直接安装 maven (3.3.9)：  
`sudo apt install maven`

下载:  
`wget http://apache.fayea.com/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz`  
解压到` /opt/maven`, 设置 `path` 或 创建 `/usr/local/bin/mvn`

**ruby**
apt 直接安装 ruby (2.3.0)：  
`sudo apt install ruby`

`wget https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.1.tar.gz`  
`$ ./configure; make; sudo make install`


**golang**  
apt 直接安装 golang (1.6)：  
`sudo apt install golang`

下载安装
`wget https://storage.googleapis.com/golang/go1.8.1.linux-amd64.tar.gz`  
```
tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz

export PATH=$PATH:/usr/local/go/bin
or
export GOROOT=/opt/go
export PATH=$PATH:$GOROOT/bin
```


**python**
apt 安装 Python (2.7.11)  
`sudo apt install python`

apt 安装 Python3 (3.5.1)     
`sudo apt insatll python3`


### 配置

**Adjusting the Prompt**  
修改 `$HOME/.bashrc`    
`PS1='\e[37;1m\u@\e[35m\W\e[0m\$ '`

`PS1=\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$`  
```
\[\e]0;\u@\h: \w\a\]      
${debian_chroot:+($debian_chroot)}
\[\033[01;32m\]\u@\h	  账号名称@主机名称 01(字体宽度，好像有加亮功能) 32(绿色)
\[\033[00m\]:           :
\[\033[01;34m\]\w       完整工作目录名称 34(蓝色)
\[\033[00m\]\$          $ 默认的字体宽度
wangwg@DESKTOP-HOPAPBJ:~$
```

`PS1=\e[37;1m\u@\e[35m\W\e[0m\$`  
```
\e[37;1m\u@    			    账号名称@  \e[37(灰色) ;1m 字体宽度，好像有加亮功能。       
\e[35m\W				        工作目录   \e[35(紫色)
\e[0m\$					        $   (root 时，提示字符为 # ，否则就是 $)   
wangwg@~$
```

`PS1=' \u@\h:\w\$ '`
```
\u@\h: \w\$ 			账号名称@主机名称: 工作目录名称$
```

`PS1="\[\033[1;32;40m[\033[0;32;40m\u@\h:\033[1;35;40m\w\033[1;32;40m]\033[1;31;40m\$\033[1;32;40m \]"`
* 最外边的`"\[    \]"`是为了把转义序列的字符串括起来，防止转义序列的文本显示在 shell 里占用太多的空间。
* `\033` 声明了转义序列的开始，然后是 `[` 开始定义颜色。
* 后面的 `0` 定义了默认的字体宽度，接着的中间的数字定义字符颜色。
* 最后面的数字定义了字符背景色。字母`m`是定义本身所必须的，字母`m`后面的字符就是你想改变的字符了。

转义字符，下面详细解释：
* `\d` ：代表日期，格式为weekday month date，例如："Mon Aug 1"
* `\H` ：完整的主机名称。例如：我的机器名称为：fc4.linux，则这个名称就是fc4.linux
* `\h` ：仅取主机的第一个名字，如上例，则为fc4，.linux则被省略
* `\t` ：显示时间为24小时格式，如：`HH：MM：SS`
* `\T` ：显示时间为12小时格式
* `\A` ：显示时间为24小时格式：`HH：MM`
* `\u` ：当前用户的账号名称
* `\v` ：BASH的版本信息
* `\w` ：完整的工作目录名称。家目录会以 ~代替
* `\W` ：利用basename取得工作目录名称，所以只会列出最后一个目录
* `\#` ：下达的第几个命令
* `\$` ：提示字符，如果是root时，提示符为：`#` ，普通用户则为：`$`
* `\n` ：新建一行


**DIRCOLORS**  
生成 `dircolors -p > ~/.dircolors`  
修改 `.dircolors` 文件

或 设置 `LS_OPTIONS` 变量

修改 $HOME/.bashrc
```
LS_COLORS='rs=0:di=1;35:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arj=01;31:*.taz=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lz=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.axa=00;36:*.oga=00;36:*.spx=00;36:*.xspf=00;36:';
export LS_COLORS
```

**Vim**  
修改 `.vimrc` 文件  
`set background=dark`
