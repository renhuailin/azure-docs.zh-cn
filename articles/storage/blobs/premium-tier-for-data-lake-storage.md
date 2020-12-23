---
title: Azure Data Lake Storage 的高级层 |Microsoft Docs
description: 结合使用 "高级" 性能层 Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/30/2020
ms.author: normesta
ms.openlocfilehash: be440407fb6f4d9715ba80c584af023a5f662394
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324223"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage 的高级层

Azure Data Lake Storage Gen2 现在支持 " [高级" 性能层](storage-blob-performance-tiers.md#premium-performance)。 高级性能层非常适合用于需要低延迟且具有大量事务的大数据分析应用程序和工作负荷。

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>可以受益于高级性能层的工作负荷

示例工作负荷包括交互式工作负荷、IoT、流分析、人工智能和机器学习。 

**交互式工作负荷** 

这些工作负荷需要即时更新和用户反馈，如电子商务和地图应用程序、交互式视频应用程序等。例如，在电子商务应用程序中，可能不会缓存经常查看的项目。 但是，这些商品必须可按需立即向客户显示。 作为另一个示例，数据科学家、分析人员和开发人员可以通过对存储在使用高级性能层的帐户中的数据运行查询，从而更快地获取时间敏感型的见解。 

**IoT/流分析** 

在 IoT 方案中，每秒可能就会将大量的小规模写入操作推送到云中。 大量数据可能会引入、聚合起来以便分析，然后几乎可以立即删除。 高级性能层的高引入功能使得这种类型的工作负荷更有效。 

**人工智能/机器学习 (AI/ML)** 

AI/ML 可以解决不同数据类型（例如视觉对象、语音和文本）的使用和处理。 这种高性能计算类型的工作负荷可以处理大量的、需要快速做出响应并在短时间内引入以进行高效数据分析的数据。 

## <a name="cost-effectiveness"></a>成本效益

与标准性能层相比，高级性能层的存储成本较高，但事务成本较低。 如果你的应用程序和工作负荷执行大量事务，则高级性能层可能是经济高效的。

下表演示了 Azure Data Lake Storage 的高级层的成本效益。 每个列表示一个月中的事务数。  每行表示读取事务的事务的百分比。 表中的每个单元显示与读取事务百分比和执行的事务数相关的成本降低百分比。 

例如，假设你的帐户位于美国东部2区域，你的帐户中的事务数超过90M，而这些事务的70% 是读取事务，高级性能级别更具成本效益。

> [!div class="mx-imgBorder"]
> ![此处显示图像](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> 如果希望根据每个 TB 的数据每秒的事务数评估成本有效性，则可以使用表底部显示的列标题。

有关定价的详细信息，请参阅 [Azure Data Lake Storage Gen2 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/) 页。

## <a name="feature-availability"></a>功能可用性 

某些 Blob 存储功能可能不可用，或者可能仅对高级性能层有部分支持。 有关完整列表，请参阅 [Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)。 然后，查看 [已知问题](data-lake-storage-known-issues.md) 的列表以评估功能的任何差距。

## <a name="enabling-the-premium-performance-tier"></a>启用高级性能层 

可以通过创建一个 **启用** 了 **分层命名空间** 设置的 BlockBlobStorage 帐户，将高级层用于 Azure Data Lake Storage。 有关完整指南，请参阅 [创建 BlockBlobStorage 帐户](storage-blob-create-account-block-blob.md) 帐户。

创建帐户时，请确保选择 " **高级** " 性能选项和 " **BlockBlobStorage** " 帐户类型。

> [!div class="mx-imgBorder"]
> ![创建 blockblobstorageacount](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

启用 " **创建存储帐户** " 页的 " **高级** " 选项卡中的 " **分层命名空间** " 设置。 必须在创建帐户时启用此设置。 无法在以后启用它。

下图显示了“创建存储帐户”页中的此设置。

> [!div class="mx-imgBorder"]
> ![分层命名空间设置](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>区域可用性

以下区域提供 Azure Data Lake Storage 的高级层。

|区域|冗余|
|--|--|
|美国东部|LRS、ZRS|
|美国东部 2|LRS、ZRS|
|Central US|LRS|
|美国西部|LRS|
|美国西部 2|LRS、ZRS|
|美国中西部|LRS|
|美国中南部|LRS|
|加拿大中部|LRS|
|加拿大东部|LRS|
|北欧|LRS、ZRS|
|西欧|LRS、ZRS|
|英国南部|LRS|
|英国西部|LRS|
|法国中部|LRS|
|亚洲东部|LRS|
|韩国中部|LRS|
|韩国南部|LRS|
|印度中部|LRS|
|印度西部|LRS|
|阿拉伯联合酋长国北部|LRS|
|日本东部|LRS|
|日本西部|LRS|
|东南亚|LRS、ZRS|
|澳大利亚东部|LRS、ZRS|
|澳大利亚东南部|LRS|
|巴西南部|LRS|

## <a name="next-steps"></a>后续步骤

将高级层用于 Azure Data Lake Storage 与你喜欢的分析服务（如 Azure Databricks、Azure HDInsight 和 Azure Synapse Analytics）结合使用。 

- [教程：Azure Data Lake Storage Gen2、Azure Databricks 和 Spark](data-lake-storage-use-databricks-spark.md) 
- [将 Azure Data Lake Storage Gen2 与 Azure HDInsight 群集配合使用](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) HDInsight 目前支持使用高级性能层的帐户以及启用了加速写入功能的 HBase 群集。
- [快速入门：创建 Synapse 工作区](../../synapse-analytics/quickstart-create-workspace.md)

