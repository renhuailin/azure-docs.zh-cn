---
title: Synapse Link for Azure Cosmos DB 支持的功能
description: 了解 Synapse Link for Azure Cosmos DB 支持的当前操作列表
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 06/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 8400a479b45770570c43ec906a192bf4f05a71a0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123110389"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Synapse Link for Azure Cosmos DB 支持的功能

本文介绍 Synapse Link for Azure Cosmos DB 当前支持的功能。

## <a name="azure-synapse-support"></a>Azure Synapse 支持

Azure Cosmos DB 中有两种类型的容器：
* HTAP 容器 - 启用了 Synapse Link 的容器。 此容器具有事务存储和分析存储。 
* OLTP 容器 - 未启用 Synaspe Link 的容器。 此容器仅具有事务存储，不具备分析存储。

可以在不启用 Synapse Link 的情况下连接到 Azure Cosmos DB 容器。 在这种情况下，只能读取/写入事务存储。 以下是 Synapse Link for Azure Cosmos DB 当前支持的功能的列表。 

| 类别              | 说明 |[Apache Spark 池](../sql/on-demand-workspace-overview.md) | [无服务器 SQL 池](../sql/on-demand-workspace-overview.md) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| 运行时支持 |支持 Azure Synapse 运行时访问 Azure Cosmos DB| ✓ | ✓ |
| Azure Cosmos DB API 支持 | 支持 Azure Cosmos DB API 类型 | SQL / MongoDB | SQL / MongoDB |
| **Object**  |对象，例如可以创建一个表，直接指向 Azure Cosmos DB 容器| 数据帧、视图、表 | 查看 |
| **读取**    | 可读取的 Azure Cosmos DB 容器的类型 | OLTP / HTAP | HTAP  |
| **写入**   | 是否可以使用 Azure Synapse 运行时将数据写入 Azure Cosmos DB 容器 | 是 | 否 |

* 如果从 Spark 将数据写入 Azure Cosmos DB 容器中，此过程会通过 Azure Cosmos DB 的事务存储进行， 并会通过消耗请求单位来影响 Azure Cosmos DB 的事务性能。
* 目前不支持通过外部表进行专用 SQL 池集成。
 
## <a name="supported-code-generated-actions-for-spark"></a>支持的 Spark 代码生成的操作

| 手势              | 说明 |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| 加载到数据帧 |加载数据并将其读取到 Spark 数据帧 |✓| ✓ |
| 创建 Spark 表 |创建指向 Azure Cosmos DB 容器的表|✓| ✓ |
| 将数据帧写入容器 |将数据写入容器|✓| ✓ |
| 加载容器中的流式处理数据帧 |使用 Azure Cosmos DB 更改源流式处理数据|✓| ✓ |
| 将流式处理数据帧写入容器 |使用 Azure Cosmos DB 更改源流式处理数据|✓| ✓ |

## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>无服务器 SQL 池支持的代码生成操作

| 手势              | 说明 |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **浏览数据** |使用熟悉的 T-SQL 语法和自动架构推理浏览容器中的数据|X| ✓ |
| 创建视图并生成 BI 报表 |创建 SQL 视图，以便通过无服务器 SQL 池直接访问 BI 容器 |X| ✓ |
| **将不同数据源与 Cosmos DB 数据联接起来** | 使用 CETAS 将从 Cosmos DB 容器读取数据的查询结果与 Azure Blob 存储或 Azure Data Lake Storage 中的数据一起存储 |X| ✓ |

## <a name="next-steps"></a>后续步骤

* 请参阅如何[连接到 Synapse Link for Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [了解如何使用 Spark 3 查询 Cosmos DB 分析存储](how-to-query-analytical-store-spark-3.md)
* [了解如何使用 Spark 2 查询 Cosmos DB 分析存储](how-to-query-analytical-store-spark.md)
