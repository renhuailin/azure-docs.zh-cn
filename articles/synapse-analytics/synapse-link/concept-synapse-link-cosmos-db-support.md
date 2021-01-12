---
title: Azure Synapse Link for Azure Cosmos DB（预览版）支持的特性
description: 了解 Synapse Link for Azure Cosmos DB 支持的当前操作列表
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b58474758ac4d26b347dc72d84be401d15a3846b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119809"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Synapse Link for Azure Cosmos DB 支持的功能

本文介绍 Synapse Link for Azure Cosmos DB 当前支持的功能。

## <a name="azure-synapse-support"></a>Azure Synapse 支持

Azure Cosmos DB 中有两种类型的容器：
* HTAP 容器 - 启用了 Synapse Link 的容器。 此容器具有事务存储和分析存储。 
* OLTP 容器-未启用 Synaspe 链接的容器。 此容器只有事务性存储区，没有分析存储区。

> [!IMPORTANT]
> 对于未启用托管虚拟网络的 Synapse 工作区，当前支持 Azure Cosmos DB 的 Azure Synapse 链接。 

无需启用 Synapse 链接即可连接到 Azure Cosmos DB 容器。 在这种情况下，只能读取/写入事务存储区。 下面是 Azure Cosmos DB 的 Synapse 链接中当前支持的功能的列表。 

| 类别              | 描述 |[Apache Spark 池](../sql/on-demand-workspace-overview.md) | [无服务器 SQL 池](../sql/on-demand-workspace-overview.md) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| 运行时支持 |支持 Azure Synapse 运行时访问 Azure Cosmos DB| ✓ | 预览 |
| Azure Cosmos DB API 支持 | 支持 Azure Cosmos DB API 类型 | SQL / MongoDB | SQL / MongoDB |
| **Object**  |对象，例如可以创建一个表，直接指向 Azure Cosmos DB 容器| 数据帧、View、Table | 查看 |
| **读取**    | 可读取 Azure Cosmos DB 容器的类型 | OLTP / HTAP | HTAP  |
| **写入**   | 可以使用 Azure Synapse 运行时将数据写入 Azure Cosmos DB 容器 | 是 | 否 |

* 如果将数据从 Spark 写入 Azure Cosmos DB 容器，则会通过 Azure Cosmos DB 的事务存储进行此过程。 它将通过使用请求单位来影响 Azure Cosmos DB 的事务性能。
* 当前不支持通过外部表的专用 SQL 池集成。
 
## <a name="supported-code-generated-actions-for-spark"></a>支持的 Spark 代码生成的操作

| 手势              | 说明 |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| 加载到数据帧 |加载数据并将其读取到 Spark 数据帧 |✓| ✓ |
| 创建 Spark 表 |创建指向 Azure Cosmos DB 容器的表|✓| ✓ |
| 将数据帧写入容器 |将数据写入容器|✓| ✓ |
| 加载容器中的流式处理数据帧 |使用 Azure Cosmos DB 更改源流式处理数据|✓| ✓ |
| 将流式处理数据帧写入容器 |使用 Azure Cosmos DB 更改源流式处理数据|✓| ✓ |


## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>对无服务器 SQL 池支持的代码生成操作

| 手势              | 说明 |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **浏览数据** |使用熟悉的 T-sql 语法和自动架构推理浏览容器中的数据|X| ✓ |
| **创建视图并生成 BI 报表** |创建一个 SQL 视图，以便通过无服务器 SQL 池直接访问用于 BI 的容器 |X| ✓ |
| **与 Cosmos DB 数据一起联接不同的数据源** | 存储从 Cosmos DB 容器读取数据的结果，以及 Azure Blob 存储中的数据或使用 CETAS 的 Azure Data Lake Storage |X| ✓ |

## <a name="next-steps"></a>后续步骤

* 请参阅如何[连接到 Synapse Link for Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [了解如何通过 Spark 查询 Cosmos DB 分析存储](how-to-query-analytical-store-spark.md)