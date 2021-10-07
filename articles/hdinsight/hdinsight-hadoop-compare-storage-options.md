---
title: 比较用于与 Azure HDInsight 群集配合使用的存储选项
description: 概述存储类型及其如何使用 Azure HDInsight。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4d58bfc14e4b61860d239cc3dcfd0ad276aec781
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273507"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>比较用于与 Azure HDInsight 群集配合使用的存储选项

创建 HDInsight 群集时，可以在几种不同的 Azure 存储服务之间进行选择：

* [带有 HDInsight 的 Azure Blob 存储](./overview-azure-storage.md)
* [带有 HDInsight 的 Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [带有 HDInsight 的 Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)

本文概述了这些存储类型和其独特功能。

## <a name="storage-types-and-features"></a>存储类型和功能

下表总结了可以与不同版本的 HDInsight 配合使用的 Azure 存储服务：

| 存储服务 | 帐户类型 | 命名空间类型 | 支持的服务 | 支持的性能层 | 支持的访问层 | HDInsight 版本 | 群集类型 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 常规用途 V2 | 分层（文件系统） | Blob | 标准 | 热、冷、存档 | 3.6+ | 全部（Spark 2.1 和 2.2 除外）|
|Azure 存储| 常规用途 V2 | 对象 | Blob | 标准 | 热、冷、存档 | 3.6+ | 全部 |
|Azure 存储| 常规用途 V1 | 对象 | Blob | Standard | 空值 | All | 全部 |
|Azure 存储| Blob 存储** | 对象 | 块 blob | 标准 | 热、冷、存档 | 全部 | 全部 |
|Azure Data Lake Storage Gen1| 不适用 | 分层（文件系统） | 不适用 | 不可用 | 不适用 | 仅 3.6 | 全部（HBase 除外） |
|Azure 存储| 块 blob| 对象 | 块 blob | 高级 | 空值| 3.6+ | 仅带有加速写入功能的 HBase|
|Azure Data Lake Storage Gen2| 块 blob| 分层（文件系统） | 块 blob | 高级 | 空值| 3.6+ | 仅带有加速写入功能的 HBase|

**对于 HDInsight 群集，只有辅助存储帐户才能是 BlobStorage 类型，页 blob 不是受支持的存储选项。

若要详细了解 Azure 存储帐户类型，请参阅 [Azure 存储帐户概述](../storage/common/storage-account-overview.md)

若要详细了解 Azure 存储访问层，请参阅 [Azure Blob 存储：高级存储层（预览）、热存储层、冷存储层和存档存储层](../storage/blobs/access-tiers-overview.md)

可以使用主要存储和（可选）辅助存储的服务组合来创建群集。 下表总结了 HDInsight 目前支持的群集存储配置：

| HDInsight 版本 | 主要存储 | 辅助存储 | 支持 |
|---|---|---|---|
| 3.6 和 4.0 | 常规用途 V1、常规用途 V2 | 常规用途 V1、常规用途 V2、BlobStorage（块 blob） | 是 |
| 3.6 和 4.0 | 常规用途 V1、常规用途 V2 | Data Lake Storage Gen2 | 否 |
| 3.6 和 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | 是 |
| 3.6 和 4.0 | Data Lake Storage Gen2* | 常规用途 V1、常规用途 V2、BlobStorage（块 blob） | 是 |
| 3.6 和 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 否 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | 是 |
| 3.6 | Data Lake Storage Gen1 | 常规用途 V1、常规用途 V2、BlobStorage（块 blob） | 是 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 否 |
| 4.0 | Data Lake Storage Gen1 | 任意 | 否 |
| 4.0 | 常规用途 V1、常规用途 V2 | Data Lake Storage Gen1 | 否 |

*=这可以是一个或多个 Data Lake Storage Gen2，只要它们都设置为使用相同的托管标识进行群集访问。

> [!NOTE]
> Spark 2.1 或 2.2 群集不支持 Data Lake Storage Gen2 主要存储。

## <a name="data-replication"></a>数据复制

Azure HDInsight 不存储客户数据。 群集的主要存储方法是其关联的存储帐户。 可以将群集附加到现有的存储帐户，也可以在群集创建过程中新建存储帐户。 如果新建帐户，则帐户会被创建为本地冗余存储 (LRS) 帐户，并满足区域内的数据驻留要求，包括[信任中心](https://azuredatacentermap.azurewebsites.net)内指定的要求。

通过确保与 HDInsight 关联的存储帐户是 LRS 或[信任中心](https://azuredatacentermap.azurewebsites.net)内提及的另一个存储选项，可以验证 HDInsight 是否正确地配置为在单个区域存储数据。
 
## <a name="next-steps"></a>后续步骤

* [HDInsight 中的 Azure 存储概述](./overview-azure-storage.md)
* [HDInsight 中的 Azure Data Lake Storage Gen1 概述](./overview-data-lake-storage-gen1.md)
* [HDInsight 中的 Azure Data Lake Storage Gen2 概述](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen2 简介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 存储简介](../storage/common/storage-introduction.md)
