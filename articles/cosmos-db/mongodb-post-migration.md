---
title: 使用 Azure Cosmos DB API for MongoDB 进行迁移后的优化步骤
description: 本文档提供从 MongoDB 迁移到 Azure Cosmos DB MongoDB API 后的优化方法。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 05/19/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 2fc8b3a3c3c03eb0df7fe21840becdd0fe24346e
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113359976"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB MongoDB API 时的迁移后优化步骤
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> 在执行迁移后步骤之前，请完整阅读本指南。
>

本 MongoDB 迁移后指南是 MongoDB 迁移系列的一部分。 关键的 MongoDB 迁移步骤包括[迁移前步骤](mongodb-pre-migration.md)、迁移步骤和迁移后步骤，如下所示。

![迁移步骤示意图。](./media/mongodb-pre-migration/overall-migration-steps.png)

## <a name="overview-of-post-migration"></a>迁移后概述

将 MongoDB 数据库中存储的数据迁移到 Azure Cosmos DB 的 API for MongoDB 后，你可以连接到 Azure Cosmos DB 并管理数据。 本指南提供迁移后应考虑的步骤。 有关迁移步骤，请参阅[将 MongoDB 迁移到 Azure Cosmos DB MongoDB API](../dms/tutorial-mongodb-cosmos-db.md) 教程。

按照以下步骤执行迁移后操作

- [优化索引策略](#optimize-the-indexing-policy)
- [为 Azure Cosmos DB API for MongoDB 配置全局分配](#globally-distribute-your-data)
- [设置一致性级别](#set-consistency-level)
- [连接（转换）你的应用程序](#connect-or-cutover-your-application)
- [通过优化实现最优性能](#tune-for-optimal-performance)

> [!NOTE]
> 唯一必需执行的迁移后步骤是将应用程序中的连接字符串更改为指向新的 Azure Cosmos DB 帐户。 所有其他迁移后步骤都是推荐优化步骤，以提高数据层性能。 但是，如果在不执行其他步骤的情况下立即执行应用程序转换，则应用程序立即会有非最优索引和一致性的影响。 具体来说，如果在配置索引之前转换，则应用程序的性价比可能会立即下降。 请注意，这是可以补救的 - [一旦索引得到优化](#optimize-the-indexing-policy)，预计 Azure Cosmos DB 在性价比方面通常会优于目前的解决方案。
>

## <a name="pre-requisites"></a>先决条件

在本指南中，我们假设你使用某种跟踪项目（如电子表格）来维护迁移进度的记录。 如果你尚未这样做，建议你阅读《[迁移前指南](mongodb-pre-migration.md)》，了解有关生成“数据资产迁移电子表格”、发现现有 MongoDB 资源和规划迁移的指导。

## <a name="optimize-the-indexing-policy"></a>优化索引策略

为了优化性价比，建议你逐步浏览数据资产迁移电子表格，并为每种资源设计索引配置。 
1. 实际上我们建议在[迁移前阶段规划索引](mongodb-pre-migration.md#post-migration)。 将列添加到数据资产迁移电子表格以获取索引设置。 
   * 用于 MongoDB 服务器的 Azure Cosmos DB API 版本 3.6 和更高版本仅自动为 _id 字段编制索引。 无法删除此字段。 它会自动强制确保每个分片密钥的 _id 字段的唯一性。 若要为其他字段编制索引，请应用 MongoDB 索引管理命令。 此默认索引编制策略不同于 Azure Cosmos DB SQL API，后者在默认情况下会为所有字段编制索引。

   * 对于用于 MongoDB 服务器的 Azure Cosmos DB API 版本 3.2，默认情况下，在将数据迁移到 Azure Cosmos DB 的过程中，所有数据字段都将自动编制索引。 在许多情况下，此默认索引策略是可接受的。 一般情况下，删除索引可以优化写入请求，而使用默认索引策略（即自动编制索引）可以优化读取请求。

   * Azure Cosmos DB 提供的索引编制功能包括添加复合索引、唯一索引和生存时间 (TTL) 索引。 索引管理接口映射到 createIndex() 命令。 详情请参阅“Azure Cosmos DB 中的索引编制”和“Azure Cosmos DB 的 API for MongoDB 中的索引编制”。
2. 在迁移后应用这些索引设置。
   * [Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db.md)自动迁移具有唯一索引的 MongoDB 集合。 但是，必须在迁移之前创建唯一索引。 如果集合中已包含数据，Azure Cosmos DB 将不支持创建唯一索引。 有关详细信息，请参阅 [Azure Cosmos DB 中的唯一键](unique-keys.md)。

## <a name="globally-distribute-your-data"></a>全局分发数据

Azure Cosmos DB 在全球范围的所有 [Azure 区域](https://azure.microsoft.com/regions/#services)中推出。 
1. 按照[在 Azure Cosmos DB 的 API for MongoDB 上在全球范围内分发数据](tutorial-global-distribution-mongodb.md)文章中的指导在全球范围内分发数据。 为 Azure Cosmos DB 帐户选择默认的一致性级别后，可以关联一个或多个 Azure 区域（具体取决于全局分配需求）。 为了实现高可用性和业务连续性，我们始终建议至少在 2 个区域中运行 Azure Cosmos DB。 可以查看有关 [Azure Cosmos DB 中多区域部署的优化成本](optimize-cost-regions.md)的提示。

## <a name="set-consistency-level"></a>设置一致性级别

Azure Cosmos DB 提供 5 个妥善定义的[一致性级别](consistency-levels.md)。 若要了解 MongoDB 与 Azure Cosmos DB 一致性级别之间的映射，请参阅[一致性级别和 Azure Cosmos DB API](./consistency-levels.md)。 默认一致性级别是会话一致性级别。 更改一致性级别是可选操作，可为应用优化一致性级别。 若要使用 Azure 门户更改一致性级别：

1. 转到“设置”下的“默认一致性”边栏选项卡。 
2. 选择所需的[一致性级别](consistency-levels.md)

大多数用户会将其一致性级别保留为默认的会话一致性设置。 但是，[不同一致性级别的可用性和性能各有利弊](./consistency-levels.md)。

## <a name="connect-or-cutover-your-application"></a>连接或转换你的应用程序

通过转换或连接应用程序的处理，可以在迁移完成后将应用程序切换到使用 Azure Cosmos DB。 请遵循以下步骤进行配置：

1. 在新窗口中，登录到 [Azure 门户](https://www.portal.azure.com/)
2. 在 [Azure 门户](https://www.portal.azure.com/)的左窗格中打开“所有资源”菜单，找到已将数据迁移到的 Azure Cosmos DB 帐户。 
3. 打开“连接字符串”边栏选项卡。  右侧窗格中包含成功连接到帐户所需的全部信息。
4. 使用应用程序配置（或其他相关位置）中的连接信息来反映应用中的 Azure Cosmos DB MongoDB API 连接。
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="屏幕截图显示了连接字符串的设置。":::

有关更多详细信息，请参阅[将 MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md) 页。

## <a name="tune-for-optimal-performance"></a>通过优化实现最优性能

关于[索引编制](#optimize-the-indexing-policy)、[全局分发](#globally-distribute-your-data)和[一致性](#set-consistency-level)的一个便捷事实 - 可以根据需要轻松配置和重新配置所有这些内容。 因此，完成应用程序转换后，建议监视应用程序的性能，并根据需要调整这些设置，以满足应用程序的需求。

## <a name="next-steps"></a>后续步骤

* [将 MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md)
* [使用 Studio 3T 连接到 Azure Cosmos DB 帐户](mongodb-mongochef.md)
* [如何使用 Azure Cosmos DB 的用于 MongoDB 的 API 全局分发读取](mongodb-readpreference.md)
* [使用 Azure Cosmos DB 的用于 MongoDB 的 API 过期数据](mongodb-time-to-live.md)
* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)
* [Azure Cosmos DB 中的索引](index-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)