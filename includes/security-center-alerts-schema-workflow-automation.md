---
title: include 文件
description: include 文件
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400964"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>架构的数据模型

|字段|数据类型|说明|
|----|----|----|
|**AlertDisplayName**|String|警报的显示名称。|
|**AlertType**|String|警报的类型。 同一类型的警报应具有相同的值。 此字段是表示警报类型警报类型而不是警报实例的键控字符串。 同一检测逻辑/分析中的所有警报实例都应有相同的警报类型值。|
|**CompromisedEntity**|String|与此警报最相关的资源的显示名称。|
|**说明**|String|警报的说明。|
|**EndTimeUtc**|DateTime|警报中包含的最后一个事件或活动的时间。  字段应为符合 ISO8601 格式的字符串，包括 UTC 时区信息。|
|**实体**|IEnumerable (IEntity)|与警报相关的实体的列表。 此列表可以包含不同类型的实体。 实体类型可以是实体部分中定义的任何类型。 还可以发送不在下面列表中的实体，但是不能保证对其进行处理（警报不会在验证新类型的实体时失败）。|
|**ExtendedProperties**|Dictionary (String,String)|提供程序可能（可选）在此处包含自定义字段。|
|**意向**|枚举|警报背后的终止链相关意向。 有关支持的值的列表以及 Azure 安全中心支持的终止链意向的说明，请参阅[意向](../articles/security-center/alerts-reference.md#intentions)。<br/>此字段可能包含多个值（用逗号分隔）。|
|**IsIncident**|Bool|此字段确定警报是否为事件（多个警报的复合分组）或单个警报。 该字段的默认值为“false”（表示它是单个警报）。|
|**ProcessingEndTime**|DateTime|最终用户在保存警报的原始产品中可以访问警报的时间。|
|ProductName|String|发布此警报的产品的名称（Azure 安全中心、Azure ATP、Microsoft Defender ATP 和 MCAS 等）。|
|**RemediationSteps**|List<String>|要执行以修正警报的手动操作项。|
|**ResourceIdentifiers**|列表（资源标识符）|此警报的资源标识符，可用于将警报定向到正确的产品公开组（租户、工作区、订阅等）。 每个警报可有多个不同类型的标识符。|
|**严重性**|枚举|提供程序报告的警报的严重性。 可能的值：信息性、低、中和高。|
|**StartTimeUtc**|DateTime|警报中包含的第一个事件或活动的时间。 字段应为符合 ISO8601 格式的字符串，包括 UTC 时区信息。|
|**Status**|枚举|警报的生命周期状态。<br/>支持的状态为：新增、已解决、已消除、未知。<br/>如果警报指定的值不是受支持的选项，则会将该警报状态分配为“未知”。<br/>未指定值的警报的状态将分配为“新增”。|
|**SystemAlertId**|String|警报标识符。|
|**TimeGenerated**|DateTime|警报提供程序生成警报的时间。 如果内部警报提供程序未报告，则产品可选择分配接收到警报的时间，以便产品进行处理。  字段应为符合 ISO8601 格式的字符串，包括 UTC 时区信息。|
|**VendorName**|String|引发警报的供应商的名称。|
|||
