---
title: 如何在 Azure Cosmos DB 中配置多区域写入
description: 了解如何在 Azure Cosmos DB 中使用不同 SDK 为应用程序配置多区域写入。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, "seo-nov-2020"
ms.openlocfilehash: 66f2a8cee24c1e7e26a40faecabbaf2b3e0c78d6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113383"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>在使用 Azure Cosmos DB 的应用程序配置多区域写入
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

创建启用了多个写入区域的帐户后，必须在应用程序中对 Cosmos 客户端的 ConnectionPolicy 进行两处更改，以启用 Azure Cosmos DB 中的多区域写入功能。 在 ConnectionPolicy 中，将 UseMultipleWriteLocations 设置为 true，并将部署应用程序的区域的名称传递给 ApplicationRegion。 这将根据传入位置的地理接近性填充 PreferredLocations 属性。 如果稍后将新区域添加到帐户中，则无需更新或重新部署应用程序，它将自动检测距离较近的区域，并在发生区域事件时自动定位到该区域。

> [!Note]
> 最初配置有单个写入区域的 Cosmos 帐户可以配置为多个写入区域且停机时间为零。 若要了解详细信息，请参阅[配置多个写入区域](../how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="azure-portal"></a><a id="portal"></a> Azure 门户

若要从 Azure 门户启用多区域写入，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到 Azure Cosmos 帐户，然后从菜单中打开“全局复制数据”窗格。

1. 在“多区域写入”选项下，选择“启用”。 它会自动将现有区域添加到读取和写入区域。

1. 可以通过选择地图上的图标或选择“添加区域”按钮来添加其他区域。 你添加的所有区域都会启用读取和写入功能。

1. 更新区域列表后，请选择“保存”以应用所做的更改。

   :::image type="content" source="./media/how-to-multi-master/enable-multi-region-writes.png" alt-text="使用 Azure 门户启用多区域写入的屏幕截图" lightbox="./media/how-to-multi-master/enable-multi-region-writes.png":::

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

若要在应用程序中启用多区域写入，请将 `UseMultipleWriteLocations` 设置为 `true`。 此外，将 `SetCurrentLocation` 设置为在其中部署应用程序并复制 Azure Cosmos DB 的区域：

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

若要在应用程序中启用多区域写入，请将 `ApplicationRegion` 设置为在其中部署应用程序并复制 Cosmos DB 的区域：

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

（可选）可以使用 `CosmosClientBuilder` 和 `WithApplicationRegion` 来获得相同的结果：

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> Java V4 SDK

若要在应用程序中启用多区域写入，请在客户端生成器中调用 `.multipleWriteRegionsEnabled(true)` 和 `.preferredRegions(preferredRegions)`，其中 `preferredRegions` 是包含一个元素的 `List`，即正在部署应用程序和复制 Cosmos DB 的区域：

# <a name="async"></a>[异步](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 异步 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Sync](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 同步 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a> Async Java V2 SDK

Java V2 SDK 使用 Maven [com.microsoft.azure::azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)。 若要在应用程序中启用多区域写入，请设置 `policy.setUsingMultipleWriteLocations(true)`，并将 `policy.setPreferredLocations` 设置为在其中部署应用程序并复制 Cosmos DB 的区域：

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js、JavaScript 和 TypeScript SDK

若要在应用程序中启用多区域写入，请将 `connectionPolicy.UseMultipleWriteLocations` 设置为 `true`。 此外，将 `connectionPolicy.PreferredLocations` 设置为在其中部署应用程序并复制 Cosmos DB 的区域：

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

若要在应用程序中启用多区域写入，请将 `connection_policy.UseMultipleWriteLocations` 设置为 `true`。 此外，将 `connection_policy.PreferredLocations` 设置为在其中部署应用程序并复制 Cosmos DB 的区域。

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>后续步骤

请阅读以下文章：

* [使用会话令牌在 Azure Cosmos DB 中管理一致性](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB 中的冲突类型和解决策略](../conflict-resolution-policies.md)
* [Azure Cosmos DB 中的高可用性](../high-availability.md)
* [Azure Cosmos DB 中的一致性级别](../consistency-levels.md)
* [在 Azure Cosmos DB 中选择适当的一致性级别](../consistency-levels.md)
* [Azure Cosmos DB 中的一致性、可用性和性能权衡](../consistency-levels.md)
* [各种一致性级别的可用性和性能权衡](../consistency-levels.md)
* [全局缩放预配的吞吐量](../request-units.md)
* [全球分布：揭秘](../global-dist-under-the-hood.md)