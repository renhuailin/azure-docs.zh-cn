---
title: 排查 Azure Cosmos DB 的“未找到”异常
description: 了解如何诊断和修复“未找到”异常。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 05/26/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 095f12f3b40844a34c2db30140ea2cef40c839d3
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113407"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>诊断和排查 Azure Cosmos DB 的“未找到”异常
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 状态代码 404 表示资源不再存在。

## <a name="expected-behavior"></a>预期行为
应用程序预期接收代码 404 并正确进行处理的有效方案有许多。

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>对应该存在或确实存在的项返回了“未找到”异常
以下是在该项应该或确实存在的情况下返回状态代码 404 的可能原因。

### <a name="the-read-session-is-not-available-for-the-input-session-token"></a>读取会话不可用于输入会话令牌

#### <a name="solution"></a>解决方案：
1. 将当前 SDK 更新到已发布的最新版本。 此特定错误的最常见原因问题已在最新版 SDK 中得到解决。

### <a name="race-condition"></a>争用条件
有多个 SDK 客户端实例且读取在写入之前发生。

#### <a name="solution"></a>解决方案：
1. Azure Cosmos DB 的默认帐户一致性为会话一致性。 创建或更新项时，响应将返回一个会话令牌，该令牌可以在 SDK 实例之间传递，以确保读取请求在从具有该更改的副本中进行读取。
1. 将[一致性级别](../consistency-levels.md)更改为[更高级别](../consistency-levels.md)。

### <a name="reading-throughput-for-a-container-or-database-resource"></a>读取容器或数据库资源的吞吐量
使用 PowerShell 或 Azure CLI 并收到“未找到”错误消息。

#### <a name="solution"></a>解决方案：
可以在数据库级别和/或容器级别预配吞吐量。 如果收到“未找到”错误，请尝试读取父数据库资源或子容器资源的吞吐量。

### <a name="invalid-partition-key-and-id-combination"></a>分区键和 ID 组合无效
分区键和 ID 组合无效。

#### <a name="solution"></a>解决方案：
修复导致错误组合的应用程序逻辑。 

### <a name="invalid-character-in-an-item-id"></a>项 ID 中的字符无效
项已插入 Azure Cosmos DB，并且项 ID 中带有[无效字符](/dotnet/api/microsoft.azure.documents.resource.id#remarks)。

#### <a name="solution"></a>解决方案：
将 ID 更改为不包含特殊字符的其他值。 如果不能更改 ID，则可以对 ID 进行 Base64 编码以将特殊字符转义。 Base64 仍然会生成一个包含无效字符“/”的名称，该字符需要替换。

对于已经插入容器中的项，可以使用 RID 值来替换其 ID，而不使用基于名称的引用。
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes).Replace('/', '!');

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>生存时间清除
项已设置[生存时间 (TTL)](./time-to-live.md) 属性。 由于 TTL 属性已过期，项被清除。

#### <a name="solution"></a>解决方案：
更改 TTL 属性以防止清除该项。

### <a name="lazy-indexing"></a>惰性索引编制
[惰性索引编制](../index-policy.md#indexing-mode)未跟进。

#### <a name="solution"></a>解决方案：
等待索引编制跟进或更改索引编制策略。

### <a name="parent-resource-deleted"></a>已删除父资源
项所在的数据库或容器已删除。

#### <a name="solution"></a>解决方案：
1. [还原](../configure-periodic-backup-restore.md#request-restore)父资源或重新创建资源。
1. 创建新资源来替换已删除的资源。

### <a name="7-containercollection-names-are-case-sensitive"></a>7.容器/集合名称区分大小写
容器/集合名称在 Cosmos DB 中区分大小写。

#### <a name="solution"></a>解决方案：
请确保在连接到 Cosmos DB 时使用确切的名称。

## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-dot-net-sdk.md)在使用 Azure Cosmos DB .NET SDK 时遇到的问题。
* 了解 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET v2](performance-tips.md) 的性能准则。
* [诊断和排查](troubleshoot-java-sdk-v4-sql.md)使用 Azure Cosmos DB Java v4 SDK 时遇到的问题。
* 了解 [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) 的性能准则。
