---
title: 使用 REST API 在 Azure Sentinel 中管理播放列表| Microsoft Docs
description: 本文介绍如何使用 Log Analytics 的 REST API 来管理 Azure Sentinel 播放列表，以创建、修改和删除播放列表及其项。
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
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: 254647e6e73683b13bd8c1cbeb7b476066c36a7c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467959"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>使用 REST API 管理 Azure Sentinel 中的播放列表

Azure Sentinel 部分在 Azure Monitor Log Analytics 上构建，支持你使用 Log Analytics 的 REST API 来管理播放列表。 本文档演示如何使用 REST API 创建、修改和删除播放列表及其项。  以这种方式创建的播放列表将显示在 Azure Sentinel UI 中。

## <a name="common-uri-parameters"></a>常见 URI 参数

下面是所有播放列表 API 命令的常见 URI 参数：

| 名称 | 在 | 必需 | 类型 | 说明 |
|-|-|-|-|-|
| “{subscriptionId}” | path | 是 | GUID | Azure 订阅 ID |
| “{resourceGroupName}” | path | 是 | 字符串 | 订阅中的资源组的名称 |
| “{workspaceName}” | path | 是 | 字符串 | Log Analytics 工作区的名称 |
| “{watchlistAlias}” | path | 是* | 字符串 | 给定播放列表的名称<br>\* 检索所有播放列表时不需要 |
| “{watchlistItemId}” | path | 是** | GUID | 要在播放列表中创建、添加或删除的项的 ID<br>\*\* 仅对播放列表项命令是必需的 |
| “{api-version}” | query | 是 | 字符串 | 用于发出此请求的协议的版本。 从 2020 年 10 月 29 日起，播放列表 API 版本为“2019-01-01-preview” |
| “{bearerToken}” | authorization | 是 | 令牌 | 持有者授权令牌 |
|  

## <a name="retrieve-all-watchlists"></a>检索所有播放列表

此命令检索与工作区关联的所有播放列表，而不包含它们的项。

### <a name="request-uri"></a>请求 URI
（URI 是单行，为提高可读性分成多个小段）

| 方法 | 请求 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>响应

| 状态代码 | 响应正文 | 说明 |
|-|-|-|
| 200 / 正常 | 现有播放列表的列表，如果未找到播放列表，则为空 |  |
| 400 / 错误请求 |  | 请求语法格式错误，请求参数无效... |
|

## <a name="look-up-a-watchlist-by-name"></a>按名称查找播放列表

此命令检索与工作区关联的特定播放列表，而不包含其项。

### <a name="request-uri"></a>请求 URI
（URI 是单行，为提高可读性分成多个小段）

| 方法 | 请求 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>响应

| 状态代码 | 响应正文 | 说明 |
|-|-|-|
| 200 / 正常 | 请求的播放列表 |  |
| 400 / 错误请求 |  | 请求语法格式错误，请求参数无效... |
| 404 / 未找到 |  | 找不到具有所请求名称的播放列表 |
|

## <a name="create-a-watchlist"></a>创建播放列表

此命令创建播放列表，并向其中添加项。 此命令需要对此终结点进行两次调用以创建播放列表及其项：第一个调用将创建播放列表（父级），第二个调用将添加项。

### <a name="request-uri"></a>请求 URI
（URI 是单行，为提高可读性分成多个小段）

| 方法 | 请求 URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>请求正文

下面是播放列表创建请求的请求正文示例：

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>响应

| 状态代码 | 响应正文 | 说明 |
|-|-|-|
| 200 / 正常 | 请求创建的播放列表，没有项 |  |
| 400 / 错误请求 |  | 请求语法格式错误，请求参数无效... |
| 409 / 冲突 |  | 操作失败，存在具有相同别名的现有播放列表 |
|

## <a name="delete-a-watchlist"></a>删除播放列表

此命令删除播放列表及其项。

### <a name="request-uri"></a>请求 URI
（URI 是单行，为提高可读性分成多个小段）

| 方法 | 请求 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>响应

| 状态代码 | 响应正文 | 说明 |
|-|-|-|
| 200 / 正常 | 空响应正文 |  |
| 204 / 无内容 | 空响应正文 | 未删除任何内容 |
| 400 / 错误请求 |  | 请求语法格式错误，请求参数无效... |
|

## <a name="add-or-update-a-watchlist-item"></a>添加或更新播放列表项

在现有“watchlisItemId”(GUID) 上运行此命令时，命令将使用请求正文提供的数据更新该项。 否则，将创建一个新的项。

### <a name="request-uri"></a>请求 URI
（URI 是单行，为提高可读性分成多个小段）

| 方法 | 请求 URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>请求正文

下面是播放列表项的“添加/更新”请求正文的示例：

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>响应

| 状态代码 | 响应正文 | 说明 |
|-|-|-|
| 200 / 正常 | 请求创建或更新的播放列表项 |  |
| 400 / 错误请求 |  | 请求语法格式错误，请求参数无效... |
| 409 / 冲突 |  | 操作失败，存在具有相同别名的现有播放列表 |
|

## <a name="delete-a-watchlist-item"></a>删除播放列表项

此命令删除现有的播放列表项。

### <a name="request-uri"></a>请求 URI
（URI 是单行，为提高可读性分成多个小段）

| 方法 | 请求 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>响应

| 状态代码 | 响应正文 | 说明 |
|-|-|-|
| 200 / 正常 | 空响应正文 |  |
| 204 / 无内容 | 空响应正文 | 未删除任何内容 |
| 400 / 错误请求 |  | 请求语法格式错误，请求参数无效... |
|

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Log Analytics API 管理 Azure Sentinel 中的播放列表及其项。 要详细了解 Azure Sentinel，请参阅以下文章：

- 详细了解[播放列表](watchlists.md)
- 了解 [Azure Sentinel API](/rest/api/securityinsights/) 的其他用法
