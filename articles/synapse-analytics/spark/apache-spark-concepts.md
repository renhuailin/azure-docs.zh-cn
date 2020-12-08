---
title: Apache Spark 核心概念
description: 介绍 Azure Synapse Analytics 中 Apache Spark 的核心概念。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 8df7a2ecd2a6732a011267eb0efe00e8eaf2bef4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458742"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Azure Synapse Analytics 中的 Apache Spark 的核心概念

Apache Spark 是并行处理框架，支持使用内存中处理来提升大数据分析应用程序的性能。 Azure Synapse Analytics 中的 Apache Spark 是 Apache Spark 在云中的一种 Microsoft 实现。 

使用 Azure Synapse 可在 Azure 中轻松创建和配置 Spark 功能。 Azure Synapse 提供本文档所述的这些 Spark 功能的不同实现。

## <a name="spark-pools"></a>Spark 池

无服务器 Apache Spark 池在 Azure 门户中创建。 Spark 池的定义是，在实例化后，该池可用于创建一个 Spark 实例来处理数据。 Spark 池在创建后只作为元数据存在，不会消耗、运行资源，也不会产生资源费用。 Spark 池具有一系列控制 Spark 实例特征的属性。 这些特征包括但不限于名称、大小、缩放行为、生存时间。

由于创建 Spark 池不会产生相关的费用或资源成本，因此可以使用任意数目的不同配置创建任意数目的 Spark 池。 还可以将权限应用到 Spark 池，使用户只能访问某些池。

最佳做法是先创建较小的 Spark 池进行开发和调试，然后再创建较大的 Spark 池来运行生产工作负载。

可在 [Azure Synapse Analytics 中的 Spark 池入门](../quickstart-create-apache-spark-pool-portal.md)中了解如何创建 Spark 池及查看其所有属性

## <a name="spark-instances"></a>Spark 实例

Spark 实例在你连接到 Spark 池、创建会话和运行作业时创建。 由于可能有多个用户有权访问单个 Spark 池，因此将为连接的每个用户创建一个新的 Spark 实例。 

提交第二个作业时，如果池中有容量，则现有的 Spark 实例也有容量。 然后，现有实例将处理该作业。 否则，如果容量在池级别可用，则将新建一个 Spark 实例。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1

- 创建名为 SP1 的 Spark 池；其固定群集大小为 20 个节点。
- 提交使用 10 个节点的笔记本作业 J1，此时会创建 Spark 实例 SI1 来处理该作业。
- 现在提交使用 10 个节点的另一个作业 J2（因为池和实例中仍有容量），此时，J2 将由 SI1 处理。
- 如果 J2 请求 11 个节点，则 SP1 或 SI1 中不会有容量。 在这种情况下，如果 J2 来自笔记本，则会拒绝该作业；如果 J2 来自批处理作业，则会将其排队。

### <a name="example-2"></a>示例 2

- 创建名为 SP2 的 Spark 池；该池已启用自动缩放，并使用 10-20 个节点
- 提交使用 10 个节点的笔记本作业 J1，此时会创建 Spark 实例 SI1 来处理该作业。
- 现在提交使用 10 个节点的另一个作业 J2（因为池中仍有容量），此时实例会自动扩展到 20 个节点并处理 J2。

### <a name="example-3"></a>示例 3

- 创建名为 SP1 的 Spark 池；其固定群集大小为 20 个节点。
- 提交使用 10 个节点的笔记本作业 J1，此时会创建 Spark 实例 SI1 来处理该作业。
- 另一个用户 U2 提交使用 10 个节点的作业 J3，此时会创建新的 Spark 实例 SI2 来处理该作业。
- 你现在提交使用 10 个节点的另一个作业 J2，因为池和实例中仍有容量，J2 将由 SI1 处理。

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Apache Spark for Azure Synapse 中的配额和资源约束

### <a name="workspace-level"></a>工作区级别

每个 Azure Synapse 工作区都附带了可用于 Spark 的 vCore 默认配额。 该配额拆分为用户配额和数据流配额，因此两种使用模式都只在工作区中使用部分 vCore。 配额有所差别，具体取决于订阅的类型，但在用户和数据流之间对称。 然而，如果请求的 vCore 数超出工作区中剩余的数目，将收到以下错误：

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

消息中的链接指向本文。

下文介绍了如何请求调高工作区 vCore 配额。

- 选择“Azure Synapse Analytics”作为服务类型。
- 在“配额详细信息”窗口中，选择“每工作区 Apache Spark (vCore)”

[通过 Azure 门户请求增加容量](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests#request-a-standard-quota-increase-from-help--support)

### <a name="spark-pool-level"></a>Spark 池级别

定义 Spark 池时，可有效地为该池定义每位用户的配额；如果运行多个笔记本和/或多个作业，则可能会耗尽池配额。 如果这样做，将生成如下所示的错误消息

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

若要解决此问题，必须在运行笔记本或作业来提交新的资源请求之前，减少对池资源的使用量。

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 文档](https://spark.apache.org/docs/2.4.5/)
