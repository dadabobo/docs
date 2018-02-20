## CFSSL

###### CFSSL-CloudFlare公司的PKI/TLS工具包 
CFSSL 包括以下部份： 
* 一组用于自定义 PKI/TLS 工具的软件包。 
* Cfssl 程序，用 CFSSL 软件包支持的权威命令行工具。  Multirootca 程序，用于多签名私钥(multiple signing keys)的认证服务器。 
* Mkbundle 程序，用于构建证书链池的工具。 
* Cfssljson 程序，用于接受 cfssl 和 multirootca 程序产生的 json，并将证书，私钥，CSR 及链写入磁盘。 

###### 安装 
安装要求一个 Go 1.6 版以上的安装环境，且已正确设置 GOPATH 变量。 
`$ go get -u github.com/cloudflare/cfssl/cmd/cfssl` 
此条命令，将会下载，打包 CFSSL 工具，并且将它安装到$GOPATH/bin/cfssl.目录。 


