---
title: 管理 NSG 流日志 - Azure REST API
titleSuffix: Azure Network Watcher
description: 此页说明如何在 Azure 网络观察程序中使用 REST API 管理网络安全组流日志
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: cede3018f8922c6771f81470a714eed430cd5cdf
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291876"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>使用 REST API 配置网络安全组流日志

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

网络安全组流日志是网络观察程序的一项功能，用于查看通过网络安全组的入口和出口 IP 流量的信息。 这些流日志以 json 格式编写，并根据规则显示出站和入站流、流所适用的 NIC、有关流的 5 元组信息（源/目标 IP、源/目标端口、协议），以及是允许还是拒绝流量。

## <a name="before-you-begin"></a>开始之前

通过 PowerShell 调用 REST API 时，使用的是 ARMclient。 根据 [Chocolatey 上的 ARMClient](https://chocolatey.org/packages/ARMClient) 中所述在 chocolatey 上找到 ARMClient。 可在[此处](/rest/api/network-watcher/flowlogs)找到 NSG 流日志 REST API 详细规范 

此方案假定已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。

> [!Important]
> 对于网络观察程序 REST API 调用来说，请求 URI 中的资源组名称是包含网络观察程序的资源组，而不是要对其执行诊断操作的资源。

## <a name="scenario"></a>方案

本文中介绍的方案演示了如何使用 REST API 启用、禁用和查询流日志。 若要了解有关网络安全组流日志记录的详细信息，请访问[网络安全组流日志记录 - 概述](network-watcher-nsg-flow-logging-overview.md)。

在此方案中，将：

* 启用流日志（版本 2）
* 禁用流日志
* 查询流日志状态

## <a name="log-in-with-armclient"></a>使用 ARMClient 登录

使用 Azure 凭据登录到 armclient。

```powershell
armclient login
```

## <a name="register-insights-provider"></a>注册 Insights 提供程序

要使流日志记录正常工作，必须注册 **Microsoft.Insights** 提供程序。 如果不确定 **Microsoft.Insights** 提供程序是否已注册，请运行以下脚本。

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>启用网络安全组流日志

以下示例显示了用于启用流日志版本 2 的命令。 对于版本 1，请将“version”字段替换为“1”：

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

上述示例返回的响应如下所示：

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```
> [!NOTE]
> - 以上使用的[网络观察程序 - 设置 Flow 日志配置](/rest/api/network-watcher/network-watchers/set-flow-log-configuration)为旧 API，可能很快就会弃用。
> - 建议改为使用新的 [Flow 日志 - 创建或更新](/rest/api/network-watcher/flow-logs/create-or-update) REST API。

## <a name="disable-network-security-group-flow-logs"></a>禁用网络安全组流日志

使用以下示例禁用流日志。 该调用与启用流日志相同，但为 enabled 属性设置 **false** 除外。

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

上述示例返回的响应如下所示：

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

> [!NOTE]
> - 以上使用的[网络观察程序 - 设置 Flow 日志配置](/rest/api/network-watcher/network-watchers/set-flow-log-configuration)为旧 API，可能很快就会弃用。
> - 建议使用新的 [Flow 日志 - 创建或更新](/rest/api/network-watcher/flow-logs/create-or-update) REST API 来禁用流日志，并使用 [Flow 日志 - 删除](/rest/api/network-watcher/flow-logs/delete)来删除流日志资源。

## <a name="query-flow-logs"></a>查询流日志

以下 REST 调用查询有关网络安全组的流日志的状态。

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

以下是返回的响应的示例：

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

> [!NOTE]
> - 上面使用的 API [网络观察程序 - 获取 Flow 日志状态](/rest/api/network-watcher/network-watchers/get-flow-log-status)需要在网络观察程序的资源组中使用额外的“读取”权限。 此外，此 API 为旧版，可能很快就会弃用。
> - 建议改为使用新的 [Flow 日志 - 获取](/rest/api/network-watcher/flow-logs/get) REST API。

## <a name="download-a-flow-log"></a>下载流日志

流日志的存储位置是在创建时定义的。 用于访问这些保存到存储帐户的流日志的便利工具是 Microsoft Azure 存储资源管理器，下载地址为：https://storageexplorer.com/

如果指定了存储帐户，则数据包捕获文件将保存到以下位置的存储帐户：

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>后续步骤

了解如何[使用 PowerBI 直观地显示 NSG 流日志](network-watcher-visualize-nsg-flow-logs-power-bi.md)

了解如何[使用开源工具直观地显示 NSG 流日志](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)