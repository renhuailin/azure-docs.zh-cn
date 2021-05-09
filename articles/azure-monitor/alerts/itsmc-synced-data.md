---
title: 从 IT 服务管理产品同步到 LA 工作区的数据
description: 本文概述了从 IT 服务管理产品同步到 LA 工作区的数据。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 83994c1b6e150342a777a079d79d6a594d30c3ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041597"
---
# <a name="data-synced-from-your-itsm-product"></a>从 IT 服务管理产品同步的数据

事件和更改请求根据连接配置（使用“同步数据”字段）从 IT 服务管理工具同步到 Log Analytics 工作区。
* [ServiceNow](./itsmc-connections-servicenow.md)
* [System Center Service Manager](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>已同步的数据

本部分介绍 ITSMC 收集的数据的一些示例。

ServiceDesk_CL 中的字段因导入到 Log Analytics 中的工作项类型而异。 下面是针对两种工作项类型的字段列表：

**工作项：** **事件**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- 服务台连接名称
- 服务台 ID
- 状态
- 紧急性
- 影响
- 优先度
- 升级
- 创建者
- 解决者
- 关闭者
- 源
- 分配给
- 类别
- 标题
- 说明
- 创建日期
- 关闭日期
- 解决日期
- 上次修改日期
- Computer

**工作项：** **更改请求**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- 服务台连接名称
- 服务台 ID
- 创建者
- 关闭者
- 源
- 分配给
- 标题
- 类型
- 类别
- 状态
- 升级
- 冲突状态
- 紧急性
- 优先度
- 风险
- 影响
- 分配给
- 创建日期
- 关闭日期
- 上次修改日期
- 请求日期
- 计划开始日期
- 计划结束日期
- 工作开始日期
- 工作结束日期
- 说明
- Computer

## <a name="servicenow-example"></a>ServiceNow 示例 
### <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 事件的输出数据

| Log Analytics 字段 | ServiceNow 字段 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | 状态 |
| Urgency_s |紧急性 |
| Impact_s |影响|
| Priority_s | 优先度 |
| CreatedBy_s | 打开者 |
| ResolvedBy_s | 解决者|
| ClosedBy_s  | 关闭者 |
| Source_s| 联系类型 |
| AssignedTo_s | 已分配到  |
| Category_s | 类别 |
| Title_s|  简短说明 |
| Description_s|  说明 |
| CreatedDate_t|  已打开 |
| ClosedDate_t| 已关闭|
| ResolvedDate_t|已解决|
| Computer  | 配置项 |

### <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 更改请求的输出数据

| Log Analytics | ServiceNow 字段 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 请求者 |
| ClosedBy_s | 关闭者 |
| AssignedTo_s | 已分配到  |
| Title_s|  简短说明 |
| Type_s|  类型 |
| Category_s|  类别 |
| CRState_s|  状态|
| Urgency_s|  紧急性 |
| Priority_s| 优先度|
| Risk_s| 风险|
| Impact_s| 影响|
| RequestedDate_t  | 请求日期 |
| ClosedDate_t | 关闭日期 |
| PlannedStartDate_t  | 计划开始日期 |
| PlannedEndDate_t  | 计划结束日期 |
| WorkStartDate_t  | 实际开始日期 |
| WorkEndDate_t | 实际结束日期|
| Description_s | 说明 |
| Computer  | 配置项 |

## <a name="next-steps"></a>后续步骤

* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)
