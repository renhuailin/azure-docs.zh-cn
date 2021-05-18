---
title: Azure 流分析中的 Azure SQL 数据库输出
description: 本文介绍作为 Azure 流分析的输出的 Azure SQL 数据库。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 631fdba451f69e44a675d396a42e1cddaea50a3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013951"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Azure 流分析中的 Azure SQL 数据库输出

可以将 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)用作本质上为关系型数据的输出，也可以将其用于所依赖的内容在关系数据库中托管的应用程序。 Azure 流分析作业将写入到 SQL 数据库的现有表中。 表架构必须与作业输出中的字段及其类型完全匹配。 还可以通过 SQL 数据库输出选项将 [Azure Synapse Analytics](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) 指定为输出。 若要了解提高写入吞吐量的方法，请参阅[以 Azure SQL 数据库作为输出的流分析](stream-analytics-sql-output-perf.md)一文。

还可以使用 [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)作为输出。 必须[在 SQL 托管实例中配置公共终结点](../azure-sql/managed-instance/public-endpoint-configure.md)，然后在 Azure 流分析中手动配置以下设置。 还可以通过手动配置以下设置来支持运行附加了数据库的 SQL Server 的 Azure 虚拟机。

## <a name="output-configuration"></a>输出配置

下表列出了属性名称和用于创建 SQL 数据库输出的属性说明。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 |在查询中使用的友好名称，用于将查询输出定向到此数据库。 |
| 数据库 | 将输出发送到的数据库的名称。 |
| 服务器名称 | 逻辑 SQL 服务器名称或托管实例名称。 对于 SQL 托管实例，需要指定端口 3342。 例如，“sampleserver.public.database.windows.net,3342” |
| 用户名 | 对数据库拥有写入访问权限的用户名。 流分析仅支持 SQL 身份验证。 |
| 密码 | 用于连接到数据库的密码。 |
| 表 | 将写入输出的表名称。 表名称区分大小写。 此表的架构应与字段数量以及作业输出生成的字段类型完全匹配。 |
|继承分区方案| 一个用于继承先前查询步骤的分区方案，以启用具有多个表的写入器的完全并行拓扑的选项。 有关详细信息，请参阅从 [Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)。|
|最大批数| 与每个大容量插入事务一起发送的推荐记录数上限。|

有两个适配器可用于从 Azure 流分析输出到 Azure Synapse Analytics：SQL 数据库和 Azure Synapse。 如果以下任何条件成立，则建议选择 Azure Synapse Analytics 适配器，而不是 SQL 数据库适配器：

* **吞吐量**：如果现在或未来的预期吞吐量大于 10 MB/秒，请使用 Azure Synapse 输出选项来获得更好的性能。

* 输入分区数：如果有八个或更多个输入分区，请使用 Azure Synapse 输出选项进行更好的扩展。

## <a name="partitioning"></a>分区

需要启用分区，且分区基于查询中的 PARTITION BY 子句。 启用“继承分区”选项后，该选项将遵循[完全可并行化的查询](stream-analytics-scale-jobs.md)的输入分区。 若要详细了解在将数据载入 Azure SQL 数据库时如何提高写入吞吐量性能，请参阅[从 Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)。

## <a name="output-batch-size"></a>输出批大小

可以使用“最大批处理计数”来配置最大消息大小。 每次批量插入操作的默认最大值为 10,000 行，最小值为 100 行。 有关详细信息，请参阅 [Azure SQL 限制](../azure-sql/database/resource-limits-logical-server.md)。 每个批最初是按照最大批计数批量插入的。 根据 SQL 的可重试错误对半拆分批（直到达到最小批计数）。

## <a name="next-steps"></a>后续步骤

* [使用托管标识访问 Azure 流分析作业的 Azure SQL 数据库或 Azure Synapse Analytics（预览）](sql-database-output-managed-identity.md)
* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)