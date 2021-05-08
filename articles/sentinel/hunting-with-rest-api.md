---
title: 使用 REST API 在 Azure Sentinel 中管理搜寻和实时流查询 | Microsoft Docs
description: 本文介绍了如何通过 Azure Sentinel 搜寻功能来使用 Log Analytics 的 REST API 管理搜寻和实时流查询。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 64f05e18ff757d9f086cf06d74109bf64e32a05c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98795695"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>使用 REST API 在 Azure Sentinel 中管理搜寻和实时流查询

Azure Sentinel 部分在 Azure Monitor Log Analytics 上构建，支持你使用 Log Analytics 的 REST API 来管理搜寻和实时流查询。 本文档演示如何使用 REST API 创建和管理搜寻查询。  以这种方式创建的查询将显示在 Azure Sentinel UI 中。

有关[保存的搜索 API](/rest/api/loganalytics/savedsearches) 的详细信息，请参阅权威 REST API 参考。

## <a name="api-examples"></a>API 示例

在下面的示例中，请将这些占位符替换为下表中规定的替换项：

| 占位符 | 替换为 |
|-|-|
| **{subscriptionId}** | 要应用搜寻或实时流查询的订阅的名称。 |
| **{resourceGroupName}** | 要应用搜寻或实时流查询的资源组的名称。 |
| **{savedSearchId}** | 每个搜寻查询 (GUID) 的唯一 id。 |
| **{WorkspaceName}** | 作为查询目标的 Log Analytics 工作区的名称。 |
| **{DisplayName}** | 所选择的查询的名称。 |
| **{Description}** | 搜寻或实时流查询的说明。 |
| **{Tactics}** | 应用于查询的相关 MITRE ATT&CK 技巧。 |
| **{Query}** | 查询的查询表达式。 |
|  

### <a name="example-1"></a>示例 1

此示例演示如何创建或更新给定 Azure Sentinel 工作区的搜寻查询。  对于实时流查询，请将“Category”: “Hunting Queries”替换为“请求正文”中的“Category”: “Livestream Queries”：  

#### <a name="request-header"></a>请求标头

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>请求正文

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>示例 2

此示例演示如何删除给定 Azure Sentinel 工作区的搜寻或实时流查询：

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>示例 3

此示例演示如何检索给定工作区的搜寻或实时流查询：

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Log Analytics API 在 Azure Sentinel 中管理搜寻和实时流查询。 要详细了解 Azure Sentinel，请参阅以下文章：

- [主动搜寻威胁](hunting.md)
- [使用笔记本运行自动搜寻活动](notebooks.md)