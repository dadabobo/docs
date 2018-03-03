## TLS/SSL
* [图解SSL/TLS协议](http://www.ruanyifeng.com/blog/2014/09/illustration-ssl.html)

* [Announcing Keyless SSL™: All the Benefits of CloudFlare Without Having to Turn Over Your Private SSL Keys](https://blog.cloudflare.com/announcing-keyless-ssl-all-the-benefits-of-cloudflare-without-having-to-turn-over-your-private-ssl-keys/)
* [Keyless SSL: The Nitty Gritty Technical Details](https://blog.cloudflare.com/keyless-ssl-the-nitty-gritty-technical-details/)
* [What is a Digital Signature?](http://www.youdzone.com/signature.html)

###### SSL/TLS协议运行机制的概述

```plantuml
@startuml
skinparam sequence {
  ArrowColor Black
  ActorBackgroundColor WhiteSmoke
  ActorBorderColor Gray
  GroupBackgroundColor WhiteSmoke
  LifeLineBackgroundColor WhiteSmoke
  LifeLineBorderColor Gray
  ParticipantBackgroundColor WhiteSmoke
  ParticipantBorderColor Gray
}

participant "Clent" as A  
participant "Server" as B 
A -> B: Client Hello (随机数，支持的加密算法)
B -> A: Server Hello (随机数，选择一个 Client 支持的算法)
B -> A: Server 证书
A -> A: 验证证书
A -> B: Client Key Exchange (用 Server公钥，加密 Pre_master Key)
A -> B: Change Cipher Spec (通知 Server 参数协商完成)
A -> B: Finish handshake (Encrypted handshake message)
B -> A: Cipher Spec + Finish handshake

...
A <-> B: Application Data
@enduml
```


###### 单项认证
```plantuml
@startuml
skinparam sequence {
  ArrowColor Black
  ActorBackgroundColor WhiteSmoke
  ActorBorderColor Gray
  GroupBackgroundColor WhiteSmoke
  LifeLineBackgroundColor WhiteSmoke
  LifeLineBorderColor Gray
  ParticipantBackgroundColor WhiteSmoke
  ParticipantBorderColor Gray
}

participant "Clent" as A  
participant "Server" as B 
A -> B: Client Hello (随机数 C，支持的加密算法)
B -> A: Server Hello (随机数 S，选择一个 Client 支持的算法, Server 证书)
A -> A: 客户端验证服务端证书是否合法
A -> B: Client Key Exchange (用 Server公钥，加密 Pre_master Key)
A -> B: Change Cipher Spec (通知 Server 参数协商完成)
A -> B: Finish handshake (Encrypted handshake message)
B -> A: Cipher Spec + Finish handshake

...
A <-> B: Application Data
@enduml
```
