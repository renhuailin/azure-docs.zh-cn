---
title: include 文件 description: include 文件 services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 05/10/2021 ms.author: spelluru ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

以下限制在所有层中是通用的。 

| 限制 |  注释 | 值 |
| --- |  --- | --- |
 事件中心名称的大小 |- | 256 个字符 |
| 使用者组名称的大小 | Kafka 协议无需创建使用者组。 | <p>Kafka：256 个字符</p><p>AMQP：50 个字符 |
| 每个使用者组的非 epoch 接收者数 |- |5 |
| 每个命名空间的授权规则数量 | 系统会拒绝创建授权规则的后续请求。|12 |
| GetRuntimeInformation 方法的调用数 |  - | 每秒 50 个 | 
| 虚拟网络 (VNet) 数 | - | 128 | 
| IP 配置规则数 | - | 128 | 
| 架构组名称的最大长度 | | 50 |  
| 架构名称的最大长度 | | 100 |    
| 每个架构的大小（字节） | | 1 MB |   
| 每个架构组的属性数 | | 1024 |
| 单个架构组属性键的大小（字节） | | 256 | 
| 单个架构组属性值的大小（字节） | | 1024 | 

