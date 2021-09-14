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
ms.openlocfilehash: 642024d9554b51bc60df90cf3d5a7bdd799440b5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432083"
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

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Apache Spark 数据类型到 SQL 数据类型的映射

| Spark 数据类型 | SQL 数据类型 | 注释 |
|---|---|---|
| `LongType`, `long`, `bigint`                | `bigint`              | Spark：LongType 表示 8 字节带符号整数。 [引用](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql) |
| `BooleanType`, `boolean`                    | `bit` (Parquet), `varchar(6)` (CSV)  | |
| `DecimalType`, `decimal`, `dec`, `numeric`  | `decimal`             | Spark：DecimalType 表示任意精度的带符号十进制数。 在内部由 java.math.BigDecimal 提供支持。 BigDecimal 由一个任意精度的未标度化整数值和一个 32 位整数标度构成。 <br> SQL：固定精度和标度数目。 使用最大精度时，有效值的范围为 - 10^38 +1 到 10^38 - 1。 decimal 的 ISO 同义词为 dec 和 dec(p,s)       。 numeric  在功能上完全等同于 decimal  。 [引用](/sql/t-sql/data-types/decimal-and-numeric-transact-sql]) |
| `IntegerType`, `Integer`, `int`             | `int`                 | Spark：IntegerType 表示 4 字节带符号整数。 [引用](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ByteType`, `Byte`, `tinyint`               | `smallint`            | Spark：ByteType 表示 1 字节带符号整数 [-128 到 127]，ShortType 表示 2 字节带符号整数 [-32768 到 32767]。 <br> SQL：Tinyint 表示 1 字节带符号整数 [0, 255]，smallint 表示 2 字节带符号整数 [-32768, 32767]。 [引用](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ShortType`, `Short`, `smallint`            | `smallint`            | 同上。 |
| `DoubleType`, `Double`                      | `float`               | Spark：DoubleType 表示 8 字节双精度浮点数。 对于 SQL，[访问此页面](/sql/t-sql/data-types/float-and-real-transact-sql)。|
| `FloatType`, `float`, `real`                | `real`                | Spark：FloatType 表示 4 字节双精度浮点数。 对于 SQL，[访问此页面](/sql/t-sql/data-types/float-and-real-transact-sql)。|
| `DateType`, `date`                          | `date`                | Spark：DateType 表示包含字段年、月和日的值，不带时区。|
| `TimestampType`, `timestamp`                | `datetime2`           | Spark：TimestampType 表示包含字段年、月、日、小时、分钟和秒的值以及会话本地时区。 时间戳值表示绝对时间点。
| `char`                                      | `char`                |
| `StringType`, `String`, `varchar`           | `Varchar(n)`          | Spark：StringType 表示字符串值。 VarcharType(n) 是 StringType 的变体，它有长度限制。 如果输入字符串超过长度限制，数据写入将失败。 此类型只能用于表架构，不能用于函数/运算符。<br> CharType(n) 是固定长度的 VarcharType(n) 的变体 。 读取 CharType(n) 类型的列始终返回长度为 n 的字符串值。 字符型列比较会将较短的列填充到较长的列。 <br> SQL：在 Varchar(n) 中，n 最大为 8000，如果是分区列，n 最大为 2048。 <br> 将其与排序规则 `Latin1_General_100_BIN2_UTF8` 一起使用。 |
| `BinaryType`, `binary`                      | `varbinary(n)`        | SQL：在 Varbinary(n) 中，n 最大为 8000，如果是分区列，n 最大为 2048。 |
| `array`, `map`, `struct`                    | `varchar(max)`        | SQL：使用排序规则 `Latin1_General_100_BIN2_UTF8` 序列化为 JSON |

\* 数据库级排序规则为 Latin1_General_100_CI_AS_SC_UTF8 \* 字符串列级排序规则为 Latin1_General_100_BIN2_UTF8

\** ArrayType、MapType 和 StructType 都表示为 JSON 格式。

## <a name="next-steps"></a>后续步骤

转到[存储访问控制](develop-storage-files-storage-access-control.md)一文，详细了解存储访问控制。
