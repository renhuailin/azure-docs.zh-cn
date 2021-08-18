---
title: include 文件 description: include 文件 services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 05/10/2021 ms.author: spelluru ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

下表显示了 Azure 事件中心特定层中可用（或不可用）的功能的列表。 

| 功能 | 基本 |  Standard | 高级 | 专用 |
| ------- | ------| -------- | ------- | --------- |
| 租户 | 多租户 | 多租户 | 进行资源隔离的多租户 | 独占单租户 |
| 专用链接 | 不适用 | 是 | 是 | 是 |
| 客户管理的密钥 <br/>（自带密钥） | 不适用 | 空值 | 是 | 是 |
| 捕获 | 不适用 | 单独定价 | 已含 | 已含 |
| 动态分区横向扩展 | 不适用 | 空值 | 是 | 是 |
| 入口事件 | 按每百万个事件支付 | 按每百万个事件支付 | 已含 | 已含

> [!NOTE]
> 表中的“已含”表示该功能可用，并且使用它不需要单独付费。 







 