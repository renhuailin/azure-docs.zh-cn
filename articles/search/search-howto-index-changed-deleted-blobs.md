---
title: 已更改的和已删除的 Blob
titleSuffix: Azure Cognitive Search
description: 在从 Azure Blob 存储导入的初始搜索索引生成后，后续索引可以只选取那些已更改或已删除的 Blob。 本文介绍详细信息。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: b23dabb4388331de9e37ee9db1d4b9d727ccde68
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430554"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>如何在 Azure 认知搜索索引中为 Blob 设置更改和删除检测

创建初始搜索索引后，你可能希望后续索引器作业仅选取新的和已更改的文档。 对于源自 Azure Blob 存储的搜索内容，当你使用计划触发索引时，将自动执行更改检测。 默认情况下，服务仅为已更改的 Blob 重新编制索引，正如由 Blob 的 `LastModified` 时间戳所确定。 与搜索索引器支持的其他数据源不同，Blob 始终具有时间戳，无需手动设置更改检测策略。

虽然更改检测是指定的，但删除检测不是。 如果要检测已删除的文档，请确保使用“软删除”方法。 如果彻底删除 Blob，相应的文档不会从搜索索引中删除。

可以通过两种方法实现软删除方法：

+ 本机 blob 软删除 (预览) ，请参阅下一步
+ [使用自定义元数据的软删除](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>本机 Blob 软删除（预览版）

对于这种删除检测方法，认知搜索依赖于 Azure Blob 存储中的 [本机 blob 软删除](../storage/blobs/soft-delete-blob-overview.md) 功能来确定 blob 是否已转换为软删除状态。 如果在此状态下检测到 blob，搜索索引器将使用此信息从索引中删除相应的文档。

> [!IMPORTANT]
> 对本机 Blob 软删除的支持目前为预览版。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2020-06-30-Preview](./search-api-preview.md) 提供此功能。 目前不支持门户或 .NET SDK。

### <a name="prerequisites"></a>先决条件

+ [为 Blob 启用软删除](../storage/blobs/soft-delete-blob-enable.md)。
+ Blob 必须位于 Azure Blob 存储容器中。 Azure Data Lake Storage Gen2 不支持认知搜索本机 blob 软删除策略。
+ 索引中文档的文档键必须映射为 blob 属性或 blob 元数据。
+ 若要配置软删除支持，必须使用预览版 REST API (`api-version=2020-06-30-Preview`) 。

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>如何使用本机软删除配置删除检测

1. 在 Blob 存储中，启用软删除时，将保留策略设置为比索引器间隔计划大得多的值。 这样，如果在运行索引器时出现问题，或者如果有大量的文档需要编制索引，可以为索引器留出大量的时间来最终处理已软删除的 Blob。 仅当 Azure 认知搜索索引器在处理处于“已软删除”状态的 Blob 时，才会从索引中删除文档。

1. 在认知搜索中，对数据源设置本机 blob 软删除检测策略。 下面显示了一个示例。 由于此功能处于预览阶段，因此必须使用预览版 REST API。

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [运行索引器](/rest/api/searchservice/run-indexer) 或将索引器设置为 [按计划](search-howto-schedule-indexers.md)运行。 当索引器运行并处理软删除状态的 blob 时，相应的搜索文档将从索引中删除。

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>使用本机软删除策略重新索引删除的 blob () 

如果在 Blob 存储中还原软删除 blob，则索引器不会始终将其重新编制索引。 这是因为，索引器使用 blob 的 `LastModified` 时间戳来确定是否需要索引。 如果删除了软删除的 blob，则 `LastModified` 不会更新其时间戳，因此，如果索引器已经处理了具有较新 `LastModified` 时间戳的 blob，则不会重新索引已删除的 blob。 

若要确保为取消删除的 Blob 重新编制索引，需要更新该 Blob 的 `LastModified` 时间戳。 为此，可以重新保存该 Blob 的元数据。 您无需更改元数据，但是重新保存元数据将更新 blob 的 `LastModified` 时间戳，以便索引器知道选择它。

## <a name="soft-delete-using-custom-metadata"></a>使用自定义元数据的软删除

此方法使用 blob 的元数据来确定是否应从索引中移除搜索文档。 此方法需要两个单独的操作：从索引中删除搜索文档，然后在 Azure 存储中删除 Blob。

Blob 存储和认知搜索中都有要遵循的步骤，但没有其他功能依赖关系。 此功能在公开发布的 Api 中受支持。

1. 将一个自定义元数据键值对属性添加到 Blob，以告知 Azure 认知搜索该 Blob 已采用逻辑方式删除。

1. 在数据源中配置软删除列检测策略。 例如，如果某个 Blob 具有值为 `true` 的元数据属性 `IsDeleted`，以下策略会将该 Blob 视为已删除：

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. 在索引器处理 Blob 并从索引中删除文档后，你可以删除 Azure Blob 存储中的 Blob。

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>使用自定义元数据重新索引删除的 blob () 

索引器处理已删除的 Blob 并从索引中删除相应的搜索文档后，如果你稍后还原该 Blob 并且 Blob 的 `LastModified` 时间戳早于上次索引器运行，则它不会重新访问该 Blob。

若要为该文档重新编制索引，请更改该 Blob 的 `"softDeleteMarkerValue" : "false"`，然后重新运行索引器。

## <a name="next-steps"></a>后续步骤

+ [Azure 认知搜索中的索引器](search-indexer-overview.md)
+ [如何配置 Blob 索引器](search-howto-indexing-azure-blob-storage.md)
+ [Blob 索引概述](search-blob-storage-integration.md)