---
title: Azure Data Lake Storage Gen2 的已知问题 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知问题。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 6fc966fc61893fb05185a15e7149e8a132aecb04
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113125881"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知问题

本文介绍 Azure Data Lake Storage Gen2 的限制和已知问题。

## <a name="supported-blob-storage-features"></a>支持的 Blob 存储功能

越来越多的 Blob 存储功能现在兼容有分层命名空间的帐户。 如需完整列表，请参阅 [Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)。

## <a name="supported-azure-service-integrations"></a>支持的 Azure 服务集成

Azure Data Lake Storage Gen2 支持多个可用于引入数据、执行分析和创建可视化表示形式的 Azure 服务。 有关受支持的 Azure 服务的列表，请参阅[支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)。

请参阅[支持 Azure Data Lake Storage Gen2 的 Azure 服务](data-lake-storage-supported-azure-services.md)。

## <a name="supported-open-source-platforms"></a>支持的开源平台

多个开源平台支持 Data Lake Storage Gen2。 有关完整列表，请参阅[支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)。

请参阅[支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)。

## <a name="blob-storage-apis"></a>Blob 存储 API

Blob API 和 Data Lake Storage Gen2 API 可以对相同的数据执行操作。

本部分介绍了同时使用 blob API 和 Data Lake Storage Gen2 API 对相同的数据执行操作时的问题和限制。

* 不能使用 blob API 和 Data Lake Storage API 将内容写入到文件的同一实例。 如果使用 Data Lake Storage Gen2 API 向某个文件进行写入，则调用[获取 Block 列表](/rest/api/storageservices/get-block-list) blob API 时看不到该文件的块。 唯一的例外是在覆盖时可以使用。 可以使用任一 API 覆盖文件/Blob。

* 如果在使用[列出 Blob](/rest/api/storageservices/list-blobs) 操作时不指定分隔符，则结果会包含目录和 Blob。 如果选择使用分隔符，请只使用正斜杠 (`/`)。 这是唯一支持的分隔符。

* 如果使用[删除 Blob](/rest/api/storageservices/delete-blob) API 来删除目录，则只能在该目录为空的情况下将其删除。 这意味着，不能使用 Blob API 以递归方式删除目录。

这些 Blob REST API 不受支持：

* [放置 Blob（页）](/rest/api/storageservices/put-blob)
* [放置页](/rest/api/storageservices/put-page)
* [获取页面范围](/rest/api/storageservices/get-page-ranges)
* [增量复制 Blob](/rest/api/storageservices/incremental-copy-blob)
* [从 URL 放置页](/rest/api/storageservices/put-page-from-url)
* [放置块列表](/rest/api/storageservices/put-block-list)

具有分层命名空间的帐户不支持非托管 VM 磁盘。 若要在存储帐户中启用分层命名空间，请将非托管 VM 磁盘置于未启用分层命名空间功能的存储帐户中。

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>支持以递归方式设置访问控制列表 (ACL)

将 ACL 更改以递归方式从父目录应用到子项的功能已正式发布。 在此功能的当前版本中，你可以使用 Azure 存储资源管理器、PowerShell、Azure CLI、.NET、Java 和 Python SDK 应用 ACL 更改。 尚不支持 Azure 门户。

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

仅使用最新版本的 AzCopy ([AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json))。 不支持早期版本的 AzCopy，例如 AzCopy v8.1。

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure 存储资源管理器

仅使用版本 `1.6.0` 或更高版本。

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Azure 门户中的存储资源管理器

尚不支持 ACL。

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>第三方应用程序

对于使用 REST API 保持正常运行的第三方应用程序，如果在 Data Lake Storage Gen2 中使用这些应用程序，则它们可继续正常运行。
调用 Blob API 的应用程序可能会正常运行。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>访问控制列表 (ACL) 和匿名读取访问

如果已将[匿名读取访问](./anonymous-read-access-configure.md)授予容器，则 ACL 对该容器或该容器中的文件没有影响。

## <a name="diagnostic-logs"></a>诊断日志

尚不支持设置数据保留天数，但可以使用任何支持的工具（例如 Azure 存储资源管理器、REST 或 SDK）手动删除日志。

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage 高级层的生命周期管理策略

无法在热层、冷层和存档层之间移动高级层中存储的数据。 但可以将数据从高级层复制到其他帐户中的热访问层。

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure Storage Blob (WASB) 驱动程序（不支持用于 Data Lake Storage Gen2）

目前，WASB 驱动程序设计为仅用于 Blob API，在一些常见场景中会遇到问题。 具体而言，当它是启用了分层命名空间的存储帐户的客户端时会遇到问题。 Data Lake Storage 上的多协议访问不会缓解这些问题。 

目前我们不支持客户使用 WASB 驱动程序作为启用了分层命名空间的存储帐户的客户端（在可预见的将来也很有可能是这种情况）。 因此，建议你选择在 Hadoop 环境中使用 [Azure Blob 文件系统 (ABFS)](data-lake-storage-abfs-driver.md) 驱动程序。 如果你在尝试迁移出采用 Hadoop branch-3 之前的版本的本地 Hadoop 环境，请创建一个 Azure 支持票证，这样我们就可以与你联系，确定适合你和你的组织的正确前进道路。

## <a name="soft-delete-for-blobs-capability-currently-in-preview"></a>Blob 功能的软删除（目前以预览版提供）

如果已对软删除的文件或目录的父目录重命名，则软删除的项可能无法在 Azure 门户中正确显示。 在这种情况下，可使用 [PowerShell](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-powershell) 或 [Azure CLI](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-azure-cli) 列出和还原软删除的项。 
