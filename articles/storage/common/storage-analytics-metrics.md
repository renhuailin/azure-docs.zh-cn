---
title: Azure 存储分析指标（经典）
description: 了解如何在 Azure 存储中使用存储分析指标。 了解事务和容量指标、存储指标的方式、启用指标的方式，等等。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 63c1e21b95971abf74abe47e07619c257f13e499
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114665100"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure 存储分析指标（经典）

存储分析指标（也称为经典指标）将于 2023 年 8 月 31 日停用。 有关详细信息，请查看[官方公告](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/)。 如果使用经典指标，请确保在该日期之前转换到 Azure Monitor 中的指标。 本文可帮助你进行转换。 

Azure 存储使用存储分析解决方案存储一些指标，这些指标包括有关存储服务请求的聚合事务统计信息和容量数据。 事务在 API 操作级别以及存储服务级别报告。 容量在存储服务级别报告。 指标数据可用于：
- 分析存储服务使用情况。
- 通过针对存储服务发出的请求诊断问题。
- 提高使用服务的应用程序的性能。

 默认为新存储帐户启用存储分析指标。 你可以使用 PowerShell 或 Azure CLI 在 [Azure 门户](https://portal.azure.com/)中配置指标。 有关分步指南，请参阅[启用和管理 Azure 存储分析指标（经典）](./manage-storage-analytics-logs.md)。 还可以通过 REST API 或客户端库以编程方式启用存储分析。 使用“获取服务属性”操作为每项服务启用存储分析。  

> [!NOTE]
> 存储分析指标可用于 Azure Blob 存储、Azure 队列存储、Azure 表存储以及 Azure 文件。
> 存储分析指标现在是经典指标。 建议你使用 [Azure Monitor 中的存储指标](../blobs/monitor-blob-storage.md)，而不是存储分析指标。

## <a name="transaction-metrics"></a>事务指标  
 对于每个存储服务和请求的 API 操作，将按小时或分钟为间隔记录一组可靠的数据，其中包括入口和出口、可用性、错误和分类请求百分比。 有关事务详细信息的完整列表，请参阅[存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema)。  

 事务数据在服务级别和 API 操作级别记录。 在服务级别，汇总所有请求的 API 操作的统计信息将每小时写入一次表实体，即使未向服务发出请求也是如此。 在 API 操作级别，仅当在该小时内请求操作时才将统计信息写入实体。  

 例如，如果对 Blob 服务执行 GetBlob 操作，则存储分析指标将记录请求并将其包含在 Blob 服务以及 GetBlob 操作的聚合数据中。 如果在一小时内未请求 GetBlob 操作，则不会将实体写入该操作的 $MetricsTransactionsBlob。  

 会为用户请求和存储分析本身发出的请求记录事务指标。 例如，存储分析写入日志和表实体的请求会被记录。

## <a name="capacity-metrics"></a>容量度量值  

> [!NOTE]
>  目前，容量指标仅适用于 blob 服务。

 每天记录存储帐户的 Blob 服务的容量数据，并写入两个表实体。 一个条目提供用户数据的统计信息，另一个条目提供有关存储分析所使用的 `$logs` Blob 容器的统计信息。 *$MetricsCapacityBlob* 表包含以下统计信息：  

- **容量**：存储帐户的 Blob 服务使用的存储量（以字节为单位）。  
- **ContainerCount**：存储帐户的 Blob 服务中的 blob 容器数。  
- **ObjectCount**：存储帐户的 Blob 服务中已提交和未提交的块 blob 或页 blob 数量。  

  有关容量指标的详细信息，请参阅[存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema)。  

## <a name="how-metrics-are-stored"></a>如何存储指标  

 每个存储服务的所有指标数据都存储在为该服务保留的三个表中。 一个表存储事务信息，一个表存储分钟事务信息，还有一个表存储容量信息。 事务和分钟事务信息由请求和响应数据组成。 容量信息由存储使用情况数据组成。 存储帐户的 Blob 服务的小时指标、分钟指标和容量可在按下表所述命名的表中访问。  

|指标级别|表名|支持的版本|  
|-------------------|-----------------|----------------------------|  
|小时指标，主位置|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|仅 2013 年 8 月 15 日之前的版本。 虽然仍然支持这些名称，但还是建议改用下面列出的表。|  
|小时指标，主位置|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|所有版本。 仅在 2015 年 4 月 5 日版及更高版本中提供对文件服务指标的支持。|  
|分钟指标，主位置|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|所有版本。 仅在 2015 年 4 月 5 日版及更高版本中提供对文件服务指标的支持。|  
|小时指标，辅助位置|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|所有版本。 必须启用读访问的地域冗余复制。|  
|分钟指标，辅助位置|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|所有版本。 必须启用读访问的地域冗余复制。|  
|容量（仅限 Blob 服务）|$MetricsCapacityBlob|所有版本。|  

 为存储服务终结点启用存储分析时，会自动创建这些表。 这些指标可通过存储帐户的命名空间进行访问，例如：`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`。 指标表不会显示在列表操作中，必须直接通过表名称进行访问。

## <a name="metrics-alerts"></a>度量警报
考虑在 [Azure 门户](https://portal.azure.com)中设置警报，以在存储服务行为发生重要更改时自动通知你。 有关分步指南，请参阅[创建指标警报](./manage-storage-analytics-logs.md)。

如果使用存储资源管理器工具下载这种采用分隔格式的指标数据，则可以使用 Microsoft Excel 分析数据。 有关可用存储资源管理器工具的列表，请参阅 [Azure 存储客户端工具](./storage-explorers.md)。

> [!IMPORTANT]
> 在存储事件与记录对应每小时或分钟指标数据的时间之间可能存在延迟。 对于分钟指标，可能会一次写入几分钟的数据。 此问题可能会导致将前面几分钟的事务聚合到当前分钟的事务中。 发生此问题时，警报服务可能没有已配置警报间隔内的所有可用指标数据，这可能会导致意外触发警报。
>

## <a name="billing-on-storage-metrics"></a>对存储指标计费
为度量值创建表实体的写入请求，按适用于所有 Azure 存储操作的标准费率收费。  

客户端针对指标数据的读取请求也按标准费率收费。 

指标表使用的容量也要付费。 使用以下信息估算用于存储指标数据的容量：  

-   如果某个服务每小时都会利用每个服务的每个 API，则在启用服务级别和 API 级别摘要的情况下，每小时约有 148KB 的数据存储在指标事务表中。  
-   如果某个服务每小时都会利用服务的每个 API，则在仅启用服务级别摘要的情况下，每小时约有 12KB 的数据存储在指标事务表中。  
-   Blob 的容量表每天添加两行（如果你已为日志选择加入）。 这表示，此表的大小每天最多以约 300 字节的幅度增加。

## <a name="next-steps"></a>后续步骤
* [监视存储帐户](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [存储分析日志记录](storage-analytics-logging.md)
