---
title: 在无服务器 SQL 池中同步 Apache Spark 外部表定义
description: 概述如何使用无服务器 SQL 池查询 Spark 表
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c7c2123b84aa6e3da362df9ce47d1e7780e8a856
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441753"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>在无服务器 SQL 池中同步 Apache Spark for Azure Synapse 外部表定义

无服务器 SQL 池可以自动从 Apache Spark 同步元数据。 将为无服务器 Apache Spark 池中存在的每个数据库创建无服务器 SQL 池数据库。 

对于每个基于 Parquet 或 CSV 且位于 Azure 存储的 Spark 外部表，都将在无服务器 SQL 池数据库中创建一个外部表。 因此，在关闭 Spark 池的情况下，仍可从无服务器 SQL 池查询 Spark 外部表。

在 Spark 中对表进行分区时，存储中的文件将按文件夹进行组织。 无服务器 SQL 池会使用分区元数据，仅以相关文件夹和文件作为查询目标。

将为 Azure Synapse 工作区中预配的每个无服务器 Apache Spark 池自动配置元数据同步。 可以立即开始查询 Spark 外部表。

对于每个位于 Azure 存储的 Spark Parquet 或 CSV 外部表，都将通过与无服务器 SQL 池数据库对应的 dbo 架构中的一个外部表来表示。 

对于 Spark 外部表查询，请运行以外部 [spark_table] 为目标的查询。 在运行下面的示例之前，对于文件所在的存储帐户，请确保拥有正确的[存储帐户访问权限](develop-storage-files-storage-access-control.md)。

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> 为列添加、删除或更改 Spark 外部表命令不会在无服务器 SQL 池的外部表中反映出来。

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Apache Spark 数据类型到 SQL 数据类型的映射

| Spark 数据类型 | SQL 数据类型               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| Short Type      | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | FLOAT                       |
| DecimalType     | Decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)\*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)\* (into JSON)\** |
| MapType         | varchar(max)\* (into JSON)\** |
| StructType      | varchar(max)\* (into JSON)\** |

\* 使用的排序规则为 Latin1_General_100_BIN2_UTF8。

\** ArrayType、MapType 和 StructType 都表示为 JSON 格式。



## <a name="next-steps"></a>后续步骤

转到[存储访问控制](develop-storage-files-storage-access-control.md)一文，详细了解存储访问控制。
