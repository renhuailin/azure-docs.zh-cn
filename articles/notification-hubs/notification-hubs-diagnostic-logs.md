---
title: Azure 通知中心诊断日志 |Microsoft Docs
description: 本文概述了可用于 Azure 通知中心的所有操作和诊断日志。
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b532dca6ceba44a32132bf64b322e1b4764fd5fa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417107"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>为通知中心启用诊断日志

开始使用 Azure 通知中心命名空间时，可能需要监视创建、删除或访问命名空间的方式和时间。 本文概述所有可用的操作日志和诊断日志。

Azure 通知中心当前支持活动和操作日志，它们捕获在 Azure 通知中心命名空间上执行的 *管理操作* 。

## <a name="diagnostic-logs-schema"></a>诊断日志架构

所有日志均以 JavaScript 对象表示法 (JSON) 格式存储在以下两个位置：

- **AzureActivity**：显示通过 Azure 门户或 Azure 资源管理器模板部署对命名空间执行操作时生成的日志。
- **AzureDiagnostics**：显示使用 API 或通过语言 SDK 中的管理客户端对命名空间执行操作时生成的日志。

诊断日志 JSON 字符串包括下表中列出的元素：

| 名称 | 说明 |
| ------- | ------- |
| time | 日志的 UTC 时间戳 |
| ResourceId | Azure 资源的相对路径 |
| operationName | 管理操作的名称 |
| category | 日志类别。 有效值： `OperationalLogs` |
| callerIdentity | 启动管理操作的调用方的标识 |
| resultType | 管理操作的状态。 有效值： `Succeeded` 或 `Failed` |
| resultDescription | 管理操作的说明 |
| correlationId | 如果指定，则为管理操作 (相关 ID)  |
| callerIpAddress | 调用方 IP 地址。 对于源自 Azure 门户的调用为空 |

下面是运行日志 JSON 字符串的示例：

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

此 `callerIdentity` 字段可以为空，也可以为具有以下格式之一的 JSON 字符串。

对于源自 Azure 门户的调用，该 `identity` 字段为空。 日志可以与活动日志相关联，以确定已登录的用户。
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

对于通过 Azure 发出的调用资源管理器 `identity` 字段将包含已登录用户的用户名。
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

对于对通知中心的调用 REST API `identity` 字段将包含用于生成 SharedAccessSignature 令牌的访问策略的名称。
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>在操作日志中捕获的事件和操作

操作日志将捕获在 Azure 通知中心命名空间上执行的所有管理操作。 由于在 Azure 通知中心进行的数据操作量很大，因此不会捕获数据操作。

在操作日志中捕获以下管理操作： 

| 作用域 | 操作名称 | 操作说明 |
| :-- | :-- | :-- |
| 命名空间 | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 列出授权规则 |
| 命名空间 | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 删除授权规则 |
| 命名空间 | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 列出密钥 |
| 命名空间 | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 获取授权规则 |
| 命名空间 | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 重新生成密钥 |
| 命名空间 | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 创建或更新授权规则 |
| 命名空间 | Microsoft.NotificationHubs/Namespaces/delete | 删除命名空间 |
| 命名空间 | Microsoft.NotificationHubs/Namespaces/read | 获取命名空间 |
| 命名空间 | Microsoft.NotificationHubs/Namespaces/write | 创建或更新命名空间 |
| 通知中心 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | 列出授权规则 |
| 通知中心 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | 删除授权规则 |
| 通知中心 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | 列出密钥 |
| 通知中心 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | 读取授权规则 |
| 通知中心 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | 重新生成密钥 |
| 通知中心 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | 创建或更新授权规则 |
| 通知中心 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/delete | 删除通知中心 |
| 通知中心 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | 创建、更新或获取 PNS 凭据 |
| 通知中心 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | 获取通知中心 |
| 通知中心 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | 创建或更新通知中心 |

## <a name="enable-operational-logs"></a>启用操作日志

操作日志默认已禁用。 若要启用日志，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，请参阅 Azure 通知中心命名空间，然后在 " **监视**" 下选择 "  **诊断设置**"。

   ![“诊断设置”链接](./media/notification-hubs-diagnostic-logs/image-1.png)

1. 在“诊断设置”窗格中，选择“添加诊断设置”。  

   ![“添加诊断设置”链接](./media/notification-hubs-diagnostic-logs/image-2.png)

1. 执行以下操作来配置诊断设置：

   a. 在“名称”框中，输入诊断设置的名称。  

   b. 为诊断日志选择以下三个目标之一：  
   - 如果选择 " **发送到 Log Analytics" 工作区**，则需要指定将诊断发送到 Log Analytics 的实例。  
   - 如果选择“存档到存储帐户”，则需要配置用于存储诊断日志的存储帐户。  
   - 如果选择“流式传输到事件中心”，则需要配置要将诊断日志流式传输到的事件中心。

   c. 选中“OperationalLogs”复选框。

    ![“诊断设置”窗格](./media/notification-hubs-diagnostic-logs/image-3.png)

1. 选择“保存” 。

新设置将在大约 10 分钟后生效。 日志将显示在“诊断日志”窗格中配置的存档目标中。

## <a name="next-steps"></a>后续步骤

若要详细了解如何配置诊断设置，请参阅：
* [Azure 诊断日志概述](../azure-monitor/platform/platform-logs-overview.md)。

若要了解有关 Azure 通知中心的详细信息，请参阅：
* [什么是 Azure 通知中心？](notification-hubs-push-notification-overview.md)

