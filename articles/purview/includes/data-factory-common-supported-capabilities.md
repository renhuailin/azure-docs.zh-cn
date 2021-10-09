---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 09/27/2021
ms.openlocfilehash: 8c4baccdbfb0f6162e666d6901e0080202a3b7e9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212320"
---
### <a name="copy-activity-support"></a>复制活动支持

| 数据存储 | 支持 | 
| ------------------- | ------------------- | 
| Azure Blob 存储 | 是 |
| Azure 认知搜索 | 是 | 
| Azure Cosmos DB (SQL API) \* | 是 | 
| Azure Cosmos DB 的用于 MongoDB 的 API \* | 是 |
| Azure 数据资源管理器 \* | 是 | 
| Azure Data Lake Storage Gen1 | 是 | 
| Azure Data Lake Storage Gen2 | 是 | 
| Azure Database for Maria DB \* | 是 | 
| Azure Database for MySQL \* | 是 | 
| Azure Database for PostgreSQL \* | 是 |
| Azure 文件 | 是 | 
| Azure SQL 数据库 \* | 是 | 
| Azure SQL 托管实例 \* | 是 | 
| Azure Synapse Analytics \* | 是 | 
| Azure 表存储 | 是 |
| Amazon S3 | 是 | 
| Hive \* | 是 | 
| Oracle \* | 是 |
| SAP 表（连接到 SAP ECC 或 SAP S/4HANA 时） | 是 |
| SQL Server \* | 是 | 
| Teradata \* | 是 |

\* Azure Purview 当前不支持针对世系或扫描的查询或存储过程。世系仅限于表和视图源。

如果使用自承载集成运行时，请注意具有以下世系支持的最小版本：
- 从 Oracle 复制数据：版本 5.10 或更高版本
- 通过 COPY 命令或 PolyBase 将数据复制到 Azure Synapse Analytics：版本 5.10 或更高版本

#### <a name="limitations-on-copy-activity-lineage"></a>复制活动世系的限制

当前，如果使用以下复制活动功能，则尚不支持世系：

- 采用二进制格式将数据复制到 Azure Data Lake Storage Gen1 中。
- 二进制、带分隔符的文本、Excel、JSON 和 XML 文件的压缩设置。
- 针对 Azure SQL 数据库、Azure SQL 托管实例、Azure Synapse Analytics、SQL Server 和 SAP 表的源分区选项。
- 将数据复制到基于文件的接收器，该接收器具有每个文件的最大行数设置。

除世系外，还会报告以下连接器的数据资产架构（“资产”->“架构”选项卡中会显示）：

- Azure Blob、Azure 文件存储、ADLS Gen1、ADLS Gen2 和 Amazon S3 上的 CSV 和 Parquet 文件
- Azure 数据资源管理器、Azure SQL 数据库、Azure SQL 托管实例、Azure Synapse Analytics、SQL Server、Teradata

### <a name="data-flow-support"></a>数据流支持

| 数据存储 | 支持 |
| ------------------- | ------------------- | 
| Azure Blob 存储 | 是 |
| Azure Cosmos DB (SQL API) \* | 是 | 
| Azure Data Lake Storage Gen1 | 是 |
| Azure Data Lake Storage Gen2 | 是 |
| Azure Database for MySQL \* | 是 | 
| Azure Database for PostgreSQL \* | 是 |
| Azure SQL 数据库 \* | 是 |
| Azure SQL 托管实例 \* | 是 | 
| Azure Synapse Analytics \* | 是 |

\* Azure Purview 当前不支持针对世系或扫描的查询或存储过程。世系仅限于表和视图源。

#### <a name="limitations-on-data-flow-lineage"></a>数据流世系的限制

目前，数据流世系不与 Purview [资源集](../concept-resource-sets.md)集成。
