## Creating CA with OpenSSL
OpenSSL自建CA和颁发SSL证书指南
* [概要](#summary)
* [OpenSSL管理CA证书](#openssl-man-ca)
* [OpenSSL建立根CA](#openssl_create-root-ca)
* [OpenSSL建立中间证书](#openssl_create-intermediate-ca)
* [OpenSSL使用根CA或中间CA认证签发](#openssl_sign-cert)
* [OpenSSL文件清单](#openssl_files-list)
* [OpenSSL命令](#openssl_command)
* [证书配置](#openssl_ca-config)
* [加密算法](#openssl_encryption_algorithm)
* [参考文档](#openssl_onlindocs)

<span id="summary"></span>
### Quick Start
工作目录
* `$CA_WORK` - 工作目录 `/data/docker/ca`
* `$CA_WORK/certs` - 证书目录
* `$CA_WORK/imCA` - 中间证书目录
* `$CA_WORK/rootCA` - 根CA文件目录

已搭建好根CA/中间CA环境：CA主机 `supperm.com`, 目录文件 `$CA_WORK/certs`  
* `rootCA.cnf` 根CA配置文件  
* `cacert.crt` 根CA证书
* `imCA.cnf` 中间CA配置文件
* `imca.crt` 中间CA证书链

工作目录 `$CA_WORK/certs`
```bash
## 生成密钥（server.key）与证书请求文件（server.csr）
openssl genrsa -out server.key 2048
openssl req -new -key server.key -out server.csr -subj "/C=CN/ST=jiangsu/O=SupperM/CN=server.supperm.com"

##使用中间证书签发证书 (server.crt)
openssl ca -config imCA.cnf -days 365 -in server.csr -out server.crt
openssl verify -CAfile imca.crt server.crt

##使用根证书签发证书（server.crt）
openssl ca -config rootCA.cnf -in server.csr -out server.crt
openssl verify -CAfile cacert.crt server.crt

##验证测试
openssl x509 -noout -text -in server.crt
openssl verify -CAfile imca.crt server.crt
openssl verify -CAfile cacert.crt server.crt
```

>说明:  
>CN: `server.supperm.com`  
>私钥/证书请求/证书：`server.key`,`server.csr`,`server.crt`  
>
>安装配置根证书或证书链（Ubuntu）
>拷贝证书到 `/usr/share/ca-certificates/extra`  
>安装证书：`sudo dpkg-reconfigure ca-certificates`  

### Sigin certificates for Nginx
为 Nginx Web服务器生成SSL密钥。

###### Prepare certificate
证书准备：生成密钥(`nginx.key`)/证书请求文件(`nginx.csr`)/签发证书(`nginx.crt`)
```bash
openssl genrsa -out nginx.key 2048
openssl req -new -key nginx.key -out nginx.csr -subj "/C=CN/ST=jiangsu/O=SupperM/CN=nginx.supperm.com"
openssl ca -config imCA.cnf -in nginx.csr -out nginx.crt
```

###### Nginx Server
`/etc/nginx/nginx.conf` include `/etc/nginx/sites-enabled/*`  
在nginx配置文件（`/etc/nginx/sites-enabled/default`）的`server`指令下添加：  
```
listen 443 ssl;
server_name nginx.supperm.com;

ssl on;
ssl_certificate /etc/nginx/ssl/nginx.crt;
ssl_certificate_key /etc/nginx/ssl/nginx.key;
```
同时注意 `server_name` 与证书申请时的 `Common Name` 要相同，打开`443`端口。
当然关于web服务器加密还有其他配置内容，如只对部分URL加密，对URL重定向实现强制https访问，请参考其他资料。

copy `nginx.crt`,`nginx.key` 到 `/etc/nginx/ssl/`目录
>修改 `nginx.crt` 只保留 `-----BEGIN CERTIFICATE-----`,`-----END CERTIFICATE-----`之间部分内容。???

指定 CA 证书 `imca.crt` 或 nginx证书 `nginx.crt`测试成功
```bash
curl --cacert imca.crt https://nginx.supperm.com/
curl --cacert nginx.crt https://nginx.supperm.com/
curl https://nginx.supperm.com/  ## *!!ERROR!!*
```

问题测试:
`openssl s_client -connect nginx.supperm.com:443 |tee logfile`  

获取远端server的证书:
`openssl s_client -connect nginx.supperm.com:443 </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > remote.crt`

---
<span id="openssl-man-ca"></span>
###  Manage CA Certificate with OpenSSL
使用 OpenSSL 管理CA证书

###### CA certificate
```bash
## 生成CA证书私钥(ca.key) (password: to2passwd)
openssl genrsa -des3 -out ca.key 2048
## 生成CA根证书(ca.crt) 
openssl req -new -x509 -days 365 -key ca.key -out ca.crt -subj "/C=CN/ST=jiangsu/L=nanjing/O=SupperM/OU=UFO/CN=supperm.com/emailAddress=wangwg2@msn.com"
# 或 交互输入 -subj 内容
openssl req -new -x509 -days 365 -key ca.key -out ca.crt

## 一次生成自签名证书(私钥:supperm.key, 证书:supperm.crt)
openssl req -newkey rsa:2048 -nodes -sha256 -keyout supperm.key -x509 -days 365 -out supperm.crt
# subject 输入：
#   /C=CN/ST=jiangsu/L=nanjing/O=SupperM/OU=UFO/CN=allhub.supperm.com
#   /emailAddress=wangwg2@msn.com
```

###### Server certificate  
```bash
## 服务器私钥与证书请求（私钥:server.key, 证书请求:server.csr）
#  生成私钥(server.key)，可用选项 “-des3” 指定密钥加密方法
openssl genrsa -out server.key 2048
#  生成证书请求(server.csr), 可用选项 “-subj” 
openssl req -new -key server.key -out server.csr

## 签发服务器证书(server.cert)
#  set_serial 指明了证书的序号，如果证书过期或key泄漏，重新发证时，就要加1
openssl x509 -req -days 365 -in server.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out myserver.crt
# 或 
openssl x509 -req -days 365 -in server.csr -CA ca.cert -CAkey ca.key -CAcreateserial -out server.cert
## 或 在CA主机上
openssl ca -in private/server.csr -out certs/server.cert
```

###### Key and CSR
```bash
## ssl.key 与 ssl_insec.key 相同
# 私钥的生成 (ssl.key)(非加密)
openssl genrsa -out ssl.key 2048
# 加密私钥 (ssl_sec.key) (password:to2passwd) 
openssl rsa -in ssl.key -des3 -out ssl_sec.key
# 去除Key密码 (ssl_sec.key --> ssl_insec.key)
openssl rsa -in ssl_sec.key -out ssl_insec.key

## 查看KEY信息  
openssl rsa -noout -text -in ssl.key

## 查看CSR信息  
openssl req -noout -text -in ssl.csr
```


###### Certificate File and Convert
可以从证书文件生成证书请求文件，不同格式的证书可以进行格式转换。

```bash
## 从证书文件生成CSR (domain.crt,domain.key --> domain.csr)  
`openssl x509 -x509toreq -in domain.crt -out domain.csr -signkey domain.key`  
```

一般证书有三种格式：
* PEM(.pem) 前面命令生成的都是这种格式，
* DER(.cer .der) Windows 上常见
* PKCS#12文件(.pfx .p12) Mac上常见
```bash
## PEM转换为DER  
openssl x509 -outform der -in myserver.crt -out myserver.der
## DER转换为PEM  
openssl x509 -inform der -in myserver.cer -out myserver.pem
## PEM转换为PKCS  
openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile ca.crt
## PKCS转换为PEM  
openssl pkcs12 -in myserver.pfx -out myserver2.pem -nodes
```

###### Export Certificate
```bash
## 导出客户端证书 (-name: 导出的证书别名)
openssl pkcs12 -export -clcerts -name myclient -inkey private/client-key.pem -in certs/client-cert.pem -out certs/client.keystore
    
## 导出服务端证书  
openssl pkcs12 -export -clcerts -name myserver -inkey private/server-key.pem -in certs/server-cert.pem -out certs/server.keystore

## 信任证书的导出 (keytool 为Java自带数字证书管理的工具)
keytool -importcert -trustcacerts -alias www.mydomain.com -file certs/ca.cer -keystore certs/ca-trust.keystore
```

###### Verify and Test Certificate
Openssl提供了简单的client和server工具，可以用来模拟SSL连接，做测试使用。
```bash
## 连接到远程服务器
openssl s_client -connect www.google.com.hk:443

## 模拟的HTTPS服务，可以返回Openssl相关信息   
##    -accept     用来指定监听的端口号   
##    -cert -key  用来指定提供服务的key和证书  
openssl s_server -accept 443 -cert myserver.crt -key myserver.key -www

## 可以将key和证书写到同一个文件中  
cat myserver.crt myserver.key > myserver.pem
## 使用的时候只提供一个参数就可以了  
openssl s_server -accept 443 -cert myserver.pem -www

## 可以将服务器的证书保存下来
openssl s_client -connect www.google.com.hk:443 </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > remote.pem

## 转换成DER文件，就可以在Windows下直接查看了  
openssl x509 -outform der -in remote.pem -out remote.cer
```

----
<span id="openssl_create-root-ca"></span>
### Create root CA with OpenSSL
OpenSSL建立根CA步骤：
1. 准备根CA文件目录
2. 准备配置文件
3. 生成根密钥与根证书
4. 验证根证书

###### 1.Prepare the directory
根CA文件目录: `$CA_WORK/rootCA`
创建 `certs`,`private`, `newcerts`,`crl`, `index.txt`,`serial`  
```bash
mkdir -p certs private newcerts crl
chmod 700 private
touch index.txt serial
echo 01 > serial
```

CA目录文件与子目录
* `certs` -- 存放已颁发的证书
* `newcerts` -- 存放CA指令生成的新证书
* `private` -- 存放私钥
* `crl` -- 存放已吊销的证书
* `index.txt` - OpenSSL定义的已签发证书的文本数据库文件，这个文件通常在初始化的时候是空的
* `serial` - 证书签发时使用的序列号参考文件，该文件的序列号是以16进制格式进行存放的，该文件必须提供并且包含一个有效的序列号

###### 2.Prepare the configuration file
拷贝 `/etc/ssl/openssl.cnf` 到 `rootCA.cnf` 进行修改
可在 `openssl` 命令中使用 `-config rootCA.cnf` 选项指定openssl配置文件，不指定使用默认配置文件。

`rootCA.cnf` 配置内容参考
```yaml
[ ca ]
default_ca	= CA_default		# The default ca section

[ CA_default ]
# Directory and file locations.
dir               = ./rootCA
certs             = $dir/certs
crl_dir           = $dir/crl
new_certs_dir     = $dir/newcerts
database          = $dir/index.txt
serial            = $dir/serial
RANDFILE          = $dir/private/.rand

# The root key and root certificate.
private_key       = $dir/private/cakey.pem
certificate       = $dir/certs/cacert.pem

# For certificate revocation lists.
crlnumber         = $dir/crlnumber
crl               = $dir/crl/crl.pem
crl_extensions    = crl_ext
default_crl_days  = 30

# SHA-1 is deprecated, so use SHA-2 instead.
default_md        = sha256

name_opt          = ca_default
cert_opt          = ca_default
default_days      = 375
preserve          = no
policy            = policy_strict

[ policy_strict ]
# The root CA should only sign intermediate certificates that match.
# See the POLICY FORMAT section of `man ca`.
countryName             = match
stateOrProvinceName     = match
organizationName        = match
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ policy_loose ]
# Allow the intermediate CA to sign a more diverse range of certificates.
# See the POLICY FORMAT section of the `ca` man page.
countryName             = optional
stateOrProvinceName     = optional
localityName            = optional
organizationName        = optional
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ req_distinguished_name ]
# See <https://en.wikipedia.org/wiki/Certificate_signing_request>.
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

# Optionally, specify some defaults.
countryName_default             = GB
stateOrProvinceName_default     = England
localityName_default            =
0.organizationName_default      = Alice Ltd
#organizationalUnitName_default =
#emailAddress_default           =

[ v3_ca ]
# Extensions for a typical CA (`man x509v3_config`).
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ v3_intermediate_ca ]
# Extensions for a typical intermediate CA (`man x509v3_config`).
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:0
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ usr_cert ]
# Extensions for client certificates (`man x509v3_config`).
basicConstraints = CA:FALSE
nsCertType = client, email
nsComment = "OpenSSL Generated Client Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer
keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment
extendedKeyUsage = clientAuth, emailProtection

[ server_cert ]
# Extensions for server certificates (`man x509v3_config`).
basicConstraints = CA:FALSE
nsCertType = server
nsComment = "OpenSSL Generated Server Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer:always
keyUsage = critical, digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth

[ crl_ext ]
# Extension for CRLs (`man x509v3_config`).
authorityKeyIdentifier=keyid:always

[ ocsp ]
# Extension for OCSP signing certificates (`man ocsp`).
basicConstraints = CA:FALSE
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer
keyUsage = critical, digitalSignature
extendedKeyUsage = critical, OCSPSigning
```

###### 3.Create the root key and root certificate
生成根密钥与根证书：在CA目录下，生成密钥（`private/cakey.pem`）与证书文件（`cacert.pem`）[*to2passwd*]

```bash
## 生成根证书私钥 private/cakey.pem (key文件)  
openssl genrsa -aes256 -out private/cakey.pem 4096

## 自签发根证书 certs/cacert.pem (cer文件)  
openssl req -config rootCA.cnf -key private/cakey.pem \
    -new -x509 -days 7300 -sha256 -extensions v3_ca \
    -out certs/cacert.pem \
    -subj "/C=CN/ST=jiangsu/L=nanjing/O=SupperM/OU=UFO/CN=supperm.com/emailAddress=wangwg2@msn.com"
```

###### 4.Verify the root certificate
验证根证书： `openssl x509 -noout -text -in certs/cacert.pem`


<span id="openssl_create-intermediate-ca"></span>
### Create the intermediate CA with OpenSSL
中间CA是根CA的代理，其证书由根CA签发，同时中间CA能够代表根CA签发用户证书，由此建立起信任链。
创建中间CA的好处是即使中间CA的私钥泄露，造成的影响也是可控的，我们只需要使用根CA撤销对应中间CA的证书即可。此外根CA的私钥可以脱机妥善保存，只需要在撤销和更新中间CA证书时才会使用。

###### 1.Prepare the directory
创建中间证书目录 `imCA`, 并创建子目录
```bash
mkdir certs crl newcerts private
touch index.txt serial
echo 1000 > serial
```

###### 2.Prepare the configuration file
中间证书配置文件 `imCA.cnf` 同 `rootCA.cnf`, 修改部分内容。
每次使用中间CA创建新的证书时，以 `-config imCA.cnf` 的形式告诉OpenSSL中间CA的信息. 
```yaml
...
[ CA_default ]
dir             = ./imCA
certs           = $dir/certs
private         = $dir/private
certificate     = $certs/imcacert.pem
private_key     = $private/imcakey.pem
crl             = $dir/crl/im.crl.pem
policy          = policy_loose
```

###### 3.Create the intermediate key and certificate
```bash
## 1.生成密钥 (private/imcakey.pem)
openssl genrsa -aes256 -out private/imcakey.pem 4096

## 2.生成证书请求 （certs/imca.csr）
openssl req -config imCA.cnf -new -sha256 -key private/imcakey.pem -out certs/imca.csr \
  -subj "/C=CN/ST=jiangsu/L=nanjing/O=SupperM/OU=CA/CN=supperm.com/emailAddress=wangwg2@msn.com"

## 3.签发中间CA证书（certs/imcacert.pem） [`rootCA`目录]
##   copy `$imCA/certs/imca.csr` to `$rootCA/certs/imca.csr`
openssl ca -config rootCA.cnf -extensions v3_intermediate_ca -days 3650 \
  -notext -md sha256 -in certs/imca.csr -out certs/imcacert.pem

## 4.验证中间证书 [`rootCA`目录]  
openssl x509 -noout -text -in certs/imcacert.pem

## 5.构造证书链（certs/imca.crt）  [`rootCA`目录]  
cat certs/imcacert.pem certs/cacert.pem > certs/imca.crt

## 6.copy `$rootCA/certs`下的 `imcacert.pem`、`imca.crt` to `$imCA/certs/`
```


----
<span id="openssl_sign-cert"></span>
### Sign server and client certificates with OpenSSL
###### Sign Certificates Using intermediate CA
使用中间CA认证签发, 准备目录文件 `$CA_WORK/certs`
`imCA.cnf` 中间CA配置文件
`imca.crt` 中间CA证书链

工作目录 `$CA_WORK/certs`  
```bash
## 1.生成密钥 （server.key）
openssl genrsa -out server.key 2048

## 2.生成证书请求文件（server.csr）
openssl req -key server.key -new -sha256 -out server.csr -subj "/C=CN/ST=jiangsu/O=SupperM/CN=server.supperm.com"

## 3.使用中间证书签发证书（server.crt）
openssl ca -config imCA.cnf -in server.csr -out server.crt

## 4.验证证书
openssl x509 -noout -text -in server.crt
```

其他命令
```bash
#验证测试
openssl x509 -noout -text -in server.crt
openssl verify -CAfile imca.crt server.crt

## 命令变化
openssl req -config imCA.cnf -key server.key -new -sha256 -out server.csr
openssl ca -config imCA.cnf -extensions server_cert -days 375 -notext -md sha256 -in server.csr -out server.crt
```

###### Sign Certificates Using root CA
用根CA认证签发
```bash
## 1.生成密钥（serverr.key）
openssl genrsa -out serverr.key 2048

## 2.生成证书请求文件（serverr.csr）
openssl req -new -key serverr.key -out serverr.csr -subj "/C=CN/ST=jiangsu/O=SupperM/CN=serverr.supperm.com"

## 3.使用根证书签发证书（serverr.crt）
openssl ca -config rootCA.cnf -in serverr.csr -out serverr.crt

## 4.验证证书  
openssl verify -CAfile cacert.crt serverr.crt
```

>说明  
> CN: `server.supperm.com`  
> `cacert.crt` 根证书
> `serverr.supperm.com`的密钥与证书：`serverr.key`、`serverr.crt`
>
>或使用如下命令签发  
>`openssl x509 -req -days 365 -in server.csr -CA certs/cacert.pem -CAkey private/cakey.pem -CAcreateserial -out serverr.crt`



---
<span id="openssl_files-list"></span>
### OpenSSL Files List
###### linux system ca path
系统证书目录

Ubuntu证书目录 `/etc/ssl`   
```
/etc/ssl/openssl.cnf    OpenSSL的CA主配置文件
/etc/ssl/certs          该服务器上的证书存放目录    
/etc/ssl/private        用于存放CA的私钥    
```

Centos证书目录 `/etc/pki`  
```
/etc/pki/CA/
   newcerts         存放CA签署（颁发）过的数字证书（证书备份目录）
   private          用于存放CA的私钥
   crl              吊销的证书
/etc/pki/tls/
   cert.pem         软链接到certs/ca-bundle.crt
   certs/           该服务器上的证书存放目录，可以房子自己的证书和内置证书
     ca-bundle.crt  内置信任的证书
   private          证书密钥存放目录
   openssl.cnf      OpenSSL的CA主配置文件
```

`@import "conf/openssl.cnf" {as=yaml}`
`@import "rootCA/rootCA.cnf" {as=yaml}`

###### openssl.cnf (/etc/ssl/openssl.cnf)
```yaml
#
# OpenSSL example configuration file.
# This is mostly being used for generation of certificate requests.
#

# This definition stops the following lines choking if HOME isn't
# defined.
HOME                    = .
RANDFILE                = $ENV::HOME/.rnd

# Extra OBJECT IDENTIFIER info:
#oid_file               = $ENV::HOME/.oid
oid_section             = new_oids

# To use this configuration file with the "-extfile" option of the
# "openssl x509" utility, name here the section containing the
# X.509v3 extensions to use:
# extensions            =
# (Alternatively, use a configuration file that has only
# X.509v3 extensions in its main [= default] section.)

[ new_oids ]

# We can add new OIDs in here for use by 'ca', 'req' and 'ts'.
# Add a simple OID like this:
# testoid1=1.2.3.4
# Or use config file substitution like this:
# testoid2=${testoid1}.5.6

# Policies used by the TSA examples.
tsa_policy1 = 1.2.3.4.1
tsa_policy2 = 1.2.3.4.5.6
tsa_policy3 = 1.2.3.4.5.7

####################################################################
[ ca ]
default_ca      = CA_default            # The default ca section

####################################################################
[ CA_default ]

dir             = ./demoCA              # Where everything is kept
certs           = $dir/certs            # Where the issued certs are kept
crl_dir         = $dir/crl              # Where the issued crl are kept
database        = $dir/index.txt        # database index file.
#unique_subject = no                    # Set to 'no' to allow creation of
# several ctificates with same subject.
new_certs_dir   = $dir/newcerts         # default place for new certs.

certificate     = $dir/cacert.pem       # The CA certificate
serial          = $dir/serial           # The current serial number
crlnumber       = $dir/crlnumber        # the current crl number
# must be commented out to leave a V1 CRL
crl             = $dir/crl.pem          # The current CRL
private_key     = $dir/private/cakey.pem# The private key
RANDFILE        = $dir/private/.rand    # private random number file

x509_extensions = usr_cert              # The extentions to add to the cert

# Comment out the following two lines for the "traditional"
# (and highly broken) format.
name_opt        = ca_default            # Subject Name options
cert_opt        = ca_default            # Certificate field options

# Extension copying option: use with caution.
# copy_extensions = copy

# Extensions to add to a CRL. Note: Netscape communicator chokes on V2 CRLs
# so this is commented out by default to leave a V1 CRL.
# crlnumber must also be commented out to leave a V1 CRL.
# crl_extensions        = crl_ext

default_days    = 365                   # how long to certify for
default_crl_days= 30                    # how long before next CRL
default_md      = default               # use public key default MD
preserve        = no                    # keep passed DN ordering

# A few difference way of specifying how similar the request should look
# For type CA, the listed attributes must be the same, and the optional
# and supplied fields are just that :-)
policy          = policy_match

# For the CA policy
[ policy_match ]
countryName             = match
stateOrProvinceName     = match
organizationName        = match
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

# For the 'anything' policy
# At this point in time, you must list all acceptable 'object'
# types.
[ policy_anything ]
countryName             = optional
stateOrProvinceName     = optional
localityName            = optional
organizationName        = optional
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

####################################################################
[ req ]
default_bits            = 2048
default_keyfile         = privkey.pem
distinguished_name      = req_distinguished_name
attributes              = req_attributes
x509_extensions = v3_ca # The extentions to add to the self signed cert

# Passwords for private keys if not present they will be prompted for
# input_password = secret
# output_password = secret

# This sets a mask for permitted string types. There are several options.
# default: PrintableString, T61String, BMPString.
# pkix   : PrintableString, BMPString (PKIX recommendation before 2004)
# utf8only: only UTF8Strings (PKIX recommendation after 2004).
# nombstr : PrintableString, T61String (no BMPStrings or UTF8Strings).
# MASK:XXXX a literal mask value.
# WARNING: ancient versions of Netscape crash on BMPStrings or UTF8Strings.
string_mask = utf8only

# req_extensions = v3_req # The extensions to add to a certificate request

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
countryName_default             = AU
countryName_min                 = 2
countryName_max                 = 2

stateOrProvinceName             = State or Province Name (full name)
stateOrProvinceName_default     = Some-State

localityName                    = Locality Name (eg, city)

0.organizationName              = Organization Name (eg, company)
0.organizationName_default      = Internet Widgits Pty Ltd

# we can do this but it is not needed normally :-)
#1.organizationName             = Second Organization Name (eg, company)
#1.organizationName_default     = World Wide Web Pty Ltd

organizationalUnitName          = Organizational Unit Name (eg, section)
#organizationalUnitName_default =

commonName                      = Common Name (e.g. server FQDN or YOUR name)
commonName_max                  = 64

emailAddress                    = Email Address
emailAddress_max                = 64

# SET-ex3                       = SET extension number 3

[ req_attributes ]
challengePassword               = A challenge password
challengePassword_min           = 4
challengePassword_max           = 20

unstructuredName                = An optional company name

[ usr_cert ]

# These extensions are added when 'ca' signs a request.

# This goes against PKIX guidelines but some CAs do it and some software
# requires this to avoid interpreting an end user certificate as a CA.

basicConstraints=CA:FALSE

# Here are some examples of the usage of nsCertType. If it is omitted
# the certificate can be used for anything *except* object signing.

# This is OK for an SSL server.
# nsCertType                    = server

# For an object signing certificate this would be used.
# nsCertType = objsign

# For normal client use this is typical
# nsCertType = client, email

# and for everything including object signing:
# nsCertType = client, email, objsign

# This is typical in keyUsage for a client certificate.
# keyUsage = nonRepudiation, digitalSignature, keyEncipherment

# This will be displayed in Netscape's comment listbox.
nsComment                       = "OpenSSL Generated Certificate"

# PKIX recommendations harmless if included in all certificates.
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer

# This stuff is for subjectAltName and issuerAltname.
# Import the email address.
# subjectAltName=email:copy
# An alternative to produce certificates that aren't
# deprecated according to PKIX.
# subjectAltName=email:move

# Copy subject details
# issuerAltName=issuer:copy

#nsCaRevocationUrl              = http://www.domain.dom/ca-crl.pem
#nsBaseUrl
#nsRevocationUrl
#nsRenewalUrl
#nsCaPolicyUrl
#nsSslServerName

# This is required for TSA certificates.
# extendedKeyUsage = critical,timeStamping

[ v3_req ]

# Extensions to add to a certificate request

basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment

[ v3_ca ]


# Extensions for a typical CA


# PKIX recommendation.

subjectKeyIdentifier=hash

authorityKeyIdentifier=keyid:always,issuer

# This is what PKIX recommends but some broken software chokes on critical
# extensions.
#basicConstraints = critical,CA:true
# So we do this instead.
basicConstraints = CA:true

# Key usage: this is typical for a CA certificate. However since it will
# prevent it being used as an test self-signed certificate it is best
# left out by default.
# keyUsage = cRLSign, keyCertSign

# Some might want this also
# nsCertType = sslCA, emailCA

# Include email address in subject alt name: another PKIX recommendation
# subjectAltName=email:copy
# Copy issuer details
# issuerAltName=issuer:copy

# DER hex encoding of an extension: beware experts only!
# obj=DER:02:03
# Where 'obj' is a standard or added object
# You can even override a supported extension:
# basicConstraints= critical, DER:30:03:01:01:FF

[ crl_ext ]

# CRL extensions.
# Only issuerAltName and authorityKeyIdentifier make any sense in a CRL.

# issuerAltName=issuer:copy
authorityKeyIdentifier=keyid:always

[ proxy_cert_ext ]
# These extensions should be added when creating a proxy certificate

# This goes against PKIX guidelines but some CAs do it and some software
# requires this to avoid interpreting an end user certificate as a CA.

basicConstraints=CA:FALSE

# Here are some examples of the usage of nsCertType. If it is omitted
# the certificate can be used for anything *except* object signing.

# This is OK for an SSL server.
# nsCertType                    = server

# For an object signing certificate this would be used.
# nsCertType = objsign

# For normal client use this is typical
# nsCertType = client, email

# and for everything including object signing:
# nsCertType = client, email, objsign

# This is typical in keyUsage for a client certificate.
# keyUsage = nonRepudiation, digitalSignature, keyEncipherment

# This will be displayed in Netscape's comment listbox.
nsComment                       = "OpenSSL Generated Certificate"

# PKIX recommendations harmless if included in all certificates.
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer

# This stuff is for subjectAltName and issuerAltname.
# Import the email address.
# subjectAltName=email:copy
# An alternative to produce certificates that aren't
# deprecated according to PKIX.
# subjectAltName=email:move

# Copy subject details
# issuerAltName=issuer:copy

#nsCaRevocationUrl              = http://www.domain.dom/ca-crl.pem
#nsBaseUrl
#nsRevocationUrl
#nsRenewalUrl
#nsCaPolicyUrl
#nsSslServerName

# This really needs to be in place for it to be a proxy certificate.
proxyCertInfo=critical,language:id-ppl-anyLanguage,pathlen:3,policy:foo

####################################################################
[ tsa ]

default_tsa = tsa_config1       # the default TSA section

[ tsa_config1 ]

# These are used by the TSA reply generation only.
dir             = ./demoCA              # TSA root directory
serial          = $dir/tsaserial        # The current serial number (mandatory)
crypto_device   = builtin               # OpenSSL engine to use for signing
signer_cert     = $dir/tsacert.pem      # The TSA signing certificate
# (optional)
certs           = $dir/cacert.pem       # Certificate chain to include in reply
# (optional)
signer_key      = $dir/private/tsakey.pem # The TSA private key (optional)

default_policy  = tsa_policy1           # Policy if request did not specify it
# (optional)
other_policies  = tsa_policy2, tsa_policy3      # acceptable policies (optional)
digests         = md5, sha1             # Acceptable message digests (mandatory)
accuracy        = secs:1, millisecs:500, microsecs:100  # (optional)
clock_precision_digits  = 0     # number of digits after dot. (optional)
ordering                = yes   # Is ordering defined for timestamps?
# (optional, default: no)
tsa_name                = yes   # Must the TSA name be included in the reply?
# (optional, default: no)
ess_cert_id_chain       = no    # Must the ESS cert id chain be included?
# (optional, default: no)
```

###### rootCA.cnf (rootCA/rootCA.cnf)
与 “`/etc/ssl/openssl.cnf`”基本相同，修改摘要
```yaml
[ CA_default ]
dir             = ./rootCA
certificate     = $dir/certs/cacert.pem
default_md      = sha256

[ server_cert ]
basicConstraints = CA:FALSE
nsCertType = server
nsComment = "OpenSSL Generated Server Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer:always
keyUsage = critical, digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth

[ v3_ca ]
#basicConstraints = CA:true
basicConstraints = critical,CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ v3_intermediate_ca ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:0
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ tsa_config1 ]
dir             = ./rootCA 
```

文件 `rootCA.cnf` 内容
```yaml
# file: rootCA.cnf (modified from /etc/ssh/openssl.cnf)
HOME                    = .
RANDFILE                = $ENV::HOME/.rnd
oid_section             = new_oids

[ new_oids ]
tsa_policy1 = 1.2.3.4.1
tsa_policy2 = 1.2.3.4.5.6
tsa_policy3 = 1.2.3.4.5.7

[ ca ]
default_ca      = CA_default            # The default ca section

[ CA_default ]
dir             = ./rootCA              # Where everything is kept
certs           = $dir/certs            # Where the issued certs are kept
crl_dir         = $dir/crl              # Where the issued crl are kept
database        = $dir/index.txt        # database index file.
new_certs_dir   = $dir/newcerts         # default place for new certs.
certificate     = $dir/certs/cacert.pem # The CA certificate
serial          = $dir/serial           # The current serial number
crlnumber       = $dir/crlnumber        # the current crl number
crl             = $dir/crl.pem          # The current CRL
private_key     = $dir/private/cakey.pem	 # The private key
RANDFILE        = $dir/private/.rand    # private random number file
x509_extensions = usr_cert              # The extentions to add to the cert
name_opt        = ca_default            # Subject Name options
cert_opt        = ca_default            # Certificate field options
default_days    = 365                   # how long to certify for
default_crl_days= 30                    # how long before next CRL
default_md      = sha256                # use public key default MD
preserve        = no                    # keep passed DN ordering
policy          = policy_match

[ policy_match ]
countryName             = match
stateOrProvinceName     = match
organizationName        = match
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ policy_anything ]
countryName             = optional
stateOrProvinceName     = optional
localityName            = optional
organizationName        = optional
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ req ]
default_bits            = 2048
default_keyfile         = privkey.pem
distinguished_name      = req_distinguished_name
attributes              = req_attributes
x509_extensions = v3_ca # The extentions to add to the self signed cert
string_mask = utf8only

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
countryName_default             = AU
countryName_min                 = 2
countryName_max                 = 2
stateOrProvinceName             = State or Province Name (full name)
stateOrProvinceName_default     = Some-State
localityName                    = Locality Name (eg, city)
0.organizationName              = Organization Name (eg, company)
0.organizationName_default      = Internet Widgits Pty Ltd
organizationalUnitName          = Organizational Unit Name (eg, section)
commonName                      = Common Name (e.g. server FQDN or YOUR name)
commonName_max                  = 64
emailAddress                    = Email Address
emailAddress_max                = 64

[ req_attributes ]
challengePassword               = A challenge password
challengePassword_min           = 4
challengePassword_max           = 20
unstructuredName                = An optional company name

[ usr_cert ]
basicConstraints=CA:FALSE

# This will be displayed in Netscape's comment listbox.
nsComment                       = "OpenSSL Generated Certificate"

# PKIX recommendations harmless if included in all certificates.
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer

[ server_cert ]
# Extensions for server certificates (`man x509v3_config`).
basicConstraints = CA:FALSE
nsCertType = server
nsComment = "OpenSSL Generated Server Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer:always
keyUsage = critical, digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth

[ v3_req ]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment

[ v3_ca ]
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid:always,issuer
#basicConstraints = CA:true
basicConstraints = critical,CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ v3_intermediate_ca ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:0
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ crl_ext ]
authorityKeyIdentifier=keyid:always

[ proxy_cert_ext ]
basicConstraints=CA:FALSE

# This will be displayed in Netscape's comment listbox.
nsComment                       = "OpenSSL Generated Certificate"

# PKIX recommendations harmless if included in all certificates.
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer

# This really needs to be in place for it to be a proxy certificate.
proxyCertInfo=critical,language:id-ppl-anyLanguage,pathlen:3,policy:foo

[ tsa ]
default_tsa = tsa_config1       # the default TSA section

[ tsa_config1 ]
# These are used by the TSA reply generation only.
dir             = ./rootCA              # TSA root directory
serial          = $dir/tsaserial        # The current serial number (mandatory)
crypto_device   = builtin               # OpenSSL engine to use for signing
signer_cert     = $dir/tsacert.pem      # The TSA signing certificate
certs           = $dir/cacert.pem       # Certificate chain to include in reply
signer_key      = $dir/private/tsakey.pem # The TSA private key (optional)
default_policy  = tsa_policy1           # Policy if request did not specify it
other_policies  = tsa_policy2, tsa_policy3      # acceptable policies (optional)
digests         = md5, sha1             # Acceptable message digests (mandatory)
accuracy        = secs:1, millisecs:500, microsecs:100  # (optional)
clock_precision_digits  = 0     # number of digits after dot. (optional)
ordering                = yes   # Is ordering defined for timestamps?
tsa_name                = yes   # Must the TSA name be included in the reply?
ess_cert_id_chain       = no    # Must the ESS cert id chain be included?
```


----
<span id="openssl_command"></span>
### OpenSSH command
###### openssl genrsa
genrsa用于生成RSA私钥，不会生成公钥，因为公钥提取自私钥，如果需要查看公钥或生成公钥，可以使用openssl rsa命令。
```bash
openssl genrsa [-out filename] [-passout arg] [-des] [-des3] [-idea] [numbits]
# 选项说明：
# -out filename   ：将生成的私钥保存至filename文件，若未指定输出文件，则为标准输出。
# -numbits        ：指定要生成的私钥的长度，默认为1024。该项必须为命令行的最后一项参数。
# -des|-des3|-idea：指定加密私钥文件用的算法，这样每次使用私钥文件都将输入密码，太麻烦所以很少使用。
# -passout args   ：加密私钥文件时，传递密码的格式，如果要加密私钥文件时单未指定该项，则提示输入密码。传递密码的args的格式见openssl密码格式。
```

###### openssl req
伪命令req大致有3个功能：生成证书请求文件、验证证书请求文件和创建根CA。
命令示例：
```bash
## 根据私钥pri_key.pem生成一个新的证书请求文件。
#   其中"-new"表示新生成一个新的证书请求文件，"-key"指定私钥文件，
#       "-out"指定输出文件，此处输出文件即为证书请求文件。
openssl req -new -key pri_key.pem -out req1.csr

## 查看证书请求文件内容。其中"-in"选项指定的是证书请求文件。
openssl req -in req1.csr
# "-text"选项表示以文本格式输出证书请求文件的内容。
openssl req -in req1.csr -text
# 将"-text"和"-noout"结合使用，则只输出证书请求的文件头部分。
openssl req -in req1.csr -noout -text
# 只输出subject部分的内容。
openssl req -in req2.csr -subject -noout
# 使用"-pubkey"输出证书请求文件中的公钥内容。
openssl req -in req1.csr -pubkey -noout

## 指定证书请求文件中的签名算法。
#   证书请求文件的头部分有一项是"Signature Algorithm"，它表示使用的是哪种数字签名算法。
#   默认使用的是sha1，还支持md5、sha512等
openssl req -new -key pri_key.pem -out req2.csr -md5

## 验证请求文件的数字签名,这样可以验证出证书请求文件是否被篡改过。
openssl req -verify -in req2.csr

## 自签署证书，可用于自建根CA时。
#   使用openssl req自签署证书时，需要使用"-x509"选项
#   由于是签署证书请求文件，所以可以指定"-days"指定所颁发的证书有效期。
openssl req -x509 -key pri_key.pem -in req1.csr -out CA1.crt -days 365
#   "-x509"选项和"-new"或"-newkey"配合使用时，可以不指定证书请求文件
openssl req -new -x509 -key pri_key.pem -out CA1.crt -days 365

## 让openssl req自动创建所需的私钥文件。
openssl req -new -out req3.csr
# 使用"-nodes"选项禁止加密私钥文件。
openssl req -new -out req3.csr -nodes
# 指定自动创建私钥时，私钥文件的保存位置和文件名。使用"-keyout"选项。
openssl req -new -out req3.csr -nodes -keyout myprivkey.pem

## 使用"-newkey"选项。
#   "-newkey"选项和"-new"选项类似，只不过"-newkey"选项可以直接指定私钥的算法和长度
openssl req -newkey rsa:2048 -out req3.csr -nodes -keyout myprivkey.pem
```

openssl req 命令说明
```bash
openssl req [-new] [-newkey rsa:bits] [-verify] [-x509] [-in filename] [-out filename] [-key filename] [-passin arg] [-passout arg] 
[-keyout filename] [-pubkey] [-nodes] [-[dgst]] [-config filename] [-subj arg] [-days n] [-set_serial n] [-extensions section]
[-reqexts section] [-utf8] [-nameopt] [-reqopt] [-subject] [-subj arg] [-text] [-noout] [-batch] [-verbose]
 
# 选项说明：
# -new        ：创建一个证书请求文件，会交互式提醒输入一些信息，这些交互选项以及交互选项信息的长度值以及其他一些扩展属性在配置文件(默认为
#             ：openssl.cnf，还有些辅助配置文件)中指定了默认值。如果没有指定"-key"选项，则会自动生成一个RSA私钥，该私钥的生成位置
#             ：也在openssl.cnf中指定了。如果指定了-x509选项，则表示创建的是自签署证书文件，而非证书请求文件
# -newkey args：类似于"-new"选项，创建一个新的证书请求，并创建私钥。args的格式是"rsa:bits"(其他加密算法请查看man)，其中bits
#             ：是rsa密钥的长度，如果bits省略了(即-newkey rsa)，则长度根据配置文件中default_bits指令的值作为默认长度，默认该值为2048
#             ：如果指定了-x509选项，则表示创建的是自签署证书文件，而非证书请求文件
# -nodes      ：默认情况下，openssl req自动创建私钥时都要求加密并提示输入加密密码，指定该选项后则禁止对私钥文件加密
# -key filename    ：指定私钥的输入文件，创建证书请求时需要
# -keyout filename ：指定自动创建私钥时私钥的存放位置，若未指定该选项，则使用配置文件中default_keyfile指定的值，默认该值为privkey.pem
# -[dgst]          ：指定对创建请求时提供的申请者信息进行数字签名时的单向加密算法，如-md5/-sha1/-sha512等，
#                  ：若未指定则默认使用配置文件中default_md指定的值
# -verify       ：对证书请求文件进行数字签名验证
# -x509         ：指定该选项时，将生成一个自签署证书，而不是创建证书请求。一般用于测试或者为根CA创建自签名证书
# -days n       ：指定自签名证书的有效期限，默认30天，需要和"-x509"一起使用。
#               ：注意是自签名证书期限，而非请求的证书期限，因为证书的有效期是颁发者指定的，证书请求者指定有效期是没有意义的，
#               ：配置文件中的default_days指定了请求证书的有效期限，默认365天
# -set_serial n ：指定生成自签名证书时的证书序列号，该序列号将写入配置文件中serial指定的文件中，这样就不需要手动更新该序列号文件
#               ：支持数值和16进制值(0x开头)，虽然也支持负数，但不建议
# -in filename  ：指定证书请求文件filename。注意，创建证书请求文件时是不需要指定该选项的
# -out filename ：证书请求或自签署证书的输出文件，也可以是其他内容的输出文件，不指定时默认stdout
# -subj args    ：替换或自定义证书请求时需要输入的信息，并输出修改后的请求信息。args的格式为"/type0=value0/type1=value1..."，
#               ：如果value为空，则表示使用配置文件中指定的默认值，如果value值为"."，则表示该项留空。其中可识别type(man req)有：
#               ：C是Country、ST是state、L是localcity、O是Organization、OU是Organization Unit、CN是common name等
 
# 【输出内容选项：】
# -text         ：以文本格式打印证书请求
# -noout        ：不输出部分信息
# -subject      ：输出证书请求文件中的subject(如果指定了x509，则打印证书中的subject)
# -pubkey       ：输出证书请求文件中的公钥

# 【配置文件项和杂项：】
# -passin arg      ：传递解密密码
# -passout arg     ：指定加密输出文件时的密码
# -config filename ：指定req的配置文件，指定后将忽略所有的其他配置文件。如果不指定则默认使用/etc/pki/tls/openssl.cnf中req段落的值
# -batch           ：非交互模式，直接从配置文件(默认/etc/pki/tls/openssl.cnf)中读取证书请求所需字段信息。但若不指定"-key"时，仍会询问key
# -verbose         ：显示操作执行的详细信息
```


---
<span id="openssl_ca-config"></span>
### Config Certificate
###### Config Certificate (Ubuntu) 
Ubuntu上CA证书的配置可以通过工具`ca-certificates`来方便的进行。  
安装CA证书只需要将其放在`/usr/share/ca-certificates` 目录或其子目录下， `ca-certificates`工具就能自动扫描到。

为了不与其它根证书混淆，我们创建一个子目录名为”extra”:
`sudo mkdir /usr/share/ca-certificates/extra`

拷贝证书到 `/usr/share/ca-certificates/extra`  
*注意证书后缀名为 `.crt`*   

应用服务器证书  
`sudo cp certs/nginx.crt /usr/share/ca-certificates/extra/nginx.crt`  
拷贝根证书 (如使用根证书，就不需要加入每个应用服务器证书)  
`sudo cp certs/cacert.pem /usr/share/ca-certificates/extra/supperm.crt`

安装配置证书  
`sudo dpkg-reconfigure ca-certificates`  
Ubuntu把所有的证书都放在 `/etc/ssl/certs` 目录下，包括CA证书和普通的证书。

测试成功： `curl https://nginx.supperm.com/`

###### Config Certificate (Centos) 
证书位置(应用): 
`/etc/docker/certs.d/mydockerhub.com:5000/ca.crt`

证书位置(OS级): 
`/etc/pki/ca-trust/source/anchors/mydockerhub.com.crt`

update：`update-ca-trust`


---
<span id="openssl_encryption_algorithm"></span>
### Encryption Algorithm
###### 对称加密算法
* DES（Data Encryption Standard）
  数据加密标准，速度较快，适用于加密大量数据的场合。
* 3DES（Triple DES）
  是基于DES，对一块数据用三个不同的密钥进行三次加密，强度更高。
  密钥长度：112/168；
* AES（Advanced Encryption Standard）
  高级加密标准，是下一代的加密算法标准，速度快，安全级别高；
  密钥长度：128/192/256；
###### 非对称算法
* RSA
  由 RSA 公司发明，是一个支持变长密钥的公共密钥算法，需要加密的文件块的长度也是可变的；
* ECC（Elliptic Curves Cryptography）
  椭圆曲线密码编码学。
* DSA（Digital Signature Algorithm）
  数字签名算法，是一种标准的 DSS（数字签名标准）；

ECC和RSA相比，在许多方面都有对绝对的优势，必将取代RSA，成为通用的公钥加密算法。

###### 线性散列算法（签名算法）
* MD5（Message Digest Algorithm 5）
  是RSA数据安全公司开发的一种单向散列算法。
* SHA（Secure Hash Algorithm）
  可以对任意长度的数据运算生成一个160位的数值；

---
<span id="openssl_onlindocs"></span>
### Online Resource
* [OpenSSL Certificate Authority](https://jamielinux.com/docs/openssl-certificate-authority/create-the-root-pair.html) [★★★★★]
* [基于OpenSSL自建CA和颁发SSL证书](https://segmentfault.com/a/1190000002569859)
* [OpenSSL生成根证书CA及签发子证书](https://yq.aliyun.com/articles/40398)
* [OpenSSL生成根证书CA及签发子证书](https://my.oschina.net/itblog/blog/651434)
* [基于 OpenSSL 的 CA 建立及证书签发](http://rhythm-zju.blog.163.com/blog/static/310042008015115718637/)
* [自签名证书 – CA根证书](https://harde.org/blog/2013/11/self-signed-certificate-ca-root.html)
* [CA如何自签证书及颁发证书](http://blog.csdn.net/zhuying_linux/article/details/6683034)
* [CA自签证书的颁发及应用](http://www.dashuju001.net/blog/38338205132705446207.html)
* [生成CA自签证书](http://www.07net01.com/linux/shengchengCAziqianzhengshu_21840_1351176376.html)
* [添加自签发的 SSL 证书为受信任的根证书](http://cnzhx.net/blog/self-signed-certificate-as-trusted-root-ca-in-windows/)
* [利用CA私钥和证书创建中间CA](http://www.cnblogs.com/Security-Darren/p/4079605.html)
* [openssl系列篇](http://www.cnblogs.com/f-ck-need-u/p/7048359.html)
