---
title: 故障排查：使用无服务器 SQL 池从 CSV 或 PARQUET 文件读取 UTF-8 文本
description: 在 Azure Synapse Analytics 中使用无服务器 SQL 池从 CSV 或 PARQUET 文件读取 UTF-8 文本
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 12/03/2020
ms.openlocfilehash: 70ce3c82790db0296d5359b5db2e6a323306c309
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576411"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>故障排查：在 Azure Synapse Analytics 中使用无服务器 SQL 池从 CSV 或 Parquet 文件读取 UTF-8 文本

本文提供关于在 Azure Synapse Analytics 中使用无服务器 SQL 池从 CSV 或 Parquet 文件读取 UTF-8 文本的故障排查步骤。

使用无服务器 SQL 池从 CSV 或 PARQUET 文件读取 UTF-8 文本时，如果查询返回带有非 UTF8 排序规则的 VARCHAR 列，则会错误地转换某些特殊字符，如 ü 和 ö。 这是 SQL Server 和 Azure SQL 中的已知问题。 非 UTF8 排序规则是 Synapse SQL 中的默认排序规则，因此客户查询将受到影响。 使用标准英文字符和某些扩展拉丁字符子集的客户可能不会注意到转换错误。 [始终使用 UTF-8 排序规则在无服务器 SQL 池中读取 UTF-8 文本](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)中更详细地介绍了错误转换

## <a name="workaround"></a>解决方法

此问题的解决方法是在从 CSV 或 PARQUET 文件读取 UTF-8 文本时，始终使用 UTF-8 排序规则。

- 在许多情况下，只需在数据库上设置 UTF8 排序规则（元数据操作）。

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- 可以针对 OPENROWSET 或外部表中的 VARCHAR 列显式定义排序规则：

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- 如果未针对读取 UTF8 数据的外部表指定 UTF8 排序规则，则需要重新创建受影响的外部表，并针对 VARCHAR 列设置 UTF8 排序规则（元数据操作）。


## <a name="next-steps"></a>后续步骤

* [使用 Synapse SQL 查询 Parquet 文件](../sql/query-parquet-files.md)
* [使用 Synapse SQL 查询 CSV 文件](../sql/query-single-csv-file.md)
* [Synapse SQL 提供的 CETAS](../sql/develop-tables-cetas.md)
* [快速入门：使用无服务器 SQL 池](../quickstart-sql-on-demand.md)
