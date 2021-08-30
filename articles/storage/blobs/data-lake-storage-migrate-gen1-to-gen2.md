---
title: 将 Azure Data Lake Storage 从 Gen1 迁移到 Gen2
description: 将 Azure Data Lake Storage 从 Gen1 迁移到 Gen2（在 Azure Blob 存储基础上构建，它提供了一组专用于大数据分析的功能）。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 07/13/2021
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d6d173188c147e04a688ff7c373a1a253393ee87
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114204509"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>将 Azure Data Lake Storage 从 Gen1 迁移到 Gen2

可以将数据、工作负载和应用程序从 Data Lake Storage Gen1 迁移到 Data Lake Storage Gen2 中。

Azure Data Lake Storage Gen1 将于 2024 年 2 月 29 日停用。 有关详细信息，请查看[官方公告](https://azure.microsoft.com/updates/action-required-switch-to-azure-data-lake-storage-gen2-by-29-february-2024/)。 如果使用 Azure Data Lake Storage Gen1，请确保在该日期之前迁移到 Azure Data Lake Storage Gen2。 本文介绍如何执行该操作。

‎Azure Data Lake Storage Gen2 以 [Azure Blob 存储](storage-blobs-introduction.md)为基础而构建，能够提供一组专用于大数据分析的功能。 [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) 将 [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) 中的功能（例如文件系统语义、目录、文件级安全性和规模）与 [Azure Blob 存储](storage-blobs-introduction.md)中的低成本分层存储、高可用性/灾难恢复功能进行了组合。

> [!NOTE]
> 为了便于阅读，本文使用术语 Gen1 来指代 Azure Data Lake Storage Gen1，并使用术语 Gen2 来指代 Azure Data Lake Storage Gen2 。

## <a name="recommended-approach"></a>推荐的方法

若要迁移到 Gen2，我们建议采用以下方法。

:heavy_check_mark: 步骤1：评估准备情况

:heavy_check_mark: 步骤2：准备进行迁移

:heavy_check_mark: 步骤3：迁移数据和应用程序工作负载

:heavy_check_mark: 步骤4：从 Gen1 转换为 Gen2

> [!NOTE]
> Gen1 和 Gen2 是不同的服务，没有就地升级体验，需要进行有意的迁移工作。 

### <a name="step-1-assess-readiness"></a>步骤1：评估准备情况

1. 了解 [Data Lake Storage Gen2 产品/服务](https://azure.microsoft.com/services/storage/data-lake-storage/)：它的优势、成本和常规体系结构。 

2. 将 Gen1 的功能与 Gen2 的功能[进行比较](#gen1-gen2-feature-comparison)。 

3. 查看[已知问题](data-lake-storage-known-issues.md)的列表，评估功能间的任何差距。

4. Gen2 支持[诊断日志记录](../common/storage-analytics-logging.md)、[访问层](storage-blob-storage-tiers.md)和 [Blob 存储生命周期管理策略](storage-lifecycle-management-concepts.md)等 Blob 存储功能。 如果你对使用其中任何一种功能感兴趣，请查看[当前支持级别](./data-lake-storage-supported-blob-storage-features.md)。

5. 查看 [Azure 生态系统支持](./data-lake-storage-multi-protocol-access.md)的当前状态，确保 Gen2 支持解决方案所依赖的任何服务。

### <a name="step-2-prepare-to-migrate"></a>步骤 2：准备进行迁移

1. 确定要迁移的数据集。

   利用此机会清理不再使用的数据集。 除非计划一次迁移所有数据，否则请花些时间来确定可以分阶段迁移的数据逻辑组。
   
2. 确定迁移将对你的业务造成的影响。

   例如，请考虑进行迁移时是否可以承受任何停机时间。 这些注意事项可以帮助你确定适当的迁移模式，并选择最合适的工具。

3. 创建迁移计划。 

   建议采用这些[迁移模式](#migration-patterns)。 可以选择其中一种模式，将它们组合在一起，或者设计自己的自定义模式。

### <a name="step-3-migrate-data-workloads-and-applications"></a>步骤 3：迁移数据、工作负载和应用程序

使用你喜欢的模式迁移数据、工作负载和应用程序。 建议以增量方式验证方案。

1. [创建存储帐户](create-data-lake-storage-account.md)启用分层命名空间功能。 

2. 迁移数据。 

3. 将[工作负载中的服务](./data-lake-storage-supported-azure-services.md)配置为指向 Gen2 终结点。 
   
4. 更新应用程序以使用 Gen2 API。 请参阅以下指南：

| 环境 | 项目 |
|--------|-----------|
|Azure 存储资源管理器 |[使用 Azure 存储资源管理器管理 Azure Data Lake Storage Gen2 中的目录、文件](data-lake-storage-explorer.md)|
|.NET |[使用 .NET 管理 Azure Data Lake Storage Gen2 中的目录和文件](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[使用 Java 管理 Azure Data Lake Storage Gen2 中的目录和文件](data-lake-storage-directory-file-acl-java.md)|
|Python|[使用 Python 管理 Azure Data Lake Storage Gen2 中的目录和文件](data-lake-storage-directory-file-acl-python.md)|
|JavaScript (Node.js)|[使用 Node.js 中的 JavaScript SDK 管理 Azure Data Lake Storage Gen2 中的目录和文件](data-lake-storage-directory-file-acl-javascript.md)|
|REST API |[Azure Data Lake Store REST API](/rest/api/storageservices/data-lake-storage-gen2)|
   
5. 更新脚本以使用 Data Lake Storage Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md) [Azure CLI 命令](data-lake-storage-directory-file-acl-cli.md)。
   
6. 搜索代码文件、Databricks 笔记本、Apache Hive HQL 文件或其他任何用作工作负载一部分的文件中包含字符串 `adl://` 的 URI 引用。 将这些引用替换为新存储帐户的 [Gen2 格式 URI](data-lake-storage-introduction-abfs-uri.md)。 例如，Gen1 URI：`adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` 可能会变为 `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`。 

7. 在帐户上配置安全性，以包括 [Azure 角色](assign-azure-role-data-access.md)、[文件和文件夹级别安全性](data-lake-storage-access-control.md)，以及 [Azure 存储防火墙和虚拟网络](../common/storage-network-security.md)。

### <a name="step-4-cutover-from-gen1-to-gen2"></a>步骤 4：从 Gen1 转换为 Gen2

确信应用程序和工作负载在 Gen2 上稳定后，可以开始使用 Gen2 来满足业务场景。 关闭 Gen1 上运行的任何剩余管道，并停用 Gen1 帐户。 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 与 Gen2 功能比较

此表将 Gen1 的功能与 Gen2 的功能进行了比较。

|区域 |Gen1   |Gen2 |
|---|---|---|
|数据组织|[分层命名空间](data-lake-storage-namespace.md)<br>文件和文件夹支持|[分层命名空间](data-lake-storage-namespace.md)<br>容器、文件和文件夹支持 |
|异地冗余| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage)、[ZRS](../common/storage-redundancy.md#zone-redundant-storage)、[GRS](../common/storage-redundancy.md#geo-redundant-storage)、[RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|身份验证|[AAD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)<br>[共享访问密钥](/rest/api/storageservices/authorize-with-shared-key)|
|授权|管理 - [Azure RBAC](../../role-based-access-control/overview.md)<br>数据 - [ACL](data-lake-storage-access-control.md)|管理 - [Azure RBAC](../../role-based-access-control/overview.md)<br>数据 - [ACL](data-lake-storage-access-control.md)、[Azure RBAC](../../role-based-access-control/overview.md) |
|加密 - 静态数据|服务器端 - 使用 [Microsoft 管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)或[客户管理](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)的密钥|服务器端 - 使用 [Microsoft 管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)或[客户管理](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)的密钥|
|VNET 支持|[VNET 集成](../../data-lake-store/data-lake-store-network-security.md)|[服务终结点](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)、[专用终结点](../common/storage-private-endpoints.md)|
|开发人员体验|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md)、[.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md)、[Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md)、[Python](../../data-lake-store/data-lake-store-data-operations-python.md)、[PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)、[Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|公开发布 - [REST](/rest/api/storageservices/data-lake-storage-gen2)、[.NET](data-lake-storage-directory-file-acl-dotnet.md)、[Java](data-lake-storage-directory-file-acl-java.md)、[Python](data-lake-storage-directory-file-acl-python.md)<br>公共预览版 - [JavaScript](data-lake-storage-directory-file-acl-javascript.md)、[PowerShell](data-lake-storage-directory-file-acl-powershell.md)、[Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|资源日志|经典日志<br>[已集成 Azure Monitor](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[经典日志](../common/storage-analytics-logging.md) - 正式发布<br>[已集成 Azure Monitor](monitor-blob-storage.md) - 预览版|
|生态系统|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)、[Azure Databricks（3.1 及更高版本）](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html)、[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)、[ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight（3.6、4.0）](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)、[Azure Databricks（5.1 及更高版本）](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)、[Azure Synapse Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md)、[ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Gen1 到 Gen2 的模式

选择迁移模式，并根据需要修改该模式。

|迁移模式 | 详细信息 |
|---|---|
|**直接迁移**|最简单的模式。 如果数据管道可以承受停机，这是理想选择。|
|**增量复制**|类似于直接迁移，但停机时间更少。 非常适合需要较长时间才能复制的大量数据。|
|**双管道**|非常适合无法承受任何停机时间的管道。|
|**双向同步**|类似于双管道，但具有更具阶段性的方法，适用于更复杂的管道。|

我们来详细了解每种模式。
 
### <a name="lift-and-shift-pattern"></a>直接迁移模式

这是最简单的模式。 

1. 停止对 Gen1 的所有写入操作。

2. 将数据从 Gen1 移动到 Gen2。 建议使用 [Azure 数据工厂](../../data-factory/connector-azure-data-lake-storage.md)，或使用 [Azure 门户](data-lake-storage-migrate-gen1-to-gen2-azure-portal.md)。 ACL 随数据一起复制。

3. 将引入操作和工作负载指向 Gen2。

4. 停用 Gen1。

请查看[直接迁移示例](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Lift%20and%20Shift/README.md)中的直接迁移模式示例代码。

> [!div class="mx-imgBorder"]
> ![直接迁移模式](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>使用直接迁移模式的注意事项

:heavy_check_mark: 同时将所有工作负载从 Gen1 转换到 Gen2。

:heavy_check_mark: 在迁移和转换期间预计会停机。

:heavy_check_mark: 非常适合能够承受停机时间并且可以一次升级所有应用的管道。

> [!TIP]
> 请考虑使用[Azure 门户](data-lake-storage-migrate-gen1-to-gen2-azure-portal.md)缩短停机时间，并减少完成迁移所需的步骤数。

### <a name="incremental-copy-pattern"></a>增量复制模式

1. 开始将数据从 Gen1 移动到 Gen2。 建议使用 [Azure 数据工厂](../../data-factory/connector-azure-data-lake-storage.md)。 ACL 随数据一起复制。

2. 以增量方式复制 Gen1 中的新数据。

3. 复制所有数据后，停止对 Gen1 的所有写入操作，并将工作负载指向 Gen2。

4. 停用 Gen1。

请查看[增量复制迁移示例](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Incremental/README.md)中的增量复制模式示例代码。


> [!div class="mx-imgBorder"]
> ![增量复制模式](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>使用增量复制模式时的注意事项：

:heavy_check_mark: 同时将所有工作负载从 Gen1 转换到 Gen2。

:heavy_check_mark: 预计仅会在转换期间停机。

:heavy_check_mark: 非常适合一次升级所有应用的管道，但数据复制需要更多时间。

### <a name="dual-pipeline-pattern"></a>双管道模式

1. 将数据从 Gen1 移动到 Gen2。 建议使用 [Azure 数据工厂](../../data-factory/connector-azure-data-lake-storage.md)。 ACL 随数据一起复制。

2. 将新数据引入 Gen1 和 Gen2。

3. 将工作负载指向 Gen2。

4. 停止对 Gen1 的所有写入操作，并停用 Gen1。

请查看[双管道迁移示例](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Dual%20pipeline/README.md)中双管道模式的示例代码。

> [!div class="mx-imgBorder"]
> ![双管道模式](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>使用双管道模式时的注意事项：

:heavy_check_mark: Gen1 和 Gen2 管道并行运行。

:heavy_check_mark: 支持零停机时间。

:heavy_check_mark: 当工作负载和应用程序无法承受任何停机时间，并且你可以引入两个存储帐户时，这是理想选择。

### <a name="bi-directional-sync-pattern"></a>双向同步模式

1. 在 Gen1 和 Gen2 之间设置双向复制。 建议使用 [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)。 它为现有数据提供了修复功能。

3. 完成所有移动后，停止对 Gen1 的所有写入操作并关闭双向复制。

4. 停用 Gen1。

请查看[双向同步迁移示例](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Bi-directional/README.md)中双向同步模式的示例代码。

> [!div class="mx-imgBorder"]
> ![双向模式](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>使用双向同步模式时的注意事项：

:heavy_check_mark: 非常适用于涉及大量管道和依赖项的复杂方案，其中分阶段方法可能更有意义。  

:heavy_check_mark: 迁移工作量很大，但它为 Gen1 和 Gen2 提供并行支持。

## <a name="next-steps"></a>后续步骤

- 了解为存储帐户设置安全性的各个部分。 请参阅 [Azure 存储安全指南](./security-recommendations.md)。
- 优化 Data Lake Store 的性能。 请参阅[优化 Azure Data Lake Storage Gen2 性能](data-lake-storage-performance-tuning-guidance.md)。
- 查看管理 Data Lake Store 的最佳做法。 请参阅[使用 Azure Data Lake Storage Gen2 的最佳做法](data-lake-storage-best-practices.md)