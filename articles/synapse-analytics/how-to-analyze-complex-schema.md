---
title: 分析包含数组和嵌套结构的架构
description: 如何使用 Apache Spark 和 SQL 分析数组和嵌套结构
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: 0130379b5c9225329bde1d49c119eaec446d17de
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112618"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中分析复杂数据类型

本文适用于 [Azure Synapse Link for Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md) 中的 Parquet 文件和容器。 可以使用 Spark 或 SQL 来读取或转换包含复杂架构（例如数组或嵌套结构）的数据。 以下示例是使用单个文档完成的，但可以使用 Spark 或 SQL 将文档数轻松扩展到数十亿个。 本文中包含的代码使用 PySpark (Python)。

## <a name="use-case"></a>用例

复杂的数据类型越来越常见，为数据工程师带来了挑战。 分析嵌套的架构和数组可能涉及耗时且复杂的 SQL 查询。 此外，可能很难重命名或强制转换嵌套列数据类型。 而且，在使用深度嵌套的对象时，可能会遇到性能问题。

数据工程师需要了解如何有效地处理复杂的数据类型，使每个人都可以轻松地对其进行访问。 在下面的示例中，你将在 Azure Synapse Analytics 中使用 Spark，通过数据帧读取对象并将其转换为平面结构。 使用 Azure Synapse Analytics 中的 SQL 无服务器模型直接查询此类对象，并以常规表的形式返回这些结果。

## <a name="what-are-arrays-and-nested-structures"></a>什么是数组和嵌套结构？

以下对象来自 [Application Insights](../azure-monitor/app/app-insights-overview.md)。 在此对象中，有嵌套结构和包含嵌套结构的数组。

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>包含数组和嵌套结构的架构的示例
通过命令 `df.printschema` 输出对象的数据帧（简称 **df**）的架构时，会看到以下表示形式：

* 黄色表示嵌套结构。
* 绿色表示具有两个元素的数组。

[![以黄色和绿色进行了突出显示的代码，显示架构源](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

已将 `_rid`、`_ts` 和 `_etag` 添加到系统，因为已将文档引入到 Azure Cosmos DB 的事务性存储中。

前面的数据帧只针对 5 个列和 1 个行计数。 转换后，策展数据帧将有 13 个列和 2 个行（表格格式）。

## <a name="flatten-nested-structures-and-explode-arrays"></a>平展嵌套结构和分解数组

借助 Azure Synapse Analytics 中的 Spark，可以轻松地将嵌套结构转换为列，将数组元素转换为多个行。 使用以下步骤进行实施。

[![流程图，显示进行 Spark 转换的步骤](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>定义用于平展嵌套架构的函数

无需更改即可使用此函数。 使用以下函数在 [PySpark 笔记本](quickstart-apache-spark-notebook.md)中创建单元格：

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

### <a name="use-the-function-to-flatten-the-nested-schema"></a>使用可平展嵌套架构的函数

在此步骤中，你将数据帧 (**df**) 的嵌套架构平展成新的数据帧 (`df_flat`)：

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

display 函数应返回 10 个列和 1 个行。 数组及其嵌套元素仍然存在。

### <a name="transform-the-array"></a>转换数组

这里，你将数据帧 `df_flat` 中的数组 `context_custom_dimensions` 转换成新数据帧 `df_flat_explode`。 在下面的代码中，你还定义要选择哪个列：

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

display 函数应返回 10 个列和 2 个行。 下一步是将嵌套架构与步骤 1 中定义的函数平展在一起。

### <a name="use-the-function-to-flatten-the-nested-schema"></a>使用可平展嵌套架构的函数

最后，使用函数将数据帧 `df_flat_explode` 的嵌套架构平展成新数据帧 `df_flat_explode_flat`：
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

display 函数应显示 13 个列和 2 个行。

数据帧 `df_flat_explode_flat` 的函数 `printSchema` 返回以下结果：

[![显示最终架构的代码](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>直接读取数组和嵌套结构

使用 SQL 的无服务器模型，你可以查询和创建基于此类对象的视图和表。

首先，用户应根据数据的存储方式使用以下分类。 以大写字母显示的所有内容都是特定于你的用例的：

| 批量 | 格式 |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |'Parquet' (ADLSg2)|
| N'endpoint=https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/;account=ACCOUNTNAME;database=DATABASENAME;collection=COLLECTIONNAME;region=REGIONTOQUERY', SECRET='YOURSECRET' |'CosmosDB' (Azure Synapse Link)|


替换每个字段，如下所示：
* 'YOUR BULK ABOVE' 是你连接到的数据源的连接字符串。
* 'YOUR TYPE ABOVE' 是用于连接到源的格式。

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

有两种不同的操作类型：

- 下面的代码行中指示了第一种操作类型，该行定义名为 `contextdataeventTime` 的引用嵌套元素 `Context.Data.eventTime` 的列。 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  该行定义名为 `contextdataeventTime` 的引用嵌套元素 `Context>Data>eventTime` 的列。

- 第二种操作类型使用 `cross apply` 为数组下的每个元素创建新行。 然后，它定义每个嵌套对象。 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  如果数组有 5 个具有 4 个嵌套结构的元素，则 SQL 的无服务器模型会返回 5 个行和 4 个列。 SQL 的无服务器模型可以就地查询，在 2 个行中映射数组，并将所有嵌套结构显示成列。

## <a name="next-steps"></a>后续步骤

* [了解如何通过 Spark 3 查询 Synapse Link for Azure Cosmos DB](./synapse-link/how-to-query-analytical-store-spark-3.md)
* [了解如何通过 Spark 2 查询 Synapse Link for Azure Cosmos DB](./synapse-link/how-to-query-analytical-store-spark.md)
* [查询 Parquet 嵌套类型](./sql/query-parquet-nested-types.md)
