---
title: 使用存储过程活动转换数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 说明如何使用 SQL Server 存储过程活动从数据工厂管道调用 Azure SQL 数据库/数据仓库中的存储过程。
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 11/27/2018
ms.openlocfilehash: c3815bfca83d6dcf789a780a2d82ff1d4bb62285
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271975"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用 SQL Server 存储过程活动转换数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-stored-proc-activity.md)
> * [当前版本](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

可使用数据工厂[管道](concepts-pipelines-activities.md)中的数据转换活动将原始数据转换和处理为预测和见解。 存储过程活动是数据工厂支持的转换活动之一。 本文基于[转换数据](transform-data.md)一文编写，它概述了数据转换和数据工厂中支持的转换活动。

> [!NOTE]
> 如果不熟悉 Azure 数据工厂，请在阅读本文之前，先通读 [Azure 数据工厂简介](introduction.md)，并学习以下教程：[教程：转换数据](tutorial-transform-data-spark-powershell.md)。 

可以使用存储过程活动调用企业或 Azure 虚拟机 (VM) 中以下数据存储中的存储过程： 

- Azure SQL 数据库
- Azure Synapse Analytics
- SQL Server 数据库。  如果使用 SQL Server，请在托管数据库的同一计算机上或在可以访问数据库的单独计算机上安装自托管集成运行时。 自托管集成运行时是一种以安全托管方式将本地/Azure VM 上的数据源与云服务进行连接的组件。 有关详细信息，请参阅[自托管集成运行时](create-self-hosted-integration-runtime.md)一文。

> [!IMPORTANT]
> 将数据复制到 Azure SQL 数据库或 SQL Server 中时，可以使用 sqlWriterStoredProcedureName 属性将复制活动中的 SqlSink 配置为调用存储过程   。 有关属性的详细信息，请参阅以下连接器文章：[Azure SQL 数据库](connector-azure-sql-database.md)、[SQL Server](connector-sql-server.md)。 不支持在使用复制活动将数据复制到 Azure Synapse Analytics 时调用存储过程。 但是，可使用存储过程活动来调用 Azure Synapse Analytics 中的存储过程。 
>
> 从 Azure SQL 数据库、SQL Server 或 Azure Synapse Analytics 复制数据时，可以使用 sqlReaderStoredProcedureName 属性将复制活动中的 SqlSource 配置为调用存储过程，以便从源数据库读取数据 。 有关详细信息，请参阅以下连接器文章：[Azure SQL 数据库](connector-azure-sql-database.md)、[SQL Server](connector-sql-server.md)、[Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>语法详细信息
下面是用于定义存储过程活动的 JSON 格式：

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

下表描述了其中的 JSON 属性：

| 属性                  | 说明                              | 必需 |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | 活动名称                     | 是      |
| description               | 描述活动用途的文本 | 否       |
| type                      | 对于存储过程活动，活动类型是 SqlServerStoredProcedure | 是      |
| linkedServiceName         | 引用在数据工厂中注册为链接服务的 Azure SQL 数据库、Azure Synapse Analytics 或 SQL Server。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。 | 是      |
| storedProcedureName       | 指定要调用的存储过程的名称。 | 是      |
| storedProcedureParameters | 指定存储过程的参数值。 使用 `"param1": { "value": "param1Value","type":"param1Type" }` 传递数据源支持的参数值及其类型。 如果需要为参数传递 null，请使用 `"param1": { "value": null }`（全部小写）。 | 否       |

## <a name="parameter-data-type-mapping"></a>参数数据类型映射
为参数指定的数据类型是 Azure 数据工厂类型，该类型映射到正在使用的数据源中的数据类型。 可以在连接器文档中找到所述数据源的数据类型映射。 例如：

- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#data-type-mapping-for-azure-synapse-analytics)
- [Azure SQL 数据库数据类型映射](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database)
- [Oracle 数据类型映射](connector-oracle.md#data-type-mapping-for-oracle)
- [SQL Server 数据类型映射](connector-sql-server.md#data-type-mapping-for-sql-server)

## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据： 

* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [机器学习工作室（经典）批处理执行活动](transform-data-using-machine-learning.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
