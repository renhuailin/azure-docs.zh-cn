---
title: Azure Data Lake Storage 的高级层 | Microsoft Docs
description: 将高级性能层用于 Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.openlocfilehash: 22ed6d6e4c9280d79027918615a51da305f2457f
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112453909"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage 的高级层

Azure Data Lake Storage Gen2 现在支持[高级性能层](storage-blob-performance-tiers.md#premium-performance)。 高级性能层非常适用于需要一致的低延迟且具有大量事务的大数据分析应用程序和工作负载。

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>可受益于高级性能层的工作负载

示例工作负载包括交互式工作负载、IoT、流式处理分析、人工智能和机器学习。 

**交互式工作负载** 

这些工作负载需要即时更新和用户反馈，例如电子商务和地图应用程序、交互式视频应用程序等。例如，在电子商务应用程序中，不经常查看的商品可能不会缓存。 但是，这些商品必须可按需立即向客户显示。 另一个示例是，数据科学家、分析人员和开发人员可以通过对使用高级性能层的帐户中存储的数据运行查询，更快地获取时间敏感型见解。 

**IoT/流式处理分析** 

在 IoT 方案中，每秒可能就会将大量的小规模写入操作推送到云中。 可能会引入大量的数据，将其聚合进行分析，然后立即将其删除。 高级性能层的大规模引入功能使之能够为此类工作负载带来效益。 

**人工智能/机器学习 (AI/ML)** 

AI/ML 可以解决不同数据类型（例如视觉对象、语音和文本）的使用和处理。 这种高性能计算类型的工作负荷可以处理大量的、需要快速做出响应并在短时间内引入以进行高效数据分析的数据。 

## <a name="cost-effectiveness"></a>成本效益

与标准性能层相比，高级性能层的存储成本更高，但事务成本更低。 如果应用程序和工作负载执行大量事务，则高级性能层可能具有成本效益。

下表展示了 Azure Data Lake Storage 的高级层的成本效益。 每列表示一个月中的事务数。  每行表示读取事务的事务百分比。 表中的每个单元格显示与读取事务百分比和执行的事务数相关的成本降低百分比。 

例如，假设你的帐户位于“美国东部 2”区域，帐户中的事务数超过 9000 万，且其中 70% 的事务是读取事务，则高级性能层更具成本效益。

> [!div class="mx-imgBorder"]
> ![图像在此处显示](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> 如果希望根据每 TB 数据的每秒事务数评估成本效益，则可以使用表底部显示的列标题。

有关定价的详细信息，请参阅 [Azure Data Lake Storage Gen2 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)页。

## <a name="feature-availability"></a>功能可用性 

某些 Blob 存储功能可能不可用，或者可能仅对高级性能层提供部分支持。 如需完整列表，请参阅 [Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)。 然后，查看[已知问题](data-lake-storage-known-issues.md)的列表以评估功能间的任何差距。

## <a name="enabling-the-premium-performance-tier"></a>启用高级性能层 

通过在启用“分层命名空间”设置的情况下创建 BlockBlobStorage 帐户，可使用 Azure Data Lake Storage 的高级层 。 有关完整指南，请参阅[创建 BlockBlobStorage 帐户](../common/storage-account-create.md)。

创建帐户时，请务必选择“高级”性能选项和“BlockBlobStorage”帐户类型 。

> [!div class="mx-imgBorder"]
> ![创建 blockblobstorageacount](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

在“创建存储帐户”页的“高级”选项卡中，启用“分层命名空间”设置  。 必须在创建帐户时启用此设置。 无法在以后启用它。

下图显示了“创建存储帐户”页中的此设置。

> [!div class="mx-imgBorder"]
> ![分层命名空间设置](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="next-steps"></a>后续步骤

将 Azure Data Lake Storage 的高级层与你喜欢的分析服务（例如 Azure Databricks、Azure HDInsight 和 Azure Synapse Analytics）配合使用。 

- [教程：Azure Data Lake Storage Gen2、Azure Databricks 和 Spark](data-lake-storage-use-databricks-spark.md) 
- [将 Azure Data Lake Storage Gen2 与 Azure HDInsight 群集配合使用](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) HDInsight 当前支持使用高级性能层和启用了加速写入的 HBase 群集的帐户。
- [快速入门：创建 Synapse 工作区](../../synapse-analytics/quickstart-create-workspace.md)