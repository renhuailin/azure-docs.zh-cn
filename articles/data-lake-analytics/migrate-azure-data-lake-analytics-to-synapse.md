---
title: 从 Azure Data Lake Analytics 迁移到 Azure Synapse Analytics。
description: 本文介绍如何从 Azure Data Lake Analytics 迁移到 Azure Synapse Analytics。
author: lingluo0531
ms.author: lingluo
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: migrate-azure-data-lake-analytics-to-synapse
ms.date: 08/25/2021
ms.openlocfilehash: 34a199fe8f488add4f1f2cdd9357dd9ac718d4f0
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254970"
---
# <a name="migrate-azure-data-lake-analytics-to-azure-synapse-analytics"></a>从 Azure Data Lake Analytics 迁移到 Azure Synapse Analytics

Microsoft 推出了 Azure Synapse Analytics，旨在将数据湖和数据仓库汇集在一起，提供独特的大数据分析体验。 它将帮助客户收集和分析所有不同的数据，以解决数据效率低下的问题，并协同工作。 此外，Synapse 与 Azure 机器学习和 Power BI 的集成将使组织能够从其数据中获取见解，并将机器学习执行到其所有智能应用中。 

本文档演示如何从 Azure Data Lake Analytics 迁移到 Azure Synapse Analytics。 

## <a name="recommended-approach"></a>推荐的方法
- 步骤1：评估准备情况
- 步骤 2：准备进行迁移
- 步骤 3：迁移数据和应用程序工作负载
- 步骤 4：从 Azure Data Lake Analytics 直接转换到 Azure Synapse Analytics

### <a name="step-1-assess-readiness"></a>步骤1：评估准备情况

1. 查看 [Azure Synapse Analytics 上的 Apache Spark](../synapse-analytics/spark/apache-spark-overview.md)，并了解 Azure Data Lake Analytics 和 Azure Synapse Analytics 上的 Spark 的关键差异。 

    |项 | Azure Data Lake Analytics | Synapse 上的 Spark |
    | --- | --- |--- |
    | 定价  |每分析单位/小时 |每 vCore/小时|
    |引擎     |Azure Data Lake Analytics  |Apache Spark
    |默认编程语言    |U-SQL   |T-SQL、Python、Scala、Spark SQL 和 .NET
    |“数据源”   |Azure Data Lake Storage    |Azure Blob 存储、Azure Data Lake Storage

2. 查看<a href="#questionnaire">迁移评估调查表</a>，并列出要考虑的可能风险。 

### <a name="step-2-prepare-to-migrate"></a>步骤 2：准备进行迁移

1.  确定要迁移的作业和数据。
    -   利用此机会清理这些不再使用的作业。 除非计划一次迁移所有作业，否则请花些时间来确定可以分阶段迁移的作业逻辑组。
    -   评估数据的大小并了解 Apache Spark 数据格式。 查看 U-SQL 脚本，评估脚本的重新编写工作并了解 Apache Spark 代码概念。

2.  确定迁移将对你的业务造成的影响。 例如，进行迁移时是否可以承受任何停机时间。

3.  创建迁移计划。

### <a name="step-3-migrate-data-and-application-workload"></a>步骤 3：迁移数据和应用程序工作负载

1.  将数据从 Azure Data Lake Storage Gen1 迁移到 Azure Data Lake Storage Gen2。 <br></br>
    Azure Data Lake Storage Gen1 将于 2024 年 2 月停用，请参阅[官方公告](https://azure.microsoft.com/updates/action-required-switch-to-azure-data-lake-storage-gen2-by-29-february-2024/)。 建议首先将数据迁移到 Gen2。 请参阅[了解适用于 Azure Data Lake Analytics U-SQL 开发人员的 Apache Spark 数据格式](understand-spark-data-formats.md)，并移动存储在 U-SQL 表中的文件和数据，使其可供 Azure Synapse Analytics 访问。  有关更多迁移指南的详细信息，请参阅[此处](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)。 

2.  将 U-SQL 脚本转换为 Spark。 
    请参阅[了解适用于 Azure Data Lake Analytics U-SQL 开发人员的 Apache Spark 代码概念](understand-spark-code-concepts.md)，以将 U-SQL 脚本转换为 Spark。 

3.  将作业业务流程管道转换到 Spark 程序或重新创建新的业务流程管道。

### <a name="step-4-cut-over-from-azure-data-lake-analytics-to-azure-synapse-analytics"></a>步骤 4：从 Azure Data Lake Analytics 直接转换到 Azure Synapse Analytics

确信应用程序和工作负载稳定后，可以开始使用 Azure Synapse Analytics 来满足业务场景。 关闭所有在 Azure Data Lake Analytics 上运行的其余管道，并解除 Azure Data Lake Analytics 帐户。

<a name="questionnaire"></a>
## <a name="questionnaire-for-migration-assessment"></a>迁移评估调查表 

|类别   |问题  |参考|
| --- | --- |--- |
|评估迁移的大小 |你有多少 Azure Data Lake Analytics 帐户？ 有多少管道正在使用？ 有多少 U-SQL 脚本正在使用？| 要迁移的数据和脚本越多，脚本中使用的 UDO/UDF 就越多，迁移的难度就越大。 迁移所需的时间和资源需要根据项目的规模进行良好的规划。|
|数据源 |数据源的大小是什么？ 要处理哪些类型的数据格式？ |[了解适用于 Azure Data Lake Analytics U-SQL 开发人员的 Apache Spark 数据格式](understand-spark-data-formats.md)|
|数据输出 |你会保留输出数据供以后使用吗？ 如果输出数据保存在 U-SQL 表中，如何处理它？ | 如果输出数据将经常使用并保存在 U-SQL 表中，则需要更改脚本并将输出数据更改为 Spark 支持的数据格式。|
|数据迁移 |你已制定存储迁移计划？ |[将 Azure Data Lake Storage 从 Gen1 迁移到 Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) |
|U-SQL 脚本转换|是否使用 UDO/UDF（.NET、Python 等）？如果上述答案为“是”，那么在 UDO/UDF 中使用的是哪种语言，转换期间是否存在任何转换问题？U-SQL 中是否使用联机查询？|[了解适用于 Azure Data Lake Analytics U-SQL 开发人员的 Apache Spark 代码概念](understand-spark-code-concepts.md)|

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](../synapse-analytics/get-started.md)
