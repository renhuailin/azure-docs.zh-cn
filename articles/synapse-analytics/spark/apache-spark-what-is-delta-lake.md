---
title: 什么是 Delta Lake
description: Delta Lake 概述及其在 Azure Synapse Analytics 中的工作原理
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 23a269fe9498295a3ff325a261c6539f5e1e31aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676222"
---
# <a name="what-is-delta-lake"></a>什么是 Delta Lake

Azure Synapse Analytics 与 Linux Foundation Delta Lake 兼容。 Delta Lake 是开源存储层，可将 ACID（原子性、一致性、隔离性和持续性）事务引入 Apache Spark 和大数据工作负载。

Azure Synapse 随附的当前版本的 Delta Lake 支持 Scala、PySpark 和 .NET 语言。 点击页面底部的链接可参阅更多详细的示例和文档。

## <a name="key-features"></a>主要功能

| 功能 | 说明 |
| --- | --- |
| **ACID 事务** | 数据湖通常是通过多个流程和管道进行填充的，其中部分流程和管道可以并发地写入数据和读取数据。 在使用 Delta Lake 和添加事务之前，数据工程师必须手动完成一个易出错的流程，以确保数据完整性。 Delta Lake 将常用的 ACID 事务引入数据湖。 它提供了可序列化性，即最高的隔离级别。 若要了解详细信息，请访问[深入了解 Delta Lake：事务日志解包](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)。|
| **可缩放的元数据处理** | 在大数据中，元数据本身甚至也可以是“大数据”。 Delta Lake 将元数据视为数据，利用 Spark 的分布式处理能力来处理其所有元数据。 因此，Delta Lake 可以轻松处理具有数十亿个分区和文件的拍字节级表。 |
| **按时间顺序查看(数据版本控制)** | “撤消”更改或还原为先前版本是事务的关键功能之一。 Delta Lake 提供数据快照，允许还原到较早版本的数据进行审核、回滚或重现实验。 若要了解详细信息，请参阅[为大型数据湖引入“Delta Lake 按时间顺序查看”](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)。 |
| **开放格式** | Apache Parquet 是 Delta Lake 的基线格式，通过它可以使用原本就采用该格式的高效压缩和编码方案。 |
| **统一的批处理和流式处理源和接收器** | Delta Lake 中的表是批处理表，也是流式处理源和接收器。 流式处理数据引入、批处理历史回填、交互式查询功能都是现成的。 |
| **架构强制** | 架构强制有助于确保数据类型正确且必需列存在，从而防止不良数据导致数据不一致。 有关详细信息，请参阅[深入了解 Delta Lake：架构强制与演变](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **架构演变** | Delta Lake 使你可以不必编写迁移 DDL 就能更改可自动应用的表架构。 有关详细信息，请参阅[深入了解 Delta Lake：架构强制与演变](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **审核历史记录** | Delta Lake 交易日志详细记录了对数据所做的每一项更改，可提供有关更改的完整审核日志。 |
| **更新和删除** | Delta Lake 支持 Scala/Java/Python 和 SQL API，可实现各种相关功能。 对合并、更新和删除操作的支持有助于满足合规性要求。 有关详细信息，请参阅[公布 Delta Lake 0.6.1 版本](https://delta.io/news/delta-lake-0-6-1-released/)、[公布 Delta Lake 0.7 版本](https://delta.io/news/delta-lake-0-7-0-released/)和[使用 Python API 在 Delta Lake 表中进行简单、可靠的更新插入和删除](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)，其中包含合并、更新和删除 DML 命令的代码片段。 |
| **与 Apache Spark API 完全兼容** | 由于 Delta Lake 与现有的 Spark 实现完全兼容，因此开发人员只需进行最低限度的更改即可将其与现有数据管道结合使用。 |

有关完整文档，请参阅 [Delta Lake 文档页](https://docs.delta.io/latest/delta-intro.html)

有关详细信息，请参阅 [Delta Lake 项目](https://github.com/delta-io/delta)。

## <a name="next-steps"></a>后续步骤

- [.NET for Apache Spark 文档](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)