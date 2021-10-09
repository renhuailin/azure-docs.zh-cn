---
title: Azure Data Lake Storage Gen2 简介
description: 查看 Azure Data Lake Storage Gen2 简介。 了解关键功能。 查看支持的 Blob 存储功能、Azure 服务集成和平台。
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 34484c501a95379194ad2147fbcb553bf3f06980
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277682"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 简介

Azure Data Lake Storage Gen2 是一组专用于大数据分析的功能，基于 [Azure Blob 存储](storage-blobs-introduction.md)而构建。

Data Lake Storage Gen2 囊括了 [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) 和 Azure Blob 存储的功能。 例如，Data Lake Storage Gen2 提供文件系统语义、文件级安全和缩放。 由于这些功能是在 Blob 存储的基础上构建的，因此还可以得到具有高可用性/灾难恢复功能的低成本分层存储。

## <a name="designed-for-enterprise-big-data-analytics"></a>专为企业大数据分析而设计

Data Lake Storage Gen2 使 Azure 存储成为在 Azure 上构建企业 Data Lake 的基础。 Data Lake Storage Gen2 从一开始就设计为存储数千万亿字节的信息，同时保持数百千兆位的吞吐量，允许你轻松管理大量数据。

Data Lake Storage Gen2 的一个基本部分是向 Blob 存储添加[分层命名空间](data-lake-storage-namespace.md)。 分层命名空间将对象/文件组织到目录层次结构中，以便进行有效的数据访问。 常见的对象存储命名约定在名称中使用斜杠来模拟分层目录结构。 这种结构在 Data Lake Storage Gen2 中得以真正实现。 重命名或删除目录等操作会成为目录上的单个原子元数据操作。 无需枚举和处理共享目录名称前缀的所有对象。

Data Lake Storage Gen2 在 Blob 存储的基础上构建，并通过以下方式增强了性能、管理和安全性：

- 优化了性能，因为你不需要将复制或转换数据作为分析的先决条件。 与 Blob 存储上的平面命名空间相比，分层命名空间极大地提高了目录管理操作的性能，从而提高了整体作业性能。

- 管理更为容易，因为你可以通过目录和子目录来组织和操作文件。

- 安全性是可以强制实施的，因为可以在目录或单个文件上定义 POSIX 权限。

另外，Data Lake Storage Gen2 非常经济高效，因为它构建在低成本的 [Azure Blob 存储](storage-blobs-introduction.md)之上。 这些新增功能进一步降低了在 Azure 上运行大数据分析的总拥有成本。

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2 的主要功能

- Hadoop 兼容访问：Data Lake Storage Gen2 允许你管理和访问数据，就像在 [Hadoop 分布式文件系统 (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) 中一样。 新的 [ABFS 驱动程序](data-lake-storage-abfs-driver.md)（用于访问数据）在所有 Apache Hadoop 环境中都可用。 这些环境包括 [Azure HDInsight](../../hdinsight/index.yml)、[Azure Databricks](/azure/databricks/) 和 [Azure Synapse Analytics](../../synapse-analytics/index.yml)。

- **POSIX 权限的超集：** Data Lake Gen2 的安全模型支持 ACL 和 POSIX 权限，以及特定于 Data Lake Storage Gen2 的一些额外粒度。 可以通过存储资源管理器或 Hive 和 Spark 等框架来配置设置。

- **成本效益**：Data Lake Storage Gen2 提供了低成本的存储容量和事务。 [Azure Blob 存储生命周期](./lifecycle-management-overview.md)等功能可在数据在其生命周期中进行转换时优化成本。

- **优化的驱动程序：** ABFS 驱动程序专门针对大数据分析而进行[优化](data-lake-storage-abfs-driver.md)。 相应的 REST API 通过终结点 `dfs.core.windows.net` 进行显示。

### <a name="scalability"></a>可伸缩性

按照设计，无论是通过 Data Lake Storage Gen2 还是 Blob 存储接口进行访问，Azure 存储都可自如缩放。 它可以存储和处理许多百亿亿字节的数据。 这种存储量可用于在每秒高级别的输入/输出操作 (IOPS) 下以每秒千兆位 (Gbps) 的速度测量的吞吐量。 会根据在服务、帐户和文件级别上测量的近乎持续的按请求度量的延迟来进行处理。

### <a name="cost-effectiveness"></a>成本效益

因为 Data Lake Storage Gen2 是在 Azure Blob 存储基础之上构建的，所以存储容量和事务成本较低。 不同于其他云存储服务，在分析数据之前，无需移动或转换数据。 有关定价的详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage)。

此外，[分层命名空间](data-lake-storage-namespace.md)等功能可显著提高许多分析作业的整体性能。 这一性能方面的提升意味着你需要较少的计算能力来处理相同数量的数据，从而降低端到端分析作业的总拥有成本 (TCO)。

### <a name="one-service-multiple-concepts"></a>一个服务，多个概念

因为 Data Lake Storage Gen2 是在 Azure Blob 存储基础之上构建的，所以可以使用多个概念来描述相同的共享内容。

以下是不同概念所描述的等效实体。 除非另有说明，否则这些实体是直接同义的：

| 概念                                | 顶级组织 | 较低级别的组织                                            | 数据容器 |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blob - 常规用途对象存储 | 容器              | 虚拟目录（仅限 SDK - 不提供原子操作） | Blob           |
| Azure Data Lake Storage Gen2 - 分析存储          | 容器            | Directory                                                           | 文件           |

## <a name="supported-blob-storage-features"></a>支持的 Blob 存储功能

你的帐户现在可使用[诊断日志记录](../common/storage-analytics-logging.md)、[访问层](access-tiers-overview.md)和 [Blob 存储生命周期管理策略](./lifecycle-management-overview.md)等 Blob 存储功能。 大多数 Blob 存储功能完全受支持，但某些功能仅在预览级别受支持，或者尚不受支持。

若要了解 Data Lake Storage Gen2 如何支持每项 Blob 存储功能，请参阅 [Azure 存储帐户中的 Blob 存储功能支持](storage-feature-support-in-storage-accounts.md)。

## <a name="supported-azure-service-integrations"></a>支持的 Azure 服务集成

Data Lake Storage gen2 支持多个 Azure 服务。 可以使用它们来引入数据、执行分析和创建可视化表示形式。 有关受支持的 Azure 服务的列表，请参阅[支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)。

## <a name="supported-open-source-platforms"></a>支持的开源平台

多个开源平台支持 Data Lake Storage Gen2。 有关完整列表，请参阅[支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)。

## <a name="see-also"></a>另请参阅

- [Azure Data Lake Storage Gen2 的已知问题](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage 的多协议访问](data-lake-storage-multi-protocol-access.md)
