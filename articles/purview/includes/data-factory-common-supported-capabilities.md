---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 08/25/2021
ms.openlocfilehash: 61f0dc4bf556c992721b5cd7cd2859ef30d8cbb9
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123355443"
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
| SAP 表（连接到 SAP ECC 或 SAP S/4HANA 时） | 是 |
| SQL Server \* | 是 | 
| Teradata \* | 是 |

\* Azure Purview 当前不支持针对世系或扫描的查询或存储过程。世系仅限于表和视图源。

#### <a name="known-limitations-on-copy-activity-lineage"></a>复制活动世系的已知限制

当前，如果使用以下复制活动功能，则尚不支持世系：

- 采用二进制格式将数据复制到 Azure Data Lake Storage Gen1 中。
- 使用 PolyBase 或 COPY 语句将数据复制到 Azure Synapse Analytics 中。
- 二进制、带分隔符的文本、Excel、JSON 和 XML 文件的压缩设置。
- 针对 Azure SQL 数据库、Azure SQL 托管实例、Azure Synapse Analytics、SQL Server 和 SAP 表的源分区选项。
- 针对基于文件的存储的源分区发现选项。
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