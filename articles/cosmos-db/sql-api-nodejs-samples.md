---
title: 管理 Azure Cosmos 数据库中数据的 Node.js 示例
description: 在 GitHub 上查找用于 Azure Cosmos DB 中常见任务的 Node.js 示例，包括 CRUD 操作。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 243bdf7c3383669b98529622048f14988f0b8ac4
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112239205"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>管理 Azure Cosmos DB 中数据的 Node.js 示例
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V2 SDK 示例](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 示例](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK 示例](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK 示例](sql-api-spring-data-sdk-samples.md)
> * [Node.js 示例](sql-api-nodejs-samples.md)
> * [Python 示例](sql-api-python-samples.md)
> * [Azure 代码示例库](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

对 Azure Cosmos DB 资源执行 CRUD 操作和其他常见操作的示例解决方案包含在 [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) GitHub 存储库中。 本文将提供：

* 指向每个 Node.js 示例项目文件中任务的链接。
* 指向相关的 API 参考内容的链接。

**先决条件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- 可以[激活 Visual Studio 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：Visual Studio 订阅每月提供可用来试用付费版 Azure 服务的信用额度。

[!INCLUDE [cosmos-db-emulator-docdb-api](includes/cosmos-db-emulator-docdb-api.md)]

也需 [JavaScript SDK](sql-api-sdk-node.md)。
   
   > [!NOTE]
   > 每个示例都是独立的，自行对自身进行设置并在完成后自行进行清理。 因此，这些示例对 [Containers.create](/javascript/api/%40azure/cosmos/containers) 发出多个调用。 每次执行完此操作，均会按照正在创建的容器的性能层，向订阅收取使用 1 小时的费用。
   > 
   > 

## <a name="database-examples"></a>数据库示例

[DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) 文件演示如何对数据库执行 CRUD 操作。 若要在运行以下示例之前了解 Azure Cosmos 数据库，请参阅[使用数据库、容器和项](account-databases-containers-items.md)概念性文章。 

| 任务 | API 参考 |
| --- | --- |
| [创建数据库（如果不存在）](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases#createifnotexists-databaserequest--requestoptions-) |
| [列出帐户的数据库](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases#readall-feedoptions-) |
| [按 ID 读取数据库](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database#read-requestoptions-) |
| [删除数据库](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database#delete-requestoptions-) |

## <a name="container-examples"></a>容器示例

[ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) 文件演示如何对容器执行 CRUD 操作。 若要在运行以下示例之前了解 Azure Cosmos 集合，请参阅[使用数据库、容器和项](account-databases-containers-items.md)概念性文章。 

| 任务 | API 参考 |
| --- | --- |
| [创建容器（如果不存在）](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers#createifnotexists-containerrequest--requestoptions-) |
| [列出帐户的容器](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers#readall-feedoptions-) |
| [读取容器定义](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container#read-requestoptions-) |
| [删除容器](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container#delete-requestoptions-) |

## <a name="item-examples"></a>项示例

[ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) 文件演示如何对项执行 CRUD 操作。 若要在运行以下示例之前了解 Azure Cosmos 文档，请参阅[使用数据库、容器和项](account-databases-containers-items.md)概念性文章。 

| 任务 | API 参考 |
| --- | --- |
| [创建项](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items#create-t--requestoptions-) |
| [读取容器中的所有项](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items#readall-feedoptions-) |
| [按 ID 读取项](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item#read-requestoptions-) |
| [仅当项更改时读取项](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [查询文档](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](/javascript/api/%40azure/cosmos/items) |
| [替换项](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](/javascript/api/%40azure/cosmos/item) |
| [使用条件 ETag 检查替换项](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [删除项](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](/javascript/api/%40azure/cosmos/item) |

## <a name="indexing-examples"></a>索引示例

[IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) 文件演示如何管理索引。 若要在运行以下示例之前了解 Azure Cosmos DB 中的索引，请参阅[索引策略](index-policy.md)、[索引类型](index-overview.md#index-types)和[索引路径](index-policy.md#include-exclude-paths)概念文章。 

| 任务 | API 参考 |
| --- | --- |
| [手动为特定项编制索引](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [手动从索引中排除特定项](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [从索引中排除路径](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](/javascript/api/%40azure/cosmos/indexingpolicy#excludedpaths) |
| [在字符串路径上创建范围索引](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](/javascript/api/%40azure/cosmos/indexkind)、[IndexingPolicy](/javascript/api/%40azure/cosmos/indexingpolicy)、[Items.query](/javascript/api/%40azure/cosmos/items) |
| [使用默认 indexPolicy 创建容器，然后对其进行联机更新](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](/javascript/api/%40azure/cosmos/containers)

## <a name="server-side-programming-examples"></a>服务器端编程示例

[ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) 项目的 [index.ts](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) 文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的服务器端编程，请参阅[存储过程、触发器和用户定义函数](stored-procedures-triggers-udfs.md)概念性文章。 

| 任务 | API 参考 |
| --- | --- |
| [创建存储过程](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](/javascript/api/%40azure/cosmos/storedprocedures) |
| [执行存储过程](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](/javascript/api/%40azure/cosmos/storedprocedure) |

有关服务器端编程的详细信息，请参阅 [Azure Cosmos DB 服务器端编程：存储过程、数据库触发器和 UDF](stored-procedures-triggers-udfs.md)。
