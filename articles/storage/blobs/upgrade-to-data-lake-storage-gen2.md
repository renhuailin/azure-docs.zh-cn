---
title: 将 Azure Blob 存储升级到 Azure Data Lake Storage Gen2 | Microsoft Docs
description: 此处显示说明。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: normesta
ms.openlocfilehash: 7f5c2397e2b8d6e19e912dd46eec39f432e27434
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129459025"
---
# <a name="upgrading-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>升级具有 Azure Data Lake Storage Gen2 功能的 Azure Blob 存储

本文可帮助你解锁文件和目录级安全性和更快的操作等功能。 这些功能广泛用于大数据分析工作负荷，并统称为 Azure Data Lake Storage Gen2。 最常见的功能包括：

- 更高的吞吐量、每秒输入/输出操作数 (IOPS) 和存储容量限制。

- 更快的操作（例如重命名操作），因为你可以对单个节点 URI 进行操作。
 
- 有效的查询引擎，其只传输执行给定操作所需的数据。

- 容器、目录和文件级安全性。

若要了解详细信息，请参阅 [Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)。

本文将帮助你评估对工作负荷、应用程序、成本、服务集成、工具、功能和文档的影响。 请确保仔细查看这些影响。 如果已准备好升级帐户，请参阅此分步指南：[使用 Azure Data Lake Storage Gen2 功能升级 Azure Blob 存储](upgrade-to-data-lake-storage-gen2-how-to.md)。

> [!IMPORTANT]
> 升级是单向的。 执行升级后，无法还原帐户。 建议在非生产环境中验证升级。

## <a name="impact-on-availability"></a>对可用性的影响

请确保在升级过程完成时，在帐户中计划一些故障时间。 帐户升级时，将禁用写入操作。 读取操作未被禁用，但我们强烈建议你挂起读取操作，因为这些操作可能会使升级过程不稳定。

## <a name="impact-on-workloads-and-applications"></a>对工作负荷和应用程序的影响

Blob API 适用于具有分层命名空间的帐户，因此，使用这些 API 与你的帐户进行交互的大多数应用程序将继续正常工作，无需修改。

有关问题和解决方法的完整列表，请参阅 [Blob 存储 API 的已知问题](data-lake-storage-known-issues.md#blob-storage-apis)。

必须修改使用 Windows Azure 存储 Blob 驱动程序或 [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) 驱动程序的任何 Hadoop 工作负荷，以使用 [Azure Blob 文件系统 (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) 驱动程序。 不同于对 Blob 服务终结点发出请求的 WASB 驱动程序，ABFS 驱动程序将向帐户的 Data Lake Storage 终结点发出请求。

### <a name="data-lake-storage-endpoint"></a>Data Lake 存储终结点

升级后的帐户将具有 Data Lake 存储终结点。 可在 Azure 门户中打开帐户的“属性”页，找到此终结点的 URL。

> [!div class="mx-imgBorder"]
> ![常规用途 v2 类别](./media/upgrade-to-data-lake-storage-gen2/data-lake-storage-endpoint.png)

无需修改现有应用程序和工作负载即可使用该终结点。 [Data Lake Storage 中的多协议访问](data-lake-storage-multi-protocol-access.md)使你可以使用 Blob 服务终结点或 Data Lake 存储终结点来与数据进行交互。 

Azure 服务和工具（例如 AzCopy）可以使用 Data Lake 存储终结点与存储帐户中的数据进行交互。 此外，还需要将此新的终结点用于通过使用 [Data Lake Storage Gen2 SDK](data-lake-storage-directory-file-acl-dotnet.md)、[PowerShell 命令](data-lake-storage-directory-file-acl-powershell.md)或 [Azure CLI 命令](data-lake-storage-directory-file-acl-cli.md)执行的任何操作。 

### <a name="directories"></a>目录

不具有分层命名空间的 Blob 存储帐户以平面范式而不是分层范式来组织文件。 可以将 blob 组织为虚拟目录，以模拟文件夹结构。 虚拟目录构成 blob 名称的一部分，并由分隔符表示。 由于虚拟目录是 blob 名称的一部分，因此其实际上不作为独立的对象存在。

你的新帐户具有分层命名空间。 也就是说，目录不是虚拟的。 它们是可直接操作的具体、独立的对象。 目录可以存在，但不包含任何文件。 删除目录时，该目录中的所有文件都将被删除。 在该目录消失之前，你无需删除每个单独的 blob。 

### <a name="blob-metadata"></a>Blob 元数据

在迁移之前，blob 元数据与 blob 名称及其完整虚拟路径相关联。 迁移后，元数据仅与 blob 关联。 Blob 的虚拟路径将成为目录的集合。 Blob 的元数据不会应用于这些目录中的任何一个目录。 

### <a name="put-operations"></a>Put 操作

上传 blob 时，如果指定的路径包含不存在的目录，则该操作将创建该目录，然后向其中添加 blob。 此行为在分层文件夹结构的上下文中符合逻辑。 在不具有分层命名空间的 Blob 存储帐户中，该操作不会创建目录。 而是将目录名称添加到 blob 的命名空间中。 

### <a name="list-operations"></a>List 操作

[列出 blob](/rest/api/storageservices/list-blobs) 操作返回目录和文件。 每项将单独列出。 目录在列表中显示为长度为零的 Blob。 在不具有分层命名空间的 Blob 存储帐户中，[列出 Blob](/rest/api/storageservices/list-blobs)操作仅返回 blob，而不返回目录。 如果使用 Data Lake Storage Gen2 [路径 - 列表](/rest/api/storageservices/datalakestoragegen2/path/list)操作，目录将显示为目录条目，而不是长度为零的 Blob。

列表的顺序也不同。 目录和文件按深度优先搜索顺序显示。 不具有分层命名空间的 Blob 存储帐户按字典顺序列出 Blob。 

### <a name="operations-to-rename-blobs"></a>重命名 Blob 的操作

重命名 blob 要高效得多，因为客户端应用程序可以在单个操作中重命名 blob。 在不具有分层命名空间的帐户中，工具和应用程序必须复制 blob，然后删除源 blob。  

> [!NOTE]
> 重命名 blob 时，不会更新 blob 的上次修改时间。 这是因为 Blob 的内容保持不变。 

## <a name="impact-on-costs"></a>对成本的影响

存储成本不受影响，但会影响事务成本。 使用这些页面可评估比较成本。 

- [块 Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

- [Azure Data Lake Storage Gen2 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

你还可以使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)中的“存储帐户”选项来估算升级后对成本的影响。 

除了定价更改之外，请考虑与 Data Lake Storage Gen2 功能相关的成本节约。 由于吞吐量和优化操作增加，总拥有成本通常会下降。 更高的吞吐量使你可以在更短的时间内传输更多数据。 分层命名空间提高了操作效率。  

## <a name="impact-on-service-integrations"></a>对服务集成的影响

尽管在你启用这些功能后，大多数 Azure 服务集成将继续工作，但其中一些集成仍处于预览阶段或不受支持。 请参阅[支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)，了解当前是否支持 Azure 服务与 Data Lake Storage Gen2 集成。

## <a name="impact-on-tools-features-and-documentation"></a>对工具、功能和文档的影响

升级之后，与某些功能进行交互的方式将会改变。 本节介绍这些更改。

### <a name="blob-storage-feature-support"></a>Blob 存储功能支持

尽管在你启用这些功能后，大多数 Blob 存储功能都将继续工作，但其中一些功能仍处于预览阶段或不受支持。 

请参阅 [Azure Data Lake Storage Gen2 中可用的 blob 存储功能](data-lake-storage-supported-blob-storage-features.md)，以了解 Data Lake Storage Gen2 当前支持的 Blob 存储功能。 

### <a name="diagnostic-logs"></a>诊断日志

如果启用了[存储分析日志记录](../common/storage-analytics-logging.md)，则现在可以选择使用版本 2.0 日志格式。  

不需要使用此新版本。 但是，应用于 Data Lake 存储终结点的任何操作仅记录在版本 2.0 日志中。 你使用的某些服务和工具（例如 AzCopy）将使用该终结点对你的帐户执行操作。 若要确保捕获所有活动的日志记录信息，请考虑使用版本 2.0 日志格式。 

### <a name="azure-lifecycle-management"></a>Azure 生命周期管理

移动或删除目录中所有 blob 的策略在第二天之前不会删除包含这些 blob 的目录。 这是因为，只有在首次删除位于该目录中的所有 blob 之后，才能删除该目录。 第二天，将删除该目录。 

### <a name="event-grid"></a>事件网格

新帐户具有两个终结点：Data Lake 存储终结点和 Blob 服务终结点。 服务、工具和应用程序可以使用任一终结点对数据进行操作。 因此，事件网格返回的事件响应可以在描述受影响 Blob 的“url”字段中显示这两个终结点之一。 

以下 JSON 显示使用 Blob 服务终结点创建 Blob 时出现在事件响应中的 blob 的 URL。

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_BlobURL" highlight="15":::

以下 JSON 显示使用 Data Lake 存储终结点创建 Blob 时出现在事件响应中的 blob 的 URL。

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_DataLakeURL" highlight="16":::

如果应用程序使用事件网格，可能需要修改这些应用程序，以考虑这一点。 

### <a name="storage-explorer"></a>存储资源管理器

以下按钮尚未出现在 Azure 存储资源管理器的功能区中。

|Button|Reason|
|--|--|
|复制 URL|尚未实现|
|管理快照|尚未实现|
|未删除|取决于 Data Lake Storage Gen2 尚不支持的 Blob 存储功能 |


以下按钮在新帐户中的行为有所不同。

|Button|Blob 存储行为|Data Lake Storage Gen2 行为|
|---|---|---|
|文件夹|文件夹是虚拟的，如果未向其中添加文件，则文件夹会消失。 |文件夹存在，即使未向其中添加任何文件。| 
|重命名|创建一个副本，然后删除源 Blob|重命名相同的 Blob。 效率要高一些。|

### <a name="documentation"></a>文档

可在此处找到有关使用 Data Lake Storage Gen2 功能的指南：[Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)。 

有关所有现有 Blob 存储功能的指南没有任何变化。 该指南位于：[Azure Blob 存储简介](storage-blobs-introduction.md)。 

在内容集之间移动时，你会注意到术语有一些细微的差别。 例如，Data Lake Storage Gen2 内容中特别推荐的内容可能使用术语“文件”和“文件系统”，而非“Blob”和“容器”。 术语“文件”和“文件系统” 深深植根于 Data Lake Storage 具有很长历史的大数据分析的世界。 内容包含这些术语，使这些受众能够认同。 这些术语不描述单独的内容。

## <a name="next-steps"></a>后续步骤

准备好升级存储帐户以包含 Data Lake Storage Gen2 功能时，请参阅此分步指南。 
> [!div class="nextstepaction"]
> [升级具有 Azure Data Lake Storage Gen2 功能的 Azure Blob 存储](upgrade-to-data-lake-storage-gen2-how-to.md)
