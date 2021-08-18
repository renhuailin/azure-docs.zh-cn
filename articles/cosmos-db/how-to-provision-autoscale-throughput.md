---
title: 在 Azure Cosmos DB SQL API 中预配自动缩放吞吐量
description: 了解如何使用 Azure 门户、CLI、PowerShell 和各种其他 SDK 在 Azure Cosmos DB SQL API 中预配容器和数据库级别的自动缩放吞吐量。
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/18/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 95f201a7809184b511c3187cf3fcf5fadd233168
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727030"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db---sql-api"></a>在 Azure Cosmos DB SQL API 中的数据库或容器上预配自动缩放吞吐量
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文介绍如何在 Azure Cosmos DB SQL API 中的数据库或容器（集合、图或表）上预配自动缩放吞吐量。 可以为单个容器启用自动缩放，也可以为某个数据库预配自动缩放吞吐量，然后在该数据库中的所有容器之间共享此吞吐量。

如果你使用其他 API，请参阅[用于 MongoDB 的 API](mongodb/how-to-provision-throughput-mongodb.md)、[Cassandra API](cassandra/how-to-provision-throughput-cassandra.md)、[Gremlin API](how-to-provision-throughput-gremlin.md) 文章来预配吞吐量。

## <a name="azure-portal"></a>Azure 门户

### <a name="create-new-database-or-container-with-autoscale"></a>创建支持自动缩放的新数据库或容器

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure Cosmos DB 资源管理器](https://cosmos.azure.com/)。

1. 导航到你的 Azure Cosmos DB 帐户，打开“数据资源管理器”选项卡。

1. 选择“新建容器”。 为你的数据库、容器输入一个名称并输入分区键。 在数据库或容器吞吐量下选择“自动缩放”选项，并设置希望数据库或容器缩放到的[最大吞吐量（RU/秒）](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works)。

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="创建容器并配置自动缩放预配吞吐量":::

1. 选择“确定”。

若要在共享吞吐量数据库上预配自动缩放，请在创建新数据库时选择“预配数据库吞吐量”选项。 

### <a name="enable-autoscale-on-existing-database-or-container"></a>在现有的数据库或容器上启用自动缩放

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure Cosmos DB 资源管理器](https://cosmos.azure.com/)。

1. 导航到你的 Azure Cosmos DB 帐户，打开“数据资源管理器”选项卡。

1. 为你的容器选择“缩放和设置”，或者为你的数据库选择“缩放”。 

1. 在“缩放”下，依次选择“自动缩放”选项、“保存”。  

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="在现有容器上启用自动缩放":::

> [!NOTE]
> 在现有数据库或容器上启用自动缩放时，最大 RU/秒的起始值由系统根据当前手动预配的吞吐量设置和存储确定。 在操作完成后，你可以根据需要更改最大 RU/秒。 [了解详细信息。](autoscale-faq.yml#how-does-the-migration-between-autoscale-and-standard--manual--provisioned-throughput-work-) 

## <a name="azure-cosmos-db-net-v3-sdk"></a>Azure Cosmos DB .NET V3 SDK

可以使用适用于 SQL API 的 Azure Cosmos DB .NET SDK [3.9 或更高版本](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)来管理自动缩放资源。 

> [!IMPORTANT]
> 可以使用该 .NET SDK 创建新的自动缩放资源。 该 SDK 不支持在自动缩放与标准（手动）吞吐量之间迁移。 目前只有 [Azure 门户](#enable-autoscale-on-existing-database-or-container)、[CLI](#azure-cli) 和 [PowerShell](#azure-powershell) 支持迁移方案。

### <a name="create-database-with-shared-throughput"></a>创建具有共享吞吐量的数据库

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>创建具有专用吞吐量的容器

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>读取当前吞吐量（RU/秒）

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>更改自动缩放最大吞吐量（RU/秒）

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk"></a>Azure Cosmos DB Java V4 SDK

可以使用适用于 SQL API 的 Azure Cosmos DB Java SDK [4.0 或更高版本](https://mvnrepository.com/artifact/com.azure/azure-cosmos)来管理自动缩放资源。

> [!IMPORTANT]
> 可以使用该 Java SDK 创建新的自动缩放资源。 该 SDK 不支持在自动缩放与标准（手动）吞吐量之间迁移。 目前只有 [Azure 门户](#enable-autoscale-on-existing-database-or-container)、[CLI](#azure-cli) 和 [PowerShell](#azure-powershell) 支持迁移方案。
### <a name="create-database-with-shared-throughput"></a>创建具有共享吞吐量的数据库

# <a name="async"></a>[异步](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

# <a name="sync"></a>[Sync](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>创建具有专用吞吐量的容器

# <a name="async"></a>[异步](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

# <a name="sync"></a>[Sync](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>读取当前吞吐量（RU/秒）

# <a name="async"></a>[异步](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

# <a name="sync"></a>[Sync](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>更改自动缩放最大吞吐量（RU/秒）

# <a name="async"></a>[异步](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

# <a name="sync"></a>[Sync](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="azure-resource-manager"></a>Azure 资源管理器

Azure 资源管理器模板可用于在新数据库或容器级别的资源上为所有 Azure Cosmos DB API 预配自动缩放吞吐量。 有关示例，请参阅 [Azure Cosmos DB 的 Azure 资源管理器模板](./templates-samples-sql.md)。 按照设计，Azure 资源管理器模板不能用于在现有资源上进行预配吞吐量和自动缩放吞吐量之间的迁移。 

## <a name="azure-cli"></a>Azure CLI

Azure CLI 可用于为所有 Azure Cosmos DB API 在新数据库或容器级资源上预配自动缩放吞吐量，或在现有资源上启用自动缩放。 有关示例，请参阅[用于 Azure Cosmos DB 的 Azure CLI 示例](cli-samples.md)。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 可用于在新数据库或容器级别的资源上为所有 Azure Cosmos DB API 预配自动缩放吞吐量，或在现有资源上启用自动缩放。 有关示例，请参阅[适用于 Azure Cosmos DB 的 Azure PowerShell 示例](powershell-samples.md)。

## <a name="next-steps"></a>后续步骤

* 了解[使用自动缩放预配吞吐量的优势](provision-throughput-autoscale.md#benefits-of-autoscale)。
* 了解如何[在手动与自动缩放吞吐量之间进行选择](how-to-choose-offer.md)。
* 查看[自动缩放常见问题解答](autoscale-faq.yml)。
