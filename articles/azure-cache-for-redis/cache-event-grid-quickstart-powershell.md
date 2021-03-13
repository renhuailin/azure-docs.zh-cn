---
title: 快速入门：使用 PowerShell 将 Azure Cache for Redis 事件路由到 Web 终结点
description: 使用 Azure 事件网格订阅 Azure Cache for Redis 事件、将事件发送到 Webhook，并处理 Web 应用中的事件。
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 6c3b433a8e433f39b723a7155bb6de116857efca
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508157"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>快速入门：使用 PowerShell 将 Azure Cache for Redis 事件路由到 Web 终结点

Azure 事件网格是针对云的事件处理服务。 在本快速入门中，你将使用 Azure PowerShell 订阅 Azure Cache for Redis 事件、触发事件并查看结果。 

通常，你会将事件发送到处理事件数据并执行操作的终结点。 但是，为了简化本快速入门，你需要将事件发送到要收集和显示消息的 Web 应用。 完成本快速入门所述步骤后，即可看到事件数据已发送到 Web 应用。

## <a name="setup"></a>设置

本快速入门要求运行最新版本的 Azure PowerShell。 如需进行安装或升级，请参阅[安装和配置 Azure PowerShell](/powershell/azure/install-Az-ps)。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的说明进行身份验证。

```powershell
Connect-AzAccount
```

此示例使用 westus2，并将所选内容存储在变量中以供整个过程使用。

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>创建资源组

事件网格主题部署为单个 Azure 资源，且必须在 Azure 资源组下进行预配。 资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。

以下示例在“westus2”位置创建名为“gridResourceGroup”的资源组。  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>创建用于 Redis 的 Azure 缓存实例 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
若要详细了解如何在 PowerShell 中创建缓存实例，请查看 [Azure PowerShell 参考](/powershell/module/az.rediscache/new-azrediscache)。 

## <a name="create-a-message-endpoint"></a>创建消息终结点

在订阅主题之前，让我们创建事件消息的终结点。 通常情况下，终结点基于事件数据执行操作。 为了简化此快速入门，将部署用于显示事件消息的[预建的 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)。 所部署的解决方案包括应用服务计划、应用服务 Web 应用和 GitHub 中的源代码。

将 `<your-site-name>` 替换为 Web 应用的唯一名称。 Web 应用名称必须唯一，因为它是 DNS 条目的一部分。

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

部署可能需要几分钟才能完成。 部署成功后，请查看 Web 应用以确保它正在运行。 在 Web 浏览器中导航到 `https://<your-site-name>.azurewebsites.net`

应会看到站点上当前未显示任何消息。

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="空的事件网格查看器站点。":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>订阅 Azure Cache for Redis 事件

在此步骤中，你将订阅主题，使事件网格知道你想要跟踪哪些事件。以下示例将订阅你创建的缓存实例，并将 Web 应用中的 URL 作为事件通知的终结点进行传递。 Web 应用的终结点必须包括后缀 `/api/updates/`。

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

再次查看 Web 应用，并注意现已向该应用发送了订阅验证事件。 选择眼睛图标以展开事件数据。 事件网格发送验证事件，以便终结点可以验证它是否想要接收事件数据。 Web 应用包含用于验证订阅的代码。

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure 事件网格查看器。":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>从 Azure Cache for Redis 触发事件

现在，让我们触发一个事件，看事件网格如何将消息分发到终结点。

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
若要详细了解如何在 PowerShell 中导入内容，请查看 [Azure PowerShell 参考](/powershell/module/az.rediscache/import-azrediscache)。 

现已触发事件，并且事件网格已将消息发送到订阅时配置的终结点。 查看 Web 应用以查看刚刚发送的事件。

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>清理资源
如果计划继续使用此 Azure Cache for Redis 实例和事件订阅，请不要清理在本快速入门中创建的资源。 如果不打算继续学习，请使用以下命令删除这些资源。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

你现已了解如何创建主题和事件订阅后，接下来请详细了解 Azure Cache for Redis 事件以及事件网格的功能：

- [对 Azure Cache for Redis 事件作出反应](cache-event-grid.md)
- [关于事件网格](../event-grid/overview.md)