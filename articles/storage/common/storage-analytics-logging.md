---
title: Azure 存储分析日志记录
description: 使用存储分析记录有关 Azure 存储请求的详细信息。 了解记录了哪些请求、如何存储日志，如何启用存储日志记录，等等。
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: d15a3c20be365f994f219e5ea74c7aee5b0c5b28
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560404"
---
# <a name="azure-storage-analytics-logging"></a>Azure 存储分析日志记录

存储分析记录成功和失败的存储服务请求的详细信息。 可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。 这意味着，大多数请求将生成日志记录，但不保证存储分析日志的完整性和及时性。

> [!NOTE]
> 建议你使用 Azure Monitor 中的 Azure 存储日志，而不是存储分析日志。 Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中进行预览测试。 此预览版支持 blob（包括 Azure Data Lake Storage Gen2）、文件、队列和表日志。 若要了解详细信息，请参阅以下任一文章：
>
> - [监视 Azure Blob 存储](../blobs/monitor-blob-storage.md)
> - [监视 Azure 文件存储](../files/storage-files-monitoring.md)
> - [监视 Azure 队列存储](../queues/monitor-queue-storage.md)
> - [监视 Azure 表存储](../tables/monitor-table-storage.md)

 默认未对存储帐户启用存储分析日志记录。 可以在 [Azure 门户](https://portal.azure.com/)中启用它，也可以使用 PowerShell 或 Azure CLI 启用它。 有关分步指南，请参阅[启用和管理 Azure 存储分析日志（经典）](manage-storage-analytics-logs.md)。

还可以通过 REST API 或客户端库以编程方式启用存储分析日志。 使用[获取 Blob 服务属性](/rest/api/storageservices/Blob-Service-REST-API)、[获取队列服务属性](/rest/api/storageservices/Get-Queue-Service-Properties)和[获取表服务属性](/rest/api/storageservices/Get-Table-Service-Properties)操作为每个服务启用存储分析。 若要查看使用 .NET 启用存储分析日志的示例，请参阅[启用日志](manage-storage-analytics-logs.md)

 仅在针对服务终结点发出请求时才会创建日志条目。 例如，如果存储帐户的 Blob 终结点中存在活动，而表或队列终结点中没有该活动，则仅创建与 Blob 服务有关的日志。

> [!NOTE]
>  存储分析日志记录现仅可用于 Blob、队列和表服务。 存储分析日志记录也可用于高性能 [BlockBlobStorage](./storage-account-create.md) 帐户。 但是，它不适用于具有高级性能的常规用途 v2 帐户。

## <a name="requests-logged-in-logging"></a>日志记录中记录的请求


### <a name="logging-authenticated-requests"></a>记录经过身份验证的请求

 将记录以下类型的已经过身份验证的请求：

- 成功的请求
- 失败的请求，包括超时、限制、网络、授权和其他错误
- 使用共享访问签名 (SAS) 或 OAuth 的请求，包括失败和成功的请求
- 分析数据请求

  不会记录存储分析本身发出的请求，如创建或删除日志。 [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)主题中提供了所记录数据的完整列表。

### <a name="logging-anonymous-requests"></a>记录匿名请求

 记录以下类型的匿名请求：

- 成功的请求
- 服务器错误
- 客户端和服务器的超时错误
- 失败的 GET 请求，错误代码为 304（未修改）

  不会记录所有其他失败的匿名请求。 [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)主题中提供了所记录数据的完整列表。

> [!NOTE]
> 存储分析将所有内部调用记录到数据平面。 还会记录来自 Azure 存储资源提供程序的调用。 若要标识这些请求，请在请求 URL 中查找查询字符串 `<sk=system-1>`。

## <a name="how-logs-are-stored"></a>如何存储日志

所有日志均以块 blob 的形式存储在一个名为 `$logs` 的容器中，为存储帐户启用存储分析时将自动创建该容器。 `$logs` 容器位于存储帐户的 blob 命名空间中，例如：`http://<accountname>.blob.core.windows.net/$logs`。 在启用存储分析后，无法删除该容器，但可以删除其内容。 如果使用存储浏览工具直接导航到容器，将看到包含日志记录数据的所有 blob。

> [!NOTE]
>  执行容器列出操作（例如列出容器操作）时，不会显示 `$logs` 容器。 必须直接访问该容器。 例如，可以使用列出 blob 操作访问 `$logs` 容器中的 blob。

在记录请求时，存储分析将中间结果作为块进行上传。 存储分析定期提交这些块，并将其作为 Blob 提供。 根据存储服务刷新日志编写器的频率，日志数据可能需要长达一小时的时间，才能显示在 $logs 容器的 blob 中。 在同一小时内创建的日志中可能存在重复的记录。 可以通过检查 RequestId 和操作编号确定记录是否为重复记录。 

如果你每小时都有包含多个文件的大量日志数据，则可检查 blob 元数据字段，以便使用 blob 元数据确定日志中包含的数据。 这样做也是很有用的，因为将数据写入日志文件时，有时可能存在延迟：使用 blob 元数据与使用 blob 名称相比，可以更准确地指示 blob 内容。

你可以使用大多数存储浏览工具查看 blob 的元数据；你也可以通过 PowerShell 或编程方式读取此信息。 下面的 PowerShell 代码片段是一个示例，该示例说明了如何按名称筛选日志 blob 的列表来指定时间，以及如何按元数据进行筛选以仅标识包含“写入”操作的日志。

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'blob/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```

有关以编程方式列出 blob 的信息，请参阅[枚举 blob 资源](/rest/api/storageservices/Enumerating-Blob-Resources)以及[设置并检索 blob 资源的属性和元数据](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources)。

### <a name="log-naming-conventions"></a>日志命名约定

 每个日志都使用以下格式写入：

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 下表说明了日志名称中的每个属性：

|属性|说明|
|---------------|-----------------|
|`<service-name>`|存储服务的名称 例如，`blob`、`table` 或 `queue`|
|`YYYY`|用四位数表示的日志年份。 例如： `2011`|
|`MM`|用两位数表示的日志月份。 例如： `07`|
|`DD`|用两位数表示的日志日期。 例如： `31`|
|`hh`|用两位数表示的日志起始小时，采用 24 小时 UTC 格式。 例如： `18`|
|`mm`|用两位数表示的日志起始分钟。 **注意：** 最新的存储分析版本中不支持该值，其值始终为 `00`。|
|`<counter>`|从零开始且具有六位数字的计数器，表示在 1 小时内为存储服务生成的日志 Blob 数。 此计数器从 `000000` 开始。 例如： `000001`|

 下面是组合上述示例的完整示例日志名称：

 `blob/2011/07/31/1800/000001.log`

 下面是一个可用于访问上述日志的示例 URI：

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 在记录存储请求时，生成的日志名称与完成请求的操作时间（小时）关联。 例如，如果在 2011 年 7 月 31 日下午 6:30 完成 GetBlob 请求，则会写入具有以下前缀的日志：`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>日志元数据

 所有日志 Blob 与可用于确定 Blob 包含哪些日志记录数据的元数据一起存储。 下表说明了每个元数据属性：

|属性|说明|
|---------------|-----------------|
|`LogType`|描述日志是否包含与读取、写入或删除操作有关的信息。 该值可能包含一种类型，也可能包含所有三种类型的组合并用逗号隔开。<br /><br /> 示例 1：`write`<br /><br /> 示例 2：`read,write`<br /><br /> 示例 3：`read,write,delete`|
|`StartTime`|日志中条目的最早时间，采用 `YYYY-MM-DDThh:mm:ssZ` 形式。 例如： `2011-07-31T18:21:46Z`|
|`EndTime`|日志中条目的最晚时间，采用 `YYYY-MM-DDThh:mm:ssZ` 形式。 例如： `2011-07-31T18:22:09Z`|
|`LogVersion`|日志格式的版本。|

 下表显示了使用上述示例的完整示例元数据：

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="next-steps"></a>后续步骤

- [启用和管理 Azure 存储分析日志（经典）](manage-storage-analytics-logs.md)
- [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format)（存储分析日志格式）
- [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
- [存储分析指标（经典）](storage-analytics-metrics.md)
