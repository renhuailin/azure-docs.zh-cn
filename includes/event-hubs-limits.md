---
title: include 文件
description: include 文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1b5905046fdde4d5574fd4d39f40dce1bbf11e32
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027801"
---
下表提供了特定于 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)的配额和限制。 有关事件中心定价的信息，请参阅[事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。

## <a name="common-limits-for-all-tiers"></a>所有层的通用限制
以下限制在所有层中是通用的。 

| 限制 |  说明 | 值 |
| --- |  --- | --- |
| 每个订阅的事件中心命名空间数 |- |100 |
| 每个命名空间的事件中心数 | 系统会拒绝创建新事件中心的后续请求。 |10 |
| 每个事件中心的分区数 |- |32 |
| 事件中心名称的大小 |- | 256 个字符 |
| 使用者组名称的大小 |- | 256 个字符 |
| 每个使用者组的非 epoch 接收者数 |- |5 |
| 每个命名空间的授权规则数量 | 系统会拒绝创建授权规则的后续请求。|12 |
| GetRuntimeInformation 方法的调用数 |  - | 每秒 50 个 | 
| 虚拟网络 (VNet) 和 IP 配置规则的数量 | - | 128 | 


## <a name="basic-vs-standard-tiers"></a>基本层和标准层
下表显示的限制在基本层和标准层中可能不同。 

| 限制 | 说明 | 基本 | Standard |
|---|---|--|---|
| 事件中心事件的最大大小| &nbsp; | 256 KB | 1 MB |
| 每个事件中心的使用者组数 | &nbsp; |1 |20 |
| 每个命名空间的 AMQP 连接数 | 系统会拒绝后续的附加连接请求，且调用代码会收到异常。 |100 |5,000|
| 事件数据的最长保留期限 | &nbsp; |1 天 |1-7 天 |
| 最大吞吐量单位 |超出此限制会导致数据受到限制，并生成[服务器繁忙异常](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)。 要为标准层请求大量的吞吐量单位，可填写[支持请求](../articles/azure-portal/supportability/how-to-create-azure-support-request.md)。 [额外的吞吐量单位](../articles/event-hubs/event-hubs-auto-inflate.md)将基于承诺的购买以大小为 20 个单位的块的形式提供。 |20 | 20 | 

## <a name="dedicated-tier-vs-standard-tier"></a>专用层和标准层
事件中心专用层产品/服务按固定的每月价格计费，最少用量为 4 小时。 专用层提供标准计划的所有功能，但具有企业规模容量和限制，以满足客户的工作负荷需求。 

请参阅本[文档](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md)，了解如何使用 Azure 门户创建专用“事件中心”群集。

| Feature | 标准 | 专用 |
| --- |:---|:---|
| 带宽 | 20 TU（最多 40 TU） | 20 CU |
| 命名空间 |  1 | 每个 CU 50 |
| 事件中心 |  每个命名空间 10 | 每个命名空间 1000 |
| 入口事件 | 按每百万个事件支付 | 已含 |
| 消息大小 | 1000000 字节 | 1000000 字节 |
| 分区 | 每个事件中心 32 | 每个事件中心 1024 个<br/>每个 CU 2000 个 |
| 使用者组 | 每个事件中心 20 | 每个 CU 无限制，每个事件中心 1000 |
| 中转连接 | 包括 1,000 个，最大 5,000 个 | 包括 100000 个，最大 100000 个 |
| 消息保留期 | 7 天，每个 TU 包含 84 GB | 90 天，每个 CU 包含 10 TB |
| 捕获 | 按每小时支付 | 已含 |


## <a name="schema-registry-limitations"></a>架构注册表限制

### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>标准层和专用层中的相同限制  
| 功能 | 限制 | 
|---|---|--|
| 架构组名称的最大长度 | 50 |  
| 架构名称的最大长度 | 100 |    
| 每个架构的大小（字节） | 1 MB |   
| 每个架构组的属性数 | 1024 |
| 每组属性键的大小（字节） | 256 | 
| 每个组属性值的大小（字节） | 1024 | 


### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>标准层和专用层中的不同限制  

| 限制 | 标准 | 专用 | 
|---|---|--|---|
| 架构注册表（命名空间）的大小（以兆字节为单位） | 25 |  1024 |
| 架构注册表或命名空间中的架构组数 | 1 - 排除默认组 | 1000 |
| 所有架构组的架构版本数 | 25 | 10000 |