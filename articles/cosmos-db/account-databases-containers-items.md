---
title: Azure Cosmos DB 资源模型
description: 本文介绍 Azure Cosmos DB 资源模型，其中包括 Azure Cosmos 帐户、数据库、容器和项。 还会介绍 Azure Cosmos 帐户中的这些元素的层次结构。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: f714a70a0d9eb971498fc2f9fe648804a59b5136
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110475007"
---
# <a name="azure-cosmos-db-resource-model"></a>Azure Cosmos DB 资源模型
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 是完全托管的平台即服务 (PaaS)。 若要开始使用 Azure Cosmos DB，首先应在所需订阅中的 Azure 资源组中创建一个 Azure Cosmos 帐户，然后在帐户下创建数据库、容器和项。 本文介绍 Azure Cosmos DB 资源模型以及资源模型层次结构中的不同实体。

Azure Cosmos 帐户是全局分配和高可用性的基本单元。 Azure Cosmos 帐户包含唯一的 DNS 名称，可以使用 Azure 门户、Azure CLI 或不同的特定于语言的 SDK 来管理帐户。 有关详细信息，请参阅[如何管理 Azure Cosmos 帐户](how-to-manage-database-account.md)。 要在多个 Azure 区域之间全局分配数据和吞吐量，可随时在帐户中添加和删除 Azure 区域。 可将帐户配置为使用单区域或多个写入区域。 有关详细信息，请参阅[如何在帐户中添加和删除 Azure 区域](how-to-manage-database-account.md)。 可在帐户中配置[默认一致性](consistency-levels.md)级别。

## <a name="elements-in-an-azure-cosmos-account"></a>Azure Cosmos 帐户中的元素

Azure Cosmos 容器是基本的缩放单元。 容器可以提供几乎无限的预配吞吐量 (RU/s) 和存储。 Azure Cosmos DB 使用指定的逻辑分区键以透明方式将容器分区，以弹性缩放预配吞吐量和存储。

目前，最多可在一个 Azure 订阅下创建 50 个 Azure Cosmos 帐户（这是一项软限制，可通过支持请求上调）。 单个 Azure Cosmos 帐户几乎可以管理无限量的数据和预配吞吐量。 若要管理数据和预配吞吐量，可以在帐户下创建一个或多个 Azure Cosmos 数据库，而在该数据库中，可以创建一个或多个容器。 下图显示了 Azure Cosmos 帐户中的元素层次结构：

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Azure Cosmos 帐户的层次结构" border="false":::

在 Azure 订阅下创建帐户后，可通过创建数据库、容器和项来管理帐户中的数据。 

下图显示 Azure Cosmos DB 帐户中不同实体的层次结构：

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos 帐户实体" border="false":::

## <a name="azure-cosmos-databases"></a>Azure Cosmos 数据库

可以在帐户下创建一个或多个 Azure Cosmos 数据库。 数据库类似于命名空间， 数据库是一组 Azure Cosmos 容器的管理单元。 下表显示如何将数据库映射到各种特定于 API 的实体：

| Azure Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 数据库 | 数据库 | 密钥空间 | 数据库 | 数据库 | 不可用 |

> [!NOTE]
> 如果使用表 API 帐户，在创建第一个表时，系统会自动在 Azure Cosmos 帐户中创建默认数据库。

### <a name="operations-on-an-azure-cosmos-database"></a>对 Azure Cosmos 数据库执行的操作

可以使用下表中所述的 Azure Cosmos API 来与 Azure Cosmos 数据库交互：

| 操作 | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
|枚举所有数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | 不可用 | 不可用 |
|读取数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | 不可用 | 不可用 |
|创建新数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | 不可用 | 不可用 |
|更新数据库| 是 | 是 | 是（将数据库映射至密钥空间） | 是 | 不可用 | 不可用 |

## <a name="azure-cosmos-containers"></a>Azure Cosmos 容器

Azure Cosmos 容器是预配的吞吐量和存储的缩放单元。 容器会进行水平分区，然后在多个区域间复制。 添加到容器的项将自动划分为逻辑分区，这些分区基于分区键分布在物理分区中。 容器上的吞吐量均匀分布在物理分区中。 若要详细了解分区和分区键，请参阅[将数据分区](partitioning-overview.md)。 

创建容器时，会在以下某种模式下配置吞吐量：

* **专用预配吞吐量模式**：针对容器预配的吞吐量是专门为该容器保留的，由 SLA 提供支持。 有关详细信息，请参阅[如何对容器预配吞吐量](how-to-provision-container-throughput.md)。

* **共享预配吞吐量模式**：这些容器与同一数据库中的其他容器共享预配吞吐量（不包含已配置专用预配吞吐量的容器）。 换而言之，针对该数据库预配的吞吐量是在所有“共享吞吐量”容器之间共享的。 有关详细信息，请参阅[如何对数据库预配吞吐量](how-to-provision-database-throughput.md)。

> [!NOTE]
> 只有在创建数据库和容器时，才能配置共享吞吐量和专用吞吐量。 若要在创建容器后从专用吞吐量模式切换为共享吞吐量模式（或反之），必须创建一个新容器，并将数据迁移到该容器。 可以使用 Azure Cosmos DB 更改源功能迁移数据。

无论是使用专用还是共享预配吞吐量模式创建容器，Azure Cosmos 容器都可以弹性缩放。

容器是与架构无关的项容器。 容器中的项可以采用任意架构。 例如，可以在同一个容器中放置一个表示人员的项，以及一个表示汽车的项。 默认情况下，添加到容器的所有项会自动编制索引，不需要进行显式的索引或架构管理。 通过在容器上配置的[索引策略](index-overview.md)，可以自定义索引行为。 

可针对容器中的选定项或整个容器设置[生存时间 (TTL)](time-to-live.md)，以从系统中正常清除这些项。 Azure Cosmos DB 会在这些项过期时自动将其删除。 这样还能保证对这些容器执行的查询不会返回固定边界内已过期的项。 有关详细信息，请参阅[对容器配置 TTL](how-to-time-to-live.md)。

可以使用[更改源](change-feed.md)订阅针对容器的每个逻辑分区管理的操作日志。 更改源提供对容器执行的所有更新的日志，以及更新前和更新后的项的映像。 有关详细信息，请参阅[使用更改源生成被动式应用程序](serverless-computing-database.md)。 还可以通过使用容器上的更改源策略来配置更改源的保留期限。

可为容器注册[存储过程、触发器、用户定义的函数 (UDF)](stored-procedures-triggers-udfs.md) 和[合并过程](how-to-manage-conflicts.md)。

可以在 Azure Cosmos 容器上指定一个[唯一键约束](unique-keys.md)。 通过创建唯一键策略，可确保每个逻辑分区键的一个或多个值的唯一性。 如果使用唯一键策略创建容器，则无法创建值与唯一键约束指定的值重复的任何新项或更新的项。 若要了解详细信息，请参阅[唯一键约束](unique-keys.md)。

容器专用于 API 特定的实体，如下表所示：

| Azure Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 容器 | 容器 | 表 | 集合 | Graph | 表 |

> [!NOTE]
> 创建容器时，请确保不创建名称相同但大小写不同的两个容器。 这是因为 Azure 平台的某些部分不区分大小写，这可能会对具有此类名称的容器导致遥测和操作混乱/冲突。

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 容器的属性

Azure Cosmos 容器具备一组系统定义的属性。 根据所用的 API，某些属性可能不会直接公开。 下表介绍了系统定义属性的列表：

| 系统定义的属性 | 由系统生成或用户可配置 | 目的 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | 由系统生成 | 容器的唯一标识符 | 是 | 否 | 否 | 否 | 否 |
|\_etag | 由系统生成 | 用于乐观并发控制的实体标记 | 是 | 否 | 否 | 否 | 否 |
|\_ts | 由系统生成 | 容器上次更新的时间戳 | 是 | 否 | 否 | 否 | 否 |
|\_self | 由系统生成 | 容器的可寻址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 用户可配置 | 用户定义的容器唯一名称 | 是 | 是 | 是 | 是 | 是 |
|indexingPolicy | 用户可配置 | 提供更改索引路径、索引类型和索引模型的功能 | 是 | 否 | 否 | 否 | 是 |
|TimeToLive | 用户可配置 | 提供在设置的时间段后从容器自动删除项的功能 有关详细信息，请参阅[生存时间](time-to-live.md)。 | 是 | 否 | 否 | 否 | 是 |
|changeFeedPolicy | 用户可配置 | 用于读取对容器中的项所做的更改。 有关详细信息，请参阅[更改源](change-feed.md)。 | 是 | 否 | 否 | 否 | 是 |
|uniqueKeyPolicy | 用户可配置 | 用于确保逻辑分区中一个或多个值的唯一性。 有关详细信息，请参阅[唯一键约束](unique-keys.md)。 | 是 | 否 | 否 | 否 | 是 |

### <a name="operations-on-an-azure-cosmos-container"></a>对 Azure Cosmos 容器执行的操作

使用任一 Azure Cosmos API 时，Azure Cosmos 容器支持以下操作：

| 操作 | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 枚举数据库中的容器 | 是 | 是 | 是 | 是 | 不可用 | 不可用 |
| 读取容器 | 是 | 是 | 是 | 是 | 不可用 | 不可用 |
| 创建新容器 | 是 | 是 | 是 | 是 | 不可用 | 不可用 |
| 更新容器 | 是 | 是 | 是 | 是 | 不可用 | 不可用 |
| 删除容器 | 是 | 是 | 是 | 是 | 不可用 | 不可用 |

## <a name="azure-cosmos-items"></a>Azure Cosmos 项

根据所用的 API，Azure Cosmos 项可以代表集合中的文档、表中的行，或者图形中的节点或边缘。 下表显示了 API 特定的实体与 Azure Cosmos 项之间的映射：

| Cosmos 实体 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 项 | 项目 | 行 | 文档 | 节点或边缘 | 项目 |

### <a name="properties-of-an-item"></a>项的属性

每个 Azure Cosmos 项具有以下系统定义的属性。 根据所用的 API，其中的某些属性可能不会直接公开。

| 系统定义的属性 | 由系统生成或用户可配置| 目的 | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | 由系统生成 | 项的唯一标识符 | 是 | 否 | 否 | 否 | 否 |
|\_etag | 由系统生成 | 用于乐观并发控制的实体标记 | 是 | 否 | 否 | 否 | 否 |
|\_ts | 由系统生成 | 项上次更新的时间戳 | 是 | 否 | 否 | 否 | 否 |
|\_self | 由系统生成 | 项的可寻址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 任一个 | 逻辑分区中用户定义的唯一名称。 | 是 | 是 | 是 | 是 | 是 |
|任意用户定义的属性 | 用户定义 | 用户定义的属性以 API 本机表示形式表示（包括 JSON、BSON 和 CQL） | 是 | 是 | 是 | 是 | 是 |

> [!NOTE]
> `id` 属性的唯一性仅在每个逻辑分区中强制实施。 多个文档可以具有相同的 `id` 属性，但具有不同的分区键值。

### <a name="operations-on-items"></a>对项执行的操作

Azure Cosmos 项支持以下操作。 可以使用任一 Azure Cosmos API 来执行这些操作。

| 操作 | Azure CLI | SQL API | Cassandra API | 用于 MongoDB 的 Azure Cosmos DB API | Gremlin API | 表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 插入、替换、删除、Upsert、读取 | 否 | 是 | 是 | 是 | 是 | 是 |

## <a name="next-steps"></a>后续步骤

了解如何管理 Azure Cosmos 帐户，以及了解其他概念：

* [如何管理 Azure Cosmos 帐户](how-to-manage-database-account.md)
* [全球分布](distribute-data-globally.md)
* [一致性级别](consistency-levels.md)
* [Azure Cosmos 帐户的 VNET 服务终结点](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos 帐户的 IP 防火墙](how-to-configure-firewall.md)
* [如何在 Azure Cosmos 帐户中添加和删除 Azure 区域](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
