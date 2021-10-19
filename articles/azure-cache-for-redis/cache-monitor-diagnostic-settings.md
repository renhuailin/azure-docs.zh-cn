---
title: 使用诊断设置监视 Azure Cache for Redis 数据
titleSuffix: Azure Cache for Redis
description: 了解如何使用诊断设置来监视与 Azure Cache for Redis 连接的 IP 地址。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: e62215649a79f16fcb9cbfc20cfe16bc98f2251f
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812382"
---
# <a name="monitor-azure-cache-for-redis-data-using-diagnostic-settings"></a>使用诊断设置监视 Azure Cache for Redis 数据

Azure 中的诊断设置用于收集资源日志。 Azure 资源日志由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 这些日志是按请求捕获的，也称为“数据平面日志”。 这些日志的内容因资源类型而异。

Azure Cache for Redis 使用 Azure 诊断设置来记录有关与缓存建立的所有客户端连接的信息。 记录并分析此诊断设置有助于了解谁正在连接到缓存，以及这些连接的时间戳。 此数据可用于识别安全违规范围以及进行安全审核。

配置后，缓存将开始按 IP 地址记录传入的客户端连接。 它还会记录源自每个唯一 IP 地址的连接数。 日志不是累积性的。 它们表示按 10 秒间隔创建的时间点快照。

可为 Azure Cache for Redis 实例启用诊断设置，并将资源日志发送到以下目标：

- 事件中心 - 启用虚拟网络时，诊断设置无法访问事件中心资源。 在事件中心内启用“允许受信任的 Microsoft 服务绕过此防火墙?”设置，以授予对事件中心资源的访问权限。 事件中心必须与缓存位于同一区域。
- 存储帐户 - 必须与缓存位于同一区域。

有关诊断要求的详细信息，请参阅[诊断设置](/azure/azure-monitor/essentials/diagnostic-settings?tabs=CMD)。

将诊断日志发送到任一目标时，你将按照正常数据费率支付存储帐户和事件中心使用费。 计费是在 Azure Monitor 而不是 Azure Cache for Redis 中发生的。
有关更多定价信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="create-diagnostics-settings-via-the-azure-portal"></a>通过 Azure 门户创建诊断设置

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 导航到你的 Azure Cache for Redis 帐户。 打开左侧“监视”部分下的“诊断设置”窗格 。 然后选择“添加诊断设置”。

   :::image type="content" source="media/cache-monitor-diagnostic-settings/cache-monitor-diagnostic-setting.png" alt-text="选择诊断":::

1. 在“诊断设置”窗格中，从“类别详细信息”中选择“ConnectedClientList”  。

   |类别  | 定义  | 键属性   |
   |---------|---------|---------|
   |ConnectedClientList |  连接到缓存的客户端的 IP 地址和计数，定期记录。 | `connectedClients`，嵌套在 `ip`、`count`、`privateLinkIpv6` 中 |
  
1. 选择“类别详细信息”后，将日志发送到首选目标。 选择右侧的信息。

    :::image type="content" source="media/cache-monitor-diagnostic-settings/diagnostics-resource-specific.png" alt-text="选择“启用特定于资源”":::

## <a name="create-diagnostic-setting-via-rest-api"></a>通过 REST API 创建诊断设置

使用 Azure Monitor REST API 通过交互式控制台创建诊断设置。 有关详细信息，请参阅[创建或更新](/rest/api/monitor/diagnostic-settings/create-or-update.md)。

### <a name="request"></a>请求

```http
PUT https://management.azure.com/{resourceUri}/providers/Microsoft.Insights/diagnosticSettings/{name}?api-version=2017-05-01-preview
```

### <a name="headers"></a>头文件

   | 参数/标头 | 值/说明 |
   |---------|---------|
   | name | 诊断设置的名称。 |
   | resourceUri | subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Cache/Redis/{CACHE_NAME} |
   | api-version | 2017-05-01-preview |
   | Content-Type | application/json |

### <a name="body"></a>正文

```json
{
    "properties": {
      "storageAccountId": "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/apptest/providers/Microsoft.Storage/storageAccounts/appteststorage1",
      "eventHubAuthorizationRuleId": "/subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/eventhubs/myeventhub/authorizationrules/myrule",
      "eventHubName": "myeventhub",
      "logs": [
        {
          "category": "ConnectedClientList",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>通过 Azure CLI 创建诊断设置

在 Azure CLI 中使用 `az monitor diagnostic-settings create` 命令来创建诊断设置。 有关此命令和参数说明的详细信息，请参阅[创建诊断设置以将平台日志和指标发送到不同的目标](../azure-monitor/essentials/diagnostic-settings.md)。

```azurecli

az monitor diagnostic-settings create 
    --resource /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Cache/Redis/myname
    --name constoso-setting
    --logs '[{"category": "ConnectedClientList","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'    
    --event-hub MyEventHubName 
    --event-hub-rule /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/authorizationrules/RootManageSharedAccessKey 
    --storage-account /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Storage/storageAccounts/myuserspace


```

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Azure 门户、CLI 或 PowerShell 创建诊断设置，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)一文。
