---
title: 配置和使用 Azure Synapse Link for Azure Cosmos DB
description: 了解如何为 Azure Cosmos DB 帐户启用 Synapse Link，创建启用了分析存储的容器，将 Azure Cosmos 数据库连接到 Synapse 工作区，并运行查询。
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/12/2021
ms.author: rosouz
ms.custom: references_regions, synapse-cosmos-db, devx-track-azurepowershell
ms.openlocfilehash: 116997c8abbad382dc10014fd76e7933f333c113
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123109345"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>配置和使用 Azure Synapse Link for Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse Link for Azure Cosmos DB](synapse-link.md) 是一种云原生混合事务和分析处理 (HTAP) 功能，可用于对 Azure Cosmos DB 中的操作数据运行准实时分析。 Synapse Link 在 Azure Cosmos DB 和 Azure Synapse Analytics 之间建立紧密的无缝集成。

Azure Synapse Link 可用于 Azure Cosmos DB SQL API 容器或 Azure Cosmos DB API for Mongo DB 集合。 使用以下步骤，通过 Azure Synapse Link for Azure Cosmos DB 运行分析查询：

* [为 Azure Cosmos DB 帐户启用 Synapse Link](#enable-synapse-link)
* [创建启用了分析存储的 Azure Cosmos DB 容器](#create-analytical-ttl)
* [可选 - 更新 Azure Cosmos DB 容器的分析存储 TTL](#update-analytical-ttl)
* [将 Azure Cosmos DB 数据库连接到 Synapse 工作区](#connect-to-cosmos-database)
* [使用 Synapse Spark 查询分析存储](#query-analytical-store-spark)
* [使用无服务器 SQL 池查询分析存储](#query-analytical-store-sql-on-demand)
* [使用无服务器 SQL 池在 Power BI 中分析和可视化数据](#analyze-with-powerbi)

你还可以查看 Learn 模块，了解如何[为 Azure Cosmos DB 配置 Azure Synapse Link](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/)。

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>启用 Azure Synapse Link for Azure Cosmos DB 帐户

> [!NOTE]
> 如果要将客户管理的密钥用于 Azure Synapse Link，必须在 Azure Key Vault 访问策略中配置帐户的托管标识，然后才能在你的帐户上启用 Synapse Link。 若要了解详细信息，请参阅如何[使用 Azure Cosmos DB 帐户的托管标识配置客户管理的密钥](how-to-setup-cmk.md#using-managed-identity)一文。

> [!NOTE]
> 如果要对 SQL (CORE) API 帐户使用完全保真架构，则无法使用 Azure 门户来启用 Synapse Link。 在你的帐户中启用 Synapse Link 后便无法更改此选项，必须使用 Azure CLI 或 PowerShell 进行设置。 有关详细信息，请查看[分析存储架构表示文档](analytical-store-introduction.md#schema-representation)。 

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure 帐户](create-sql-api-dotnet.md#create-account)，或选择现有的 Azure Cosmos DB 帐户。

1. 导航到你的 Azure Cosmos DB 帐户，打开“功能”窗格。

1. 从“功能”列表选择“Synapse Link”。

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="查找 Synapse Link 功能":::

1. 接下来，它会提示你在帐户上启用 Synapse Link。 选择“启用”。 此过程可能需要 1 到 5 分钟才能完成。

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="启用 Synapse Link 功能":::

1. 你的帐户现已启用，可以使用 Synapse Link。 接下来，了解如何创建启用了分析存储的容器，以便自动开始将操作数据从事务性存储复制到分析存储。

> [!NOTE]
> 启用 Synapse Link 不会自动启用分析存储。 在 Cosmos DB 帐户上启用 Synapse Link 后，请在创建容器时在容器上启用分析存储，开始将操作数据复制到分析存储。 

### <a name="azure-cli"></a>Azure CLI

以下链接演示了如何使用 Azure CLI 启用 Synapse Link：

* [创建启用 Synapse Link 的新 Azure Cosmos DB 帐户](/cli/azure/cosmosdb#az_cosmosdb_create-optional-parameters)
* [更新现有 Azure Cosmos DB 帐户以启用 Synapse Link](/cli/azure/cosmosdb#az_cosmosdb_update-optional-parameters)

### <a name="powershell"></a>PowerShell

* [创建启用 Synapse Link 的新 Azure Cosmos DB 帐户](/powershell/module/az.cosmosdb/new-azcosmosdbaccount#description)
* [更新现有 Azure Cosmos DB 帐户以启用 Synapse Link](/powershell/module/az.cosmosdb/update-azcosmosdbaccount)


以下链接演示了如何使用 PowerShell 启用 Synapse Link：

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> 使用分析存储创建 Azure Cosmos 容器

创建容器时，可以在 Azure Cosmos 容器上启用分析存储。 可以使用 Azure 门户，或在创建容器期间使用 Azure Cosmos DB SDK 配置 `analyticalTTL` 属性。

> [!NOTE]
> 目前，可以为新容器（在新帐户和现有帐户中）启用分析存储。 可以使用 [Azure Cosmos DB 迁移工具](cosmosdb-migrationchoices.md)将数据从现有容器迁移到新容器。

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)或 [Azure Cosmos DB 资源管理器](https://cosmos.azure.com/)。

1. 导航到你的 Azure Cosmos DB 帐户，打开“数据资源管理器”选项卡。

1. 选择“新容器”，并输入数据库、容器、分区键和吞吐量详细信息的名称。 打开“分析存储”选项。 启用分析存储后，它将创建一个容器，该容器的 `AnalyicalTTL` 属性设置为默认值 -1（无限保留期）。 此分析存储保留所有记录的历史版本。

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="启用 Azure Cosmos 容器的分析存储":::

1. 如果你以前未在此帐户上启用 Synapse Link，系统将提示你执行此操作，因为它是创建启用了分析存储的容器的先决条件。 如果系统提示，请选择“启用 Synapse Link”。 此过程可能需要 1 到 5 分钟才能完成。

1. 选择“确定”，创建启用了分析存储的 Azure Cosmos 容器。

1. 创建容器后，通过单击数据资源管理器中“文档”正下方的“设置”，验证分析存储是否已启用，并检查“分析存储生存时间”选项是否已启用。

### <a name="net-sdk"></a>.NET SDK

以下代码使用 .NET SDK 创建具有分析存储的容器。 将分析 TTL 属性设置为所需的值。 有关允许值的列表，请参阅[分析 TTL 支持的值](analytical-store-introduction.md#analytical-ttl)一文：

```csharp
// Create a container with a partition key, and analytical TTL configured to -1 (infinite retention)
ContainerProperties properties = new ContainerProperties()
{
    Id = "myContainerId",
    PartitionKeyPath = "/id",
    AnalyticalStoreTimeToLiveInSeconds = -1,
};
CosmosClient cosmosClient = new CosmosClient("myConnectionString");
await cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(properties);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

以下代码使用 Java V4 SDK 创建具有分析存储的容器。 将 `AnalyticalStoreTimeToLiveInSeconds` 属性设置为所需的值：

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Python V4 SDK

Python 2.7 和 Azure Cosmos DB SDK 4.1.0 是所需的最低版本，此 SDK 仅与 SQL API 兼容。

第一步是确保至少使用 [Azure Cosmos DB Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 版本 4.1.0：

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
下一步是使用 Azure Cosmos DB Python SDK 创建具有分析存储的容器：

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="azure-cli"></a>Azure CLI

以下链接演示了如何使用 Azure CLI 创建启用分析存储的容器：

* [适用于 Mongo DB 的 Azure Cosmos DB API](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create-examples)
* [Azure Cosmos DB SQL API](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create)

### <a name="powershell"></a>PowerShell

以下链接演示了如何使用 PowerShell 创建启用分析存储的容器：

* [适用于 Mongo DB 的 Azure Cosmos DB API](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection#description)
* [Azure Cosmos DB SQL API](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create)


## <a name="optional---update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> 可选 - 更新分析存储生存时间

使用特定的 TTL 值启用分析存储后，以后你可能想要将其更新为其他有效值。 可以使用 Azure 门户、Azure CLI、PowerShell 或 Cosmos DB SDK 更新值。 有关各种分析 TTL 配置选项的信息，请参阅[分析 TTL 支持的值](analytical-store-introduction.md#analytical-ttl)一文。


### <a name="azure-portal"></a>Azure 门户

如果通过 Azure 门户创建了已启用分析存储的容器，则它将包含默认为 -1 的分析 TTL。 使用以下步骤来更新此值：

1. 登录到 [Azure 门户](https://portal.azure.com/)或 [Azure Cosmos DB 资源管理器](https://cosmos.azure.com/)。
1. 导航到你的 Azure Cosmos DB 帐户，打开“数据资源管理器”选项卡。
1. 选择已启用分析存储的现有容器。 展开容器并修改以下值：
   1. 打开“规模和设置”窗口。
   1. 在“设置”下，找到“分析存储生存时间” 。
   1. 选择“启用(无默认值)”或选择“启用”，然后设置一个 TTL 值。 
   1. 单击“保存”  以保存更改。


### <a name="net-sdk"></a>.NET SDK

下面的代码演示如何使用 .NET SDK 更新分析存储的 TTL：

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

下面的代码演示如何使用 Java V4 SDK 更新分析存储的 TTL：

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

### <a name="python-v4-sdk"></a>Python V4 SDK

当前不支持。


### <a name="azure-cli"></a>Azure CLI

以下链接演示了如何使用 Azure CLI 更新容器分析 TTL：

* [适用于 Mongo DB 的 Azure Cosmos DB API](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_update)
* [Azure Cosmos DB SQL API](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_update)

### <a name="powershell"></a>PowerShell

以下链接演示了如何使用 PowerShell 更新容器分析 TTL：

* [适用于 Mongo DB 的 Azure Cosmos DB API](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollection)
* [Azure Cosmos DB SQL API](/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer)


## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> 连接到 Synapse 工作区

使用[连接到 Azure Synapse Link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) 中的说明，了解如何使用 Azure Synapse Link 从 Azure Synapse Analytics Studio 访问 Azure Cosmos DB 数据库。

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a>使用适用于 Azure Synapse Analytics 的 Apache Spark 查询分析存储

参照[使用 Spark 3 查询 Azure Cosmos DB 分析存储](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark-3.md)一文中的说明，了解如何使用 Synapse Spark 3 进行查询。 这篇文章提供了一些示例，说明如何通过 Synapse 笔势与分析存储交互。 右键单击某个容器时，即可看到这些笔势。 借助笔势，可以快速生成代码，并根据需要进行调整。 它们还适用于只需单击一下即可发现数据的功能。

对于 Spark 2 集成，请参照[使用 Spark 2 查询 Azure Cosmos DB 分析存储](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md)一文中的说明。

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a>在 Azure Synapse Analytics 中使用无服务器 SQL 池查询分析存储

利用无服务器 SQL 池，可以查询和分析启用了 Azure Synapse Link 的 Azure Cosmos DB 容器中的数据。 可以以近乎实时的方式分析数据，而不会影响事务工作负载的性能。 它提供熟悉的 T-SQL 语法，用于从分析存储中查询数据，并且通过 T-SQL 接口提供了到各种 BI 和即席查询工具的集成连接。 若要详细了解，请参阅[使用无服务器 SQL 池查询分析存储](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)一文。

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>使用无服务器 SQL 池在 Power BI 中分析和可视化数据

可以通过 Azure Cosmos DB 的 Synapse Link 创建无服务器 SQL 池数据库和视图。 在此之后，你可以查询 Azure Cosmos 容器，然后在那些视图中使用 Power BI 生成模型，以反映该查询。 对事务工作负载没有任何性能或成本影响，也不存在管理 ETL 管道的复杂性。 可以使用 [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) 或 [import](/power-bi/connect-data/service-dataset-modes-understand#import-mode) 模式。 若要详细了解，请参阅如何使用[无服务器 SQL 池通过 Synapse Link 分析 Azure Cosmos DB](synapse-link-power-bi.md) 一文。


## <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板

[Azure 资源管理器模板](./manage-with-templates.md#azure-cosmos-account-with-analytical-store)会为 SQL API 创建启用了 Synapse Link 的 Azure Cosmos DB 帐户。 此模板在一个区域中创建一个 Core (SQL) API 帐户，其中包含配置了启用分析 TTL 的容器和一个选择使用手动或自动缩放吞吐量的选项。 若要部署此模板，请在自述文件页上单击“部署到 Azure”。

## <a name="getting-started-with-azure-synapse-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a>Azure Synapse Link 入门 - 示例

可以在 [GitHub](https://aka.ms/cosmosdb-synapselink-samples) 上找到有关 Azure Synapse Link 入门的示例。 这些示例展示了物联网和零售场景的端到端解决方案。 也可以在 [MongoDB](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples) 文件夹下的同一存储库中找到与 Azure Cosmos DB API for MongoDB 对应的示例。 

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请参阅下列文档：

* 查看 Learn 模块，了解如何[为 Azure Cosmos DB 配置 Azure Synapse Link](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/)。

* [Azure Cosmos DB 分析存储概述。](analytical-store-introduction.md)

* [有关 Azure Synapse Link for Azure Cosmos 的常见问题。](synapse-link-frequently-asked-questions.yml)

* [Azure Synapse Analytics 中的 Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)。

* [Azure Synapse Analytics 中的无服务器 SQL 池运行时支持](../synapse-analytics/sql/on-demand-workspace-overview.md)。
