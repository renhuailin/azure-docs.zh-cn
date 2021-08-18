---
title: Azure 数据资源管理器 (Kusto)
description: 本文介绍如何使用连接器在 Azure 数据资源管理器 (Kusto) 与无服务器 Apache Spark 池之间移动数据。
services: synapse-analytics
ms.author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/19/2020
ms.reviewer: ManojRaheja
author: midesa
ms.openlocfilehash: 82310e14b3eac25be038a7748d1992daef72d83f
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516668"
---
# <a name="azure-data-explorer-kusto-connector-for-apache-spark"></a>适用于 Apache Spark 的 Azure 数据资源管理器 (Kusto) 连接器
适用于 Apache Spark 的 Azure 数据资源管理器 (Kusto) 连接器旨在有效地在 Kusto 群集和 Spark 之间传输数据。 此连接器在 Python、Java 和 .NET 中可用。 它内置于 Azure Synapse Apache Spark 2.4 运行时。

## <a name="authentication"></a>身份验证
使用 Azure Synapse Notebooks 或 Apache Spark 作业定义时，系统之间的身份验证与链接服务无缝连接。 令牌服务与 Azure Active Directory 进行连接以获取在访问 Kusto 群集时使用的安全令牌。

对于 Azure Synapse Pipelines，身份验证将使用服务主体名称。 目前，Azure 数据资源管理器连接器不支持托管标识。

## <a name="prerequisites"></a>先决条件 
  - [连接到 Azure 数据资源管理器](../../quickstart-connect-azure-data-explorer.md)：需要设置链接服务以连接到现有的 Kusto 群集。

## <a name="limitations"></a>限制
  - 目前仅 Azure Synapse Apache Spark 2.4 运行时支持 Azure 数据资源管理器 (Kusto) 连接器。
  - 只能使用服务主体名称来配置 Azure 数据资源管理器链接服务。
  - 在 Azure Synapse Notebooks 或 Apache Spark 作业定义中，Azure 数据资源管理器连接器将使用 AAD 直通连接到 Kusto 群集。


## <a name="use-the-azure-data-explorer-kusto-connector"></a>使用 Azure 数据资源管理器 (Kusto) 连接器
以下部分提供了一个简单的示例，说明如何将数据写入 Kusto 表以及如何从 Kusto 表读取数据。 有关详细文档，请参阅 [Azure 数据资源管理器 (Kusto) 连接器项目](https://github.com/Azure/azure-kusto-spark)。 

### <a name="read-data"></a>读取数据

```python
kustoDf  = spark.read \
            .format("com.microsoft.kusto.spark.synapse.datasource") \
            .option("spark.synapse.linkedService", "<link service name>") \
            .option("kustoDatabase", "<Database name>") \
            .option("kustoQuery", "<KQL Query>") \
            .load()

display(kustoDf)
```

还可以使用强制分发模式和其他高级选项进行批量读取。 有关其他信息，请参阅 [Kusto 源选项参考](https://github.com/Azure/azure-kusto-spark/blob/master/connector/src/main/scala/com/microsoft/kusto/spark/datasource/KustoSourceOptions.scala)。

```python
crp = sc._jvm.com.microsoft.azure.kusto.data.ClientRequestProperties()
crp.setOption("norequesttimeout",True)
crp.toString()

kustoDf  = spark.read \
            .format("com.microsoft.kusto.spark.synapse.datasource") \
            .option("spark.synapse.linkedService", "<link service name>") \
            .option("kustoDatabase", "<Database name>") \
            .option("kustoQuery", "<KQL Query>") \
            .option("clientRequestPropertiesJson", crp.toString()) \
            .option("readMode", 'ForceDistributedMode') \
            .load()

display(kustoDf) 
```
### <a name="write-data"></a>写入数据

```python
df.write \
    .format("com.microsoft.kusto.spark.synapse.datasource") \
    .option("spark.synapse.linkedService", "<link service name>") \
    .option("kustoDatabase", "<Database name>") \
    .option("kustoTable", "<Table name>") \
    .mode("Append") \
    .save()
```
此外，还可以通过提供其他引入属性来批量写入数据。 有关支持的引入属性的详细信息，请访问 [Kusto 引入属性参考资料](/azure/data-explorer/ingestion-properties)。


 ```python
extentsCreationTime = sc._jvm.org.joda.time.DateTime.now().plusDays(1)
csvMap = "[{\"Name\":\"ColA\",\"Ordinal\":0},{\"Name\":\"ColB\",\"Ordinal\":1}]"
# Alternatively use an existing csv mapping configured on the table and pass it as the last parameter of SparkIngestionProperties or use none


sp = sc._jvm.com.microsoft.kusto.spark.datasink.SparkIngestionProperties(
        False, ["dropByTags"], ["ingestByTags"], ["tags"], ["ingestIfNotExistsTags"], extentsCreationTime, csvMap, None)

df.write \
    .format("com.microsoft.kusto.spark.synapse.datasource") \
    .option("spark.synapse.linkedService", "<link service name>") \
    .option("kustoDatabase", "<Database name>") \
    .option("kustoTable", "<Table name>") \
    .option("sparkIngestionPropertiesJson", sp.toString()) \
    .option("tableCreateOptions","CreateIfNotExist") \
    .mode("Append") \
    .save()
```

## <a name="next-steps"></a>后续步骤
- [连接到 Azure 数据资源管理器](../../quickstart-connect-azure-data-explorer.md)
- [Azure 数据资源管理器 (Kusto) Apache Spark 连接器](https://github.com/Azure/azure-kusto-spark)
