## Migu UML

### 咪咕业务流程
---
#### 用户注册登录流程
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

autonumber
用户 ->> 客户端 : 点击注册
客户端 ->> 一级认证支付SDK : 初始化SDK
一级认证支付SDK -->> 用户 : 弹出注册页面
用户 ->> 一级认证支付SDK : 输入手机号码，获取验证码
一级认证支付SDK -->> 用户 : 发送短信验证码
用户 ->> 一级认证支付SDK : 用户输入验证码，点击注册且登陆
一级认证支付SDK ->> 一级用户中心 : 调用用户注册接口
一级用户中心 ->> 一级用户中心 : 完成注册
一级用户中心 -->> 一级认证支付SDK : 返回用户信息
一级认证支付SDK ->> 一级认证支付SDK : 生成token
一级认证支付SDK -->> 客户端 : token及用户信息
客户端 ->> 服务端 : 发起token检验
服务端 ->> 封装层 : 发起token校验
封装层 ->> 一级用户中心 : 发起token校验
一级用户中心 -->> 封装层 : 返回校验结果
封装层 -->> 服务端 : 返回校验结果
服务端 -->> 客户端 : 返回校验结果
客户端 ->> 客户端 : 显示注册并登陆成功
@enduml
```
---
#### 内容使用流程
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

autonumber

=== 点击内容 ===
用户 ->> 客户端 : 点击内容
客户端 ->> 服务端 : 请求内容详情
服务端 ->> 封装层 : 请求内容详情
封装层 ->> 封装层 : 查询内容详情
封装层 -->> 服务端 : 返回内容详情
服务端 -->> 客户端 : 返回内容详情
客户端 ->> 客户端 : 展示内容详情

=== 点击播放 ===
用户 ->> 客户端 : 点击播放
客户端 ->> 服务端 : 请求播放地址
服务端 ->> 封装层 : 请求播放地址
封装层 ->> 封装层 : 判断是否需要购买

alt 不需要购买
  封装层 -->> 服务端 : 返回播放地址
  服务端 -->> 客户端 : 返回播放地址
  客户端 ->> "下载/流媒体" : 播放内容
else 需要购买
  封装层 -->> 服务端 : 返回批价
  服务端 -->> 客户端 : 返回批价
end

=== 点击购买 ===
用户 ->> 客户端 : 点击购买
客户端 ->> 服务端 : 创建订单
服务端 ->> 封装层 : 创建订单
封装层 -->> 服务端 : 返回订单信息
服务端 -->> 客户端 : 返回订单信息
客户端 ->> 一级认证支付SDK : 拉起SDK支付页
一级认证支付SDK ->> 一级认证支付SDK : 支付页
用户 ->> 一级认证支付SDK : 用户选择支付
一级认证支付SDK ->> 支付中心 : SDK发起支付
支付中心 -->> 一级认证支付SDK : 支付提交成功
支付中心 -->x 封装层 : 异步通知支付成功
封装层 ->> 封装层 : 更新订单状态

loop 30秒
  客户端 ->> 服务端 : 查看订单状态
  服务端 ->> 封装层 : 获取订单状态
  封装层 -->> 服务端 : 返回订单信息

  服务端 ->> 封装层 : 请求播放地址
  封装层 ->> 封装层 : 判断是否需要购买
  alt 不需要购买
    封装层 -->> 服务端 : 返回播放地址
    服务端 -->> 客户端 : 返回播放地址
  else 需要购买
    封装层 -->> 服务端 : 返回批价
    服务端 -->> 客户端 : 返回批价
  end
end

@enduml
```

---
#### 合约类产品订购流程
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
skinparam roundcorner 10

actor 用户
participant 产品侧
participant 封装层
participant 产品中心
participant 订购中心
participant 订单中心
participant 批价中心
participant 支付中心

用户 -> 产品侧 : 点击购买合约类产品
产品侧 -> 封装层 : 合约产品订购
封装层 -> 订购中心 : 查询订购关系
订购中心 -->  封装层: 返回订购关系
封装层 -> 产品中心 : 获取产品价格类型及合约权益
产品中心 --> 封装层 : 返回产品价格类型及合约权益
封装层 -> 封装层 : 判断是否满足订购条件\n（互斥，依赖，是否已经订购）

alt 不满足订购条件
  封装层 --> 产品侧 : 该产品无法订购
else 满足订购条件
  封装层 --> 封装层 : 判断价格是否为0
  alt 0元产品
    封装层 -> 订单中心 : 创建订单
    订单中心 --> 封装层 : 返回订单
    封装层 --> 产品侧 : 购买成功
    产品侧 --> 用户 : 购买成功
  else 非0元产品
    autonumber 200 10 "<font color=blue><b>[000]"
    封装层 -> 批价中心 : <color #red>**获取价格**</color>
    note right: 批价接口
    autonumber stop
    批价中心 --> 封装层 : 返回价格
    封装层 -> 订单中心 : 创建订单
    订单中心 --> 封装层 : 返回订单
    封装层 -> 支付中心 : 支付统一下单
    支付中心 --> 封装层 : 返回支付流水号
    封装层 --> 产品侧 : 订单创建成功
    产品侧 -> 产品侧 : 拉起SDK,选择支付方式
    产品侧 -> 支付中心 : 发起支付
    支付中心 --> 产品侧 : 支付完成
    支付中心 --> 封装层 : 支付结果回调
    封装层 -> 订单中心 : 更新订单信息
    订单中心 -> 订购中心 : 落订购关系
    订购中心 --> 订单中心
    autonumber resume "<font color=blue><b>[000]"
    订单中心 -> 批价中心 : <color #red>**合约产品订购关系同步**</color>
    autonumber stop
    批价中心 --> 订单中心

    订单中心 --> 封装层
  end
  loop 30秒
    产品侧 -> 封装层 : 查看订单状态
  end
end
@enduml
```

---
#### 用户注册登录流程
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
skinparam roundcorner 10

actor 用户
participant 客户端
participant 一级认证支付SDK
participant 服务端
box "能力中心" #Ivory
  participant 封装层
  participant 一级用户中心
end box
autonumber

用户 -> 客户端 : 点击注册
客户端 -> 一级认证支付SDK : 初始化SDK
一级认证支付SDK --> 用户 : 弹出注册页面
用户 -> 一级认证支付SDK : 输入手机号码，获取验证码
一级认证支付SDK --> 用户 : 发送短信验证码
用户 -> 一级认证支付SDK : 用户输入验证码，点击注册且登陆
一级认证支付SDK -> 一级用户中心 : 调用用户注册接口
一级用户中心 -> 一级用户中心 : 完成注册
一级用户中心 --> 一级认证支付SDK : 返回用户信息
一级认证支付SDK -> 一级认证支付SDK : 生成token
一级认证支付SDK --> 客户端 : token及用户信息
客户端 -> 服务端 : 发起token检验
服务端 -> 封装层 : 发起token校验
封装层 -> 一级用户中心 : 发起token校验
一级用户中心 --> 封装层 : 返回校验结果
封装层 --> 服务端 : 返回校验结果
服务端 --> 客户端 : 返回校验结果
客户端 -> 客户端 : 显示注册并登陆成功
@enduml
```

---
#### 内容使用流程
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
skinparam roundcorner 10

actor 用户
participant 客户端
participant 一级认证支付SDK
participant 服务端
box "能力中心" #Lavender
  participant 封装层
  participant 支付中心
  participant 产品内容中心
  participant "下载/流媒体"
end box
autonumber

=== 点击内容 ===
用户 -> 客户端 : 点击内容
客户端 -> 服务端 : 请求内容详情
服务端 -> 封装层 : 请求内容详情(QueryContent)
封装层 -> 产品内容中心 : 查询内容详情(getContentDetail)
产品内容中心 --> 封装层 : 查询内容详情
封装层 --> 服务端 : 返回内容详情
服务端 --> 客户端 : 返回内容详情
客户端 -> 客户端 : 展示内容详情

=== 点击播放 ===
用户 -> 客户端 : 点击播放
客户端 -> 服务端 : 请求播放地址
服务端 -> 封装层 : 请求播放地址(GetPlayDownloadUrl)
封装层 -> 产品内容中心 : 获得产品信息(getProductInfo)
产品内容中心 --> 封装层 : 返回产品信息
封装层 -> 封装层 : 判断内容类型
alt 是免费内容
  封装层 -> 产品内容中心 : 请求播放地址(getPlayDownloadUrl)
  封装层 --> 服务端 : 返回播放地址
  服务端 --> 客户端 : 返回播放地址
  客户端 -> "下载/流媒体" : 播放内容
else 需要购买
  封装层 --> 服务端 : 返回批价
  服务端 --> 客户端 : 返回批价
end

alt 不需要购买
  封装层 --> 服务端 : 返回播放地址
  服务端 --> 客户端 : 返回播放地址
  客户端 -> "下载/流媒体" : 播放内容
else 需要购买
  封装层 --> 服务端 : 返回批价
  服务端 --> 客户端 : 返回批价
end

=== 点击购买 ===
用户 -> 客户端 : 点击购买
客户端 -> 服务端 : 创建订单
服务端 -> 封装层 : 创建订单
封装层 --> 服务端 : 返回订单信息
服务端 --> 客户端 : 返回订单信息
客户端 -> 一级认证支付SDK : 拉起SDK支付页
一级认证支付SDK -> 一级认证支付SDK : 支付页
用户 -> 一级认证支付SDK : 用户选择支付
一级认证支付SDK -> 支付中心 : SDK发起支付
支付中心 --> 一级认证支付SDK : 支付提交成功
支付中心 -->x 封装层 : 异步通知支付成功
封装层 -> 封装层 : 更新订单状态

loop 30秒
  客户端 -> 服务端 : 查看订单状态
  服务端 -> 封装层 : 获取订单状态
  封装层 --> 服务端 : 返回订单信息

  服务端 -> 封装层 : 请求播放地址
  封装层 -> 封装层 : 判断是否需要购买
  alt 不需要购买
    封装层 --> 服务端 : 返回播放地址
    服务端 --> 客户端 : 返回播放地址
  else 需要购买
    封装层 --> 服务端 : 返回批价
    服务端 --> 客户端 : 返回批价
  end
end

@enduml
```

<br>

---
<br>

## 系统模型

---
#### 内容中心模型
```plantuml
@startuml
skinparam class {
  BackgroundColor WhiteSmoke 
  ArrowColor DimGray
  BorderColor Silver
}

skinparam stereotype {
  CBackgroundColor WhiteSmoke
  IBackgroundColor Azure
  ABackgroundColor FloralWhite
  EBackgroundColor GhostWhite
}

"内容产品" "n" -- "1" "内容"
"内容" <|-- "集合内容"
"内容" <|-- "单体内容"
"集合内容" "1" o-- "n" "单体内容" : 归属于 <

"内容" "1" *-- "n" "扩展信息"
"扩展信息" <|-- "音频扩展"
"扩展信息" <|-- "视频扩展"
"扩展信息" <|-- "图片扩展"
"扩展信息" <|-- "图书扩展"
"扩展信息" <|-- "应用包扩展"

"内容" "1" *-- "n" "内容文件"
"内容" "1" *-- "n" "版权信息"
"内容" "1" *-- "n" "内容角色信息"
"内容" "1" *-- "n" "内容受众"
"内容" "1" *-- "n" "内容题材"
@enduml
```

#### 合约类产品
```plantuml
@startuml
@startuml
skinparam class {
  BackgroundColor WhiteSmoke 
  ArrowColor DimGray
  BorderColor Silver
}

skinparam stereotype {
  CBackgroundColor WhiteSmoke
  IBackgroundColor Azure
  ABackgroundColor FloralWhite
  EBackgroundColor GhostWhite
}

class "产品"

"产品" -- "渠道" : 发布到 >
"产品" -- "BOSS业务代码" : 对应 >
"产品" "1" o-- "1" "计费策略" : 包含 >
"产品" "1" o-- "0..1" "专区包" : 包含 >
"产品" "1" o-- "0..1" "内容" : 包含 >
"产品" "1" o-- "n" "产品服务" : 包含 >
"专区包" -- "内容"
"专区包" -- "内容上架规则"
"内容" -- "合作伙伴" : 归属于 >

@enduml
```

## Data Model
#### 产品
产品分为两大类：合约产品，点播产品；
点播产品又分为：点播产品(音乐)，点播产品(游戏)，点播产品(动漫阅读)和点播产品(视讯)

```plantuml
@startuml
skinparam class {
  BackgroundColor WhiteSmoke 
  ArrowColor DimGray
  BorderColor Silver
}

skinparam stereotype {
  CBackgroundColor WhiteSmoke
  IBackgroundColor Azure
  ABackgroundColor FloralWhite
  EBackgroundColor GhostWhite
}

class "产品" {
  产品名称
  产品编码
  产品状态
  产品类型
}

"产品" <|-- "点播产品"
"产品" <|-- "合约产品"

"点播产品" <|-- "点播产品(音乐)"
"点播产品" <|-- "点播产品(游戏)"
"点播产品" <|-- "点播产品(动漫阅读)"
"点播产品" <|-- "点播产品(视讯)"

@enduml
```

产品组成
```plantuml
@startuml
skinparam class {
  BackgroundColor WhiteSmoke 
  ArrowColor DimGray
  BorderColor Silver
}

skinparam stereotype {
  CBackgroundColor WhiteSmoke
  IBackgroundColor Azure
  ABackgroundColor FloralWhite
  EBackgroundColor GhostWhite
}

"产品" "1" o-- "n" "内容" : contains >
"产品" "1" o-- "n" "专区包" : contains >
"产品" "1" o-- "n" "资费策略" : contains >
@enduml
```


内容
```plantuml
@startuml
skinparam class {
  BackgroundColor WhiteSmoke 
  ArrowColor DimGray
  BorderColor Silver
}

skinparam stereotype {
  CBackgroundColor WhiteSmoke
  IBackgroundColor Azure
  ABackgroundColor FloralWhite
  EBackgroundColor GhostWhite
}

"内容" <|-- "集合内容"
"内容" <|-- "单体内容"
"集合内容" "1" o-- "n" "内容" : 父-子 <
"专区包" "n"--"m" "内容" : "标签"
@enduml
```

资费策略
```plantuml
@startuml
skinparam class {
  BackgroundColor WhiteSmoke 
  ArrowColor DimGray
  BorderColor Silver
}

skinparam stereotype {
  CBackgroundColor WhiteSmoke
  IBackgroundColor Azure
  ABackgroundColor FloralWhite
  EBackgroundColor GhostWhite
}

"资费策略" <|-- "标准资费"
"资费策略" <|-- "折扣"
"资费策略" <|-- "配额"
"资费策略" <|-- "会员升级"
@enduml
```

产品
```plantuml
@startuml
skinparam class {
  BackgroundColor WhiteSmoke 
  ArrowColor DimGray
  BorderColor Silver
}

skinparam stereotype {
  CBackgroundColor WhiteSmoke
  IBackgroundColor Azure
  ABackgroundColor FloralWhite
  EBackgroundColor GhostWhite
}

"资费策略" <|-- "折扣"
"资费策略" <|-- "配额"
"资费策略" <|-- "会员升级"
@enduml
```


```plantuml
@startuml
skinparam class {
  BackgroundColor WhiteSmoke 
  ArrowColor DimGray
  BorderColor Silver
}

skinparam stereotype {
  CBackgroundColor WhiteSmoke
  IBackgroundColor Azure
  ABackgroundColor FloralWhite
  EBackgroundColor GhostWhite
}

class "产品"
class "点播产品"
class "合约产品"
class "内容"
class "专区包"

"产品" <|-- "点播产品"
"产品" <|-- "合约产品"

"内容" <|-- "集合内容"
"内容" <|-- "单体内容"

"专区包" "1" o-- "n" "内容" : contains

"产品" "1" o-- "n" "资费" : contains
"合约产品" "1" o-- "n" "内容" : contains
"合约产品" "1" o-- "n" "专区包" : contains
@enduml
```
