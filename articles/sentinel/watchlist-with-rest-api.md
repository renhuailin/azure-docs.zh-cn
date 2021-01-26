---
title: 使用 REST API 在 Azure Sentinel 中管理 watchlists |Microsoft Docs
description: 本文介绍如何使用 Log Analytics "REST API 来管理 Azure Sentinel watchlists，以创建、修改和删除 watchlists 及其项。
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
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798378"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>使用 REST API 在 Azure Sentinel 中管理 watchlists

> [!IMPORTANT]
> Watchlists 功能目前处于 **预览阶段**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

Azure Sentinel 是在 Azure Monitor Log Analytics 上构建的，它使你可以使用 Log Analytics "REST API 来管理 watchlists。 本文档演示如何使用 REST API 创建、修改和删除 watchlists 及其项。  以这种方式创建的 Watchlists 将显示在 Azure Sentinel UI 中。

## <a name="common-uri-parameters"></a>通用 URI 参数

下面是所有播放列表 API 命令的常见 URI 参数：

| 名称 | In | 必须 | 类型 | 说明 |
|-|-|-|-|-|
| **订阅** | path | 是 | GUID | Azure 订阅 ID |
| **ResourceGroupName** | path | 是 | string | 订阅中的资源组的名称 |
| **WorkspaceName** | path | 是 | string | Log Analytics 工作区的名称 |
| **{watchlistAlias}** | path | 是* | string | 给定播放列表的名称<br>\* 检索所有 watchlists 时不需要 |
| **{watchlistItemId}** | path | 是 * * | GUID | 要在播放列表中创建、添加或删除的项的 ID<br>\*\* 仅对播放列表项命令是必需的 |
| **{api 版本}** | query | 是 | string | 用于发出此请求的协议的版本。 从2020年10月29日起，播放列表 API 版本为 *2019-01-01-preview* |
| **{bearerToken}** | authorization | 是 | 令牌 | 持有者授权令牌 |
|  

## <a name="retrieve-all-watchlists"></a>检索所有 watchlists

此命令检索与工作区关联的所有 watchlists，而不包含它们的项。

### <a name="request-uri"></a>请求 URI
 (URI 是单行，为方便可读性) 提供分解

| 方法 | 请求 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>响应

| 状态代码 | 响应正文 | 说明 |
|-|-|-|
| 200/OK | 现有 watchlists 的列表，如果未找到播放列表，则为空 |  |
| 400/错误的请求 |  | 请求语法格式错误，请求参数无效 .。。 |
|

## <a name="look-up-a-watchlist-by-name"></a>按名称查找播放列表

此命令检索与工作区关联的特定播放列表，而不包含其项。

### <a name="request-uri"></a>请求 URI
 (URI 是单行，为方便可读性) 提供分解

| 方法 | 请求 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>响应

| 状态代码 | 响应正文 | 说明 |
|-|-|-|
| 200/OK | 请求的播放列表 |  |
| 400/错误的请求 |  | 请求语法格式错误，请求参数无效 .。。 |
| 404/找不到 |  | 找不到具有所请求名称的播放列表 |
|

## <a name="create-a-watchlist"></a>创建播放列表

此命令创建播放列表，并向其中添加项。 它需要对此终结点进行两次调用以创建播放列表及其项：第一个调用将创建播放列表 (父) ，第二个调用将添加项。

### <a name="request-uri"></a>请求 URI
 (URI 是单行，为方便可读性) 提供分解

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
| 200/OK | 请求创建的播放列表，没有项 |  |
| 400/错误的请求 |  | 请求语法格式错误，请求参数无效 .。。 |
| 409/冲突 |  | 操作失败，存在具有相同别名的现有播放列表 |
|

## <a name="delete-a-watchlist"></a>删除播放列表

此命令删除播放列表及其项。

### <a name="request-uri"></a>请求 URI
 (URI 是单行，为方便可读性) 提供分解

| 方法 | 请求 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>响应

| 状态代码 | 响应正文 | 说明 |
|-|-|-|
| 200/OK | 空响应正文 |  |
| 204/无内容 | 空响应正文 | 未删除任何内容 |
| 400/错误的请求 |  | 请求语法格式错误，请求参数无效 .。。 |
|

## <a name="add-or-update-a-watchlist-item"></a>添加或更新播放列表项

在现有 *watchlisItemId* 上运行此命令时 (GUID) ，它将使用请求正文中提供的数据更新项。 否则，将创建一个新项。

### <a name="request-uri"></a>请求 URI
 (URI 是单行，为方便可读性) 提供分解

| 方法 | 请求 URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>请求正文

下面是播放列表项的 "添加/更新" 请求正文的示例：

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
| 200/OK | 请求创建或更新的播放列表项 |  |
| 400/错误的请求 |  | 请求语法格式错误，请求参数无效 .。。 |
| 409/冲突 |  | 操作失败，存在具有相同别名的现有播放列表 |
|

## <a name="delete-a-watchlist-item"></a>删除播放列表项目

此命令删除现有的播放列表项。

### <a name="request-uri"></a>请求 URI
 (URI 是单行，为方便可读性) 提供分解

| 方法 | 请求 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>响应

| 状态代码 | 响应正文 | 说明 |
|-|-|-|
| 200/OK | 空响应正文 |  |
| 204/无内容 | 空响应正文 | 未删除任何内容 |
| 400/错误的请求 |  | 请求语法格式错误，请求参数无效 .。。 |
|

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Log Analytics API 管理 Azure Sentinel 中的 watchlists 及其项目。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解有关[watchlists](watchlists.md)的详细信息
- 了解[Azure SENTINEL API](/rest/api/securityinsights/)的其他用法