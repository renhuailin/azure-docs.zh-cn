---
title: 如何配置 Azure Cosmos DB 集成缓存
description: 了解如何配置 Azure Cosmos DB 集成缓存
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: tisande
ms.openlocfilehash: b8c2e27b7023a106815b34538f1cd3dba85354b3
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114667648"
---
# <a name="how-to-configure-the-azure-cosmos-db-integrated-cache-preview"></a>如何配置 Azure Cosmos DB 集成缓存（预览版）
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文介绍如何预配专用网关、配置集成缓存和连接应用程序。 

## <a name="prerequisites"></a>先决条件

- 如果还没有 [Azure 订阅](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 一个使用 Azure Cosmos DB 的现有应用程序。 如果没有，[这里提供了一些示例](https://github.com/AzureCosmosDB/labs)。
- 一个现有的 [Azure Cosmos DB SQL（核心）API 帐户](create-cosmosdb-resources-portal.md)。

## <a name="provision-a-dedicated-gateway-cluster"></a>预配专用网关群集

1. 在 Azure 门户中，导航到 Azure Cosmos DB 帐户，然后选择“专用网关”选项卡。

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-tab.png" alt-text="图中显示如何导航到“专用网关”选项卡" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-tab.png" border="false":::

2. 使用以下详细信息填写“专用网关”表单：

   * **专用网关** - 打开“预配”切换开关。 
   * **SKU** - 选择具有所需计算和内存大小的 SKU。 
   *  **实例数** - 节点数。 出于开发目的，我们建议先选择一个 D4 大小的节点。 根据需要缓存的数据量，可在初始测试后增加节点大小。

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-input.png" alt-text="图中显示用于创建专用网关群集的示例输入设置" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-input.png" border="false":::

3. 选择“保存”，然后等待大约 5-10 分钟，以完成专用网关预配。 预配完成后，将看到以下通知：

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-notification.png" alt-text="图中显示如何检查专用网关预配是否已完成" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-notification.png" border="false":::

## <a name="configuring-the-integrated-cache"></a>配置集成缓存

1. 创建专用网关时，会自动预配集成缓存。 集成缓存将使用专用网关中大约 70% 的内存。 专用网关中剩余的 30% 的内存用于将请求路由到后端分区。

2.  修改应用程序的连接字符串，以使用新的专用网关终结点。

      更新后的专用网关连接字符串位于“密钥”边栏选项卡中：
   
      :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-connection-string.png" alt-text="图中显示了专用网关连接字符串" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-connection-string.png" border="false":::

      所有专用网关连接字符串都遵循相同的模式。 从原始连接字符串中删除 `documents.azure.com` 并将其替换为 `sqlx.cosmos.azure.com`。 专用网关始终具有相同的连接字符串，即使删除并重新预配它也是如此。

      你无需使用同一 Azure Cosmos DB 帐户修改所有应用程序中的连接字符串。 例如，你可能有一个 `CosmosClient` 使用网关模式和专用网关终结点进行连接，而另一个 `CosmosClient` 使用直接模式。 换句话说，添加专用网关并不会影响 Azure Cosmos DB 的现有连接方式。

3. 如果使用 .NET 或 Java SDK，请将连接模式设置为[网关模式](sql-sdk-connection-modes.md#available-connectivity-modes)。 对于 Python 和 Node.js SDK，不强制要求执行此步骤，因为除了网关模式之外，它们没有其他连接选项。

> [!NOTE]
> 如果使用最新的 .NET 或 Java SDK 版本，则默认连接模式为直接模式。 若要使用集成缓存，必须重写此默认值。

如果使用的是 Java SDK，还必须手动将 `CosmosClientBuilder` 中的 [contentResponseOnWriteEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.contentresponseonwriteenabled?view=azure-java-stable&preserve-view=true) 设置为 `true`。 如果使用的是任何其他 SDK，则此值已默认为 `true`，因此无需进行任何更改。

## <a name="adjust-request-consistency"></a>调整请求一致性

请务必将请求一致性调整为“最终”。 否则，请求将始终绕过集成缓存。 要为所有读取操作配置最终一致性，最简单的方法是[在帐户级别设置](consistency-levels.md#configure-the-default-consistency-level)。 你还可在[请求级别](how-to-manage-consistency.md#override-the-default-consistency-level)配置一致性，如果只希望部分读取使用集成缓存，则建议在请求级别配置。

> [!NOTE]
> 如果使用的是 Python SDK，则必须显式设置每个请求的一致性级别。 默认不会自动应用帐户级别的设置。

## <a name="adjust-maxintegratedcachestaleness"></a>调整 MaxIntegratedCacheStaleness

配置 `MaxIntegratedCacheStaleness`，这是愿意容忍过时缓存数据的最大时间。 建议将 `MaxIntegratedCacheStaleness` 设置得尽可能高，因为这会增加可缓存命中重复点读取和查询的可能性。 如果将 `MaxIntegratedCacheStaleness` 设置为 0，则无论一致性级别如何，读取请求都决不会使用集成缓存。 未配置时，默认 `MaxIntegratedCacheStaleness` 为 5 分钟。

**.NET**

```csharp
FeedIterator<Food> myQuery = container.GetItemQueryIterator<Food>(new QueryDefinition("SELECT * FROM c"), requestOptions: new QueryRequestOptions
        {
            ConsistencyLevel = ConsistencyLevel.Eventual,
            DedicatedGatewayRequestOptions = new DedicatedGatewayRequestOptions 
            { 
                MaxIntegratedCacheStaleness = TimeSpan.FromMinutes(30) 
            }
        }
);
```

> [!NOTE]
> 目前，只能使用最新的 [.NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.17.0-preview) 和 [Java](https://mvnrepository.com/artifact/com.azure/azure-cosmos/4.16.0-beta.1) 预览 SDK 来调整 MaxIntegratedCacheStaleness。

## <a name="verify-cache-hits"></a>验证缓存命中数

最后，可重启应用程序并验证重复的点读取或查询的集成缓存命中数。 将 `CosmosClient` 修改为使用专用网关终结点后，所有请求都将通过专用网关进行路由。

为了让读取请求（点读取或查询）利用集成缓存，必须满足以下所有条件：

-   客户端连接到专用网关终结点
-  客户端使用网关模式（Python 和 Node.js SDK 始终使用网关模式）
-   请求的一致性必须设置为“最终”。

> [!NOTE]
> 你对集成缓存有何反馈？ 我们想听一听！ 欢迎直接与 Azure Cosmos DB 工程团队分享反馈：cosmoscachefeedback@microsoft.com


## <a name="next-steps"></a>后续步骤

- [集成缓存的常见问题解答](integrated-cache-faq.md)
- [集成缓存概述](integrated-cache.md)
- [专用网关](dedicated-gateway.md)