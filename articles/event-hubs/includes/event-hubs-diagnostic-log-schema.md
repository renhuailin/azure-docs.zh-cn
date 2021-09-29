---
title: include 文件
description: include 文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 06/11/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ce6451bb1293f9cf3d85ff2e92cf6a5f8f42ba52
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220720"
---
事件中心会捕获以下类别的诊断日志：

| 类别 | 说明 | 
| -------- | ----------- | 
| 存档日志 | 捕获有关[事件中心捕获](../event-hubs-capture-overview.md)操作的信息，特别是与捕获错误相关的日志。 |
| 操作日志 | 捕获在 Azure 事件中心命名空间中执行的所有管理操作。 由于在 Azure 事件中心进行的数据操作量较大，因此不会捕获数据操作。 |
| 自动缩放日志 | 捕获在事件中心命名空间中执行的自动膨胀操作。 |
| Kafka 协调器日志 | 捕获与事件中心相关的 Kafka 协调器操作。 |
| Kafka 用户错误日志 | 捕获事件中心中调用的 Kafka API 的相关信息。 |
| 事件中心虚拟网络 (VNet) 连接事件 | 捕获向事件中心发送流量的 IP 地址和虚拟网络的相关信息。 |
| 客户管理的密钥用户日志 | 捕获与客户管理的密钥相关的操作。 |


所有日志均以 JavaScript 对象表示法 (JSON) 格式存储。 每个条目均包含字符串字段，这些字段采用以下部分所述的格式。

### <a name="archive-logs-schema"></a>存档日志架构

存档日志 JSON 字符串包括下表列出的元素：

名称 | 说明
------- | -------
`TaskName` | 描述失败的任务
`ActivityId` | 用于跟踪的内部 ID
`trackingId` | 用于跟踪的内部 ID
`resourceId` | Azure Resource Manager 资源 ID
`eventHub` | 事件中心的完整名称（包括命名空间名称）
`partitionId` | 要写入到的事件中心分区
`archiveStep` | 可能值：ArchiveFlushWriter、DestinationInit
`startTime` | 失败开始时间
`failures` | 发生失败的次数
`durationInSeconds` | 失败持续时间
`message` | 错误消息
`category` | ArchiveLogs

以下代码是存档日志 JSON 字符串的示例：

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>操作日志架构

运行日志 JSON 字符串包括下表列出的元素：

名称 | 说明
------- | -------
`ActivityId` | 内部 ID，用于跟踪目的 |
`EventName` | 操作名称。 有关此元素的值列表，请参阅[事件名称](#event-names) |
`resourceId` | Azure Resource Manager 资源 ID |
`SubscriptionId` | 订阅 ID |
`EventTimeString` | 操作时间 |
`EventProperties` |操作的属性。 此元素提供有关事件的详细信息，如下面的示例中所示。 |
`Status` | 操作状态。 该值可以是“成功”或“失败”。  |
`Caller` | 操作的调用方（Azure 门户或管理客户端） |
`Category` | OperationalLogs |

以下代码是运行日志 JSON 字符串的示例：

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

#### <a name="event-names"></a>事件名称
事件名称的填充格式是以下枚举中的操作类型 + 资源类型组合。 例如，`Create Queue`、`Retrieve Event Hu` 或 `Delete Rule`。 

| 操作类型 | 资源类型 | 
| -------------- | ------------- | 
| <ul><li>创建</li><li>更新</li><li>删除</li><li>检索</li><li>未知</li></ul> | <ul><li>命名空间</li><li>队列</li><li>主题</li><li>订阅</li><li>EventHub</li><li>EventHubSubscription</li><li>NotificationHub</li><li>NotificationHubTier</li><li>SharedAccessPolicy</li><li>UsageCredit</li><li>NamespacePnsCredentials</li>规则</li>ConsumerGroup</li> |

### <a name="autoscale-logs-schema"></a>自动缩放日志架构
自动缩放日志 JSON 包括下表列出的元素：

| 名称 | 说明 |
| ---- | ----------- | 
| `TrackingId` | 内部 ID，用于跟踪目的 |
| `ResourceId` | Azure 资源管理器资源 ID。 |
| `Message` | 信息性消息，提供有关自动膨胀操作的详细信息。 此消息包含给定命名空间的吞吐量单位的以前值和当前值，以及触发了 TU 膨胀的因素。 |

下面是一个示例自动缩放事件： 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2021 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

### <a name="kafka-coordinator-logs-schema"></a>Kafka 协调器日志架构
Kafka 协调器日志 JSON 包括下表列出的元素：

| 名称 | 说明 |
| ---- | ----------- | 
| `RequestId` | 请求 ID，用于跟踪目的 |
| `ResourceId` | Azure Resource Manager 资源 ID |
| `Operation` | 组协调期间执行的操作的名称 |
| `ClientId` | 客户端 ID |
| `NamespaceName` | 命名空间名称 | 
| `SubscriptionId` | Azure 订阅 ID |
| `Message` | 信息性或警告消息，提供有关组协调期间执行的操作的详细信息。 |

#### <a name="example"></a>示例

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

### <a name="kafka-user-error-logs-schema"></a>Kafka 用户错误日志架构
Kafka 用户错误日志 JSON 包括下表列出的元素：

| 名称 | 说明 |
| ---- | ----------- |
| `TrackingId` | 跟踪 ID，用于跟踪目的。 |
| `NamespaceName` | 命名空间名称 |
| `Eventhub` | 事件中心名称 |
| `PartitionId` | 分区 ID |
| `GroupId` | 组 ID |
| `ClientId` | 客户端 ID |
| `ResourceId` | Azure 资源管理器资源 ID。 |
| `Message` | 信息性消息，提供有关错误的详细信息 |

### <a name="event-hubs-virtual-network-connection-event-schema"></a>事件中心虚拟网络连接事件架构
事件中心虚拟网络 (VNet) 连接事件 JSON 包含下表列出的元素：

| 名称 | 说明 |
| ---  | ----------- | 
| `SubscriptionId` | Azure 订阅 ID |
| `NamespaceName` | 命名空间名称 |
| `IPAddress` | 连接到事件中心服务的客户端的 IP 地址 |
| `Action` | 评估连接请求时事件中心服务执行的操作。 支持的操作为“接受连接”和“拒绝连接” 。 |
| `Reason` | 提供执行操作的原因 |
| `Count` | 给定操作的发生次数 |
| `ResourceId` | Azure 资源管理器资源 ID。 |

只有当命名空间允许从选定的网络或从特定的 IP 地址（IP 筛选器规则）进行访问时，才会生成虚拟网络日志 。 如果不希望使用这些功能限制对命名空间的访问，仍希望获取虚拟网络日志来跟踪连接到事件中心命名空间的客户端的 IP 地址，则可以使用以下解决方法。 [启用 IP 筛选](../event-hubs-ip-filtering.md)，并添加整个可寻址 IPv4 范围 (1.0.0.0/1 - 255.0.0.0/1)。 事件中心 IP 筛选不支持 IPv6 范围。 日志中可能会出现 IPv6 格式的专用终结点地址。 

#### <a name="example"></a>示例

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="customer-managed-key-user-logs-schema"></a>客户管理的密钥用户日志架构
客户管理的密钥用户日志 JSON 包括下表列出的元素：

| 名称 | 说明 |
| ---- | ----------- | 
| `Category` | 消息类别的类型。 以下值之一：“错误”和“信息”。  例如，如果密钥保管库中的密钥处于禁用状态，则它将是信息类别；如果密钥无法展开，则可能发生错误。|
| `ResourceId` | 内部资源 ID，包括 Azure 订阅 ID 和命名空间名称 |
| `KeyVault` | Key Vault 资源的名称 |
| `Key` | 用于加密事件中心命名空间的 Key Vault 密钥的名称。 |
| `Version` | Key Vault 密钥的版本。|
| `Operation` | 对密钥保管库中的密钥执行的操作。 例如，禁用/启用密钥、包装或展开。 |
| `Code` | 与操作关联的代码。 示例：错误代码 404 表示找不到密钥。 |
| `Message` | 消息，提供有关错误或信息性消息的详细信息 |

下面是客户管理的密钥的日志示例：

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

下面是启用 BYOK 加密时要查找的常见错误代码。

| 操作 | 错误代码 | 数据的生成状态 |
| ------ | ---------- | ----------------------- | 
| 从密钥保管库中删除包装/展开权限 | 403 |    Inaccessible |
| 从授予了包装/展开权限的 AAD 主体中删除 AAD 角色成员身份 | 403 |  Inaccessible |
| 从密钥保管库中删除加密密钥 | 404 | Inaccessible |
| 删除密钥保管库 | 404 | 无法访问（假设启用软删除，这是必需的设置。） |
| 更改加密密钥的过期期限，使其已过期 | 403 |   Inaccessible  |
| 更改 NBF（不早于），使密钥加密密钥不会处于活动状态 | 403 | Inaccessible  |
| 为密钥保管库防火墙选择“允许 MSFT 服务”选项，或阻止对具有加密密钥的密钥保管库进行网络访问 | 403 | Inaccessible |
| 将密钥保管库移到其他租户 | 404 | Inaccessible |  
| 间歇性网络问题或 DNS/AAD/MSI 中断 |  | 可使用缓存数据加密密钥进行访问 |

