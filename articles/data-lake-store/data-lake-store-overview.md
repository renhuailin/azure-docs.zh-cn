---
title: 什么是 Azure Data Lake Storage Gen1？ | Microsoft Docs
description: 概述 Data Lake Storage Gen1（以前称为 Azure Data Lake Store），以及它与其他数据存储相比具有哪些优势
author: normesta
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: normesta
ms.openlocfilehash: f105815e6c20777060c1350b9f12fbb160dbc710
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655574"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>什么是 Azure Data Lake Storage Gen1？

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 是一个企业范围的超大规模存储库，适用于大数据分析工作负载。 使用 Azure Data Lake 可以在单个位置捕获任何大小、类型和引入速度的数据进行操作和探索分析。

使用与 WebHDFS 兼容的 REST API，可以从 Hadoop（HDInsight 群集提供）访问 Data Lake Storage Gen1。 该服务专为分析存储数据而设计，并已针对数据分析方案优化了性能。 Data Lake Storage Gen1 包含所有企业级功能：安全性、可管理性、可伸缩性、可靠性和可用性。

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>关键功能

下面介绍了 Data Lake Storage Gen1 的一些重要功能。

### <a name="built-for-hadoop"></a>专为 Hadoop 而构建

Data Lake Storage Gen1 是一个 Apache Hadoop 文件系统，该系统与 Hadoop 分布式文件系统 (HDFS) 兼容并与 Hadoop 生态系统相互协作。 采用 WebHDFS API 的现有 HDInsight 应用程序或服务可以轻松与 Data Lake Storage Gen1 集成。 Data Lake Storage Gen1 还为应用程序公开了 WebHDFS 兼容的 REST 接口。

使用 Hadoop 分析框架（例如 MapReduce 或 Hive），可以轻松分析 Data Lake Storage Gen1 中存储的数据。 可以预配 Azure HDInsight 群集并将其配置为直接访问 Data Lake Storage Gen1 中存储的数据。

### <a name="unlimited-storage-petabyte-files"></a>无限存储空间，PB 量级的文件

Data Lake Storage Gen1 提供无限存储空间，可存储各种分析数据。 它对帐户大小、文件大小或 Data Lake 中可存储的数据量均无任何限制。 单个文件的大小可以从数 KB 到数 PB 不等。 可通过创建多个副本来长期存储数据。 数据在 Data Lake 中的存储持续时间没有限制。

### <a name="performance-tuned-for-big-data-analytics"></a>针对大数据分析优化了性能

Data Lake Storage Gen1 旨在运行需要利用超大吞吐量查询和分析海量数据的大规模分析系统。 Data Lake 将文件的各个部分散在大量独立的存储服务器中。 这可改善执行数据分析时并行读取文件的吞吐量。

### <a name="enterprise-ready-highly-available-and-secure"></a>面向企业：高度可用且安全

Data Lake Storage Gen1 提供符合行业标准的可用性和可靠性。 数据资产可通过创建冗余副本来长期存储，防范任何意外的故障。

Data Lake Storage Gen1 还为存储的数据提供企业级安全性。 有关详细信息，请参阅 [保护 Azure Data Lake Storage Gen1 中的数据](#DataLakeStoreSecurity)。

### <a name="all-data"></a>所有数据

Data Lake Storage Gen1 可按本机格式存储任何数据，不需要事先经过转换。 加载数据之前，Data Lake Storage Gen1 不需要定义架构，而是等待独立的分析框架在分析时解释数据和定义架构。 Data Lake Storage Gen1 能够存储任意大小和格式的文件，因此可以处理结构化、半结构化和非结构化数据。

Data Lake Storage Gen1 的数据容器本质上是文件夹和文件。 可以使用 SDK、Azure 门户和 Powershell 来操作存储的数据。 如果使用这些接口和相应容器将数据放入存储，则可以存储任何类型的数据。 Data Lake Storage Gen1 不会根据其存储的数据类型对数据执行任何特殊处理。

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>保护数据

Data Lake Storage Gen1 使用 Azure Active Directory (Azure AD) 进行身份验证，使用访问控制列表 (ACL) 管理对数据的访问。

| 功能 | 说明 |
| --- | --- |
| 身份验证 |Data Lake Storage Gen1 与 Azure AD 集成，可对 Data Lake Storage Gen1 中存储的所有数据进行标识与访问管理。 因为这种集成，Data Lake Storage Gen1 可受益于所有 Azure AD 功能，例如多重身份验证、条件访问、Azure 基于角色的访问控制、应用程序使用情况监视、安全监视和警报等。 Data Lake Storage Gen1 支持在 REST 接口中使用 OAuth 2.0 协议进行身份验证。 请参阅 [Data Lake Storage Gen1 身份验证](data-lakes-store-authentication-using-azure-active-directory.md)。|
| 访问控制 |Data Lake Storage Gen1 通过支持 WebHDFS 协议公开的 POSIX 样式权限来提供访问控制。 可对根文件夹、子文件夹和单个文件启用 ACL。 有关 ACL 在 Data Lake Storage Gen1 上下文中的工作原理的详细信息，请参阅 [Data Lake Storage Gen1 中的访问控制](data-lake-store-access-control.md)。 |
| 加密 |Data Lake Storage Gen1 还针对帐户中存储的数据提供加密。 创建 Data Lake Storage Gen1 帐户时可以指定加密设置。 可以选择加密或不加密数据。 有关详细信息，请参阅 [Data Lake Storage Gen1 中的加密](data-lake-store-encryption.md)。 有关如何提供加密相关配置的说明，请参阅[通过 Azure 门户开始使用 Data Lake Storage Gen1](data-lake-store-get-started-portal.md)。 |

有关如何保护 Data Lake Storage Gen1 中的数据的说明，请参阅[保护 Azure Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)。

## <a name="application-compatibility"></a>应用程序兼容性

Data Lake Storage Gen1 与 Hadoop 生态系统中的大多数开源组件兼容。 此外，还与其他 Azure 服务完美集成。 若要详细了解如何将 Data Lake Storage Gen1 与开源组件和其他 Azure 服务配合使用，请使用以下链接：

- 有关可与 Data Lake Storage Gen1 互操作的开源应用程序列表，请参阅[与 Azure Data Lake Storage Gen1 兼容的应用程序和服务](data-lake-store-compatible-oss-other-applications.md)。
- 请参阅[与其他 Azure 服务集成](data-lake-store-integrate-with-other-services.md)，了解 Data Lake Storage Gen1 如何与其他 Azure 服务配合提供更多方案。
- 请参阅[使用 Data Lake Storage Gen1 的方案](data-lake-store-data-scenarios.md)，了解如何在引入数据、处理数据、下载数据和可视化数据等方案中使用 Data Lake Storage Gen1。

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 文件系统

可以在 Hadoop 环境（在 HDInsight 群集上提供）中通过文件系统 AzureDataLakeFilesystem (adl://) 访问 Data Lake Storage Gen1。 使用 adl:// 的应用程序和服务可以使用 WebHDFS 中尚不可用的其他性能优化功能。 因此，使用 Data Lake Storage Gen1 时，可以灵活使用 adl://（建议选项）获得最佳性能，或继续直接使用 WebHDFS API 维护现有代码。 Azure HDInsight 充分使用 AzureDataLakeFilesystem 来提供 Data Lake Storage Gen1 的最佳性能。

可以使用 `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` 访问 Data Lake Storage Gen1 中的数据。 有关如何访问 Data Lake Storage Gen1 中的数据的详细信息，请参阅[查看存储数据的属性](data-lake-store-get-started-portal.md#properties)。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户开始使用 Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
- [通过 .NET SDK 开始使用 Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)
- [将 Azure HDInsight 与 Data Lake Storage Gen1 配合使用](data-lake-store-hdinsight-hadoop-use-portal.md)