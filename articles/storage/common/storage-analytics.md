---
title: 使用 Azure 存储分析收集日志和指标数据 | Microsoft Docs
description: 使用存储分析，可以跟踪所有存储服务的指标数据，并收集 Blob、队列和表存储的日志。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 01283dd9607dbe3f9b5b0c2103345cb73d56e093
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128579508"
---
# <a name="storage-analytics"></a>存储分析

Azure 存储分析执行日志记录并为存储帐户提供度量值数据。 可以使用此数据为存储帐户跟踪请求、分析使用趋势和诊断问题。

若要使用存储分析，必须为每个要监视的服务单独启用它。 可以从 [Azure 门户](https://portal.azure.com)中启用它。 有关详细信息，请参阅[在 Azure 门户中监视存储帐户](./manage-storage-analytics-logs.md)。 还可以通过 REST API 或客户端库以编程方式启用存储分析。 使用[设置 Blob 服务属性](/rest/api/storageservices/set-blob-service-properties)、[设置队列服务属性](/rest/api/storageservices/set-queue-service-properties)、[设置表服务属性](/rest/api/storageservices/set-table-service-properties)和[设置文件服务属性](/rest/api/storageservices/Get-File-Service-Properties)操作，为每个服务启用存储分析。

聚合数据存储在众所周知的 Blob（用于日志记录）和众所周知的表（用于度量）中，可以使用 BLOB 服务和表服务 API 对其进行访问。

存储分析针对存储的数据量实施 20 TB 的限制，这与存储帐户的总限制无关。 有关存储帐户限制的详细信息，请参阅[标准存储帐户的可伸缩性和性能目标](scalability-targets-standard-account.md)。

有关使用存储分析及其他工具来识别、诊断和排查 Azure 存储相关问题的深入指导，请参阅[监视、诊断和排查 Microsoft Azure 存储问题](storage-monitoring-diagnosing-troubleshooting.md)。

## <a name="billing-for-storage-analytics"></a>存储分析计费

所有度量数据是由存储帐户服务写入的。 因此，存储分析执行的每个写入操作都是计费的。 此外，度量数据使用的存储量也是计费的。

存储分析执行的以下操作都是计费的：

- 为日志记录创建 Blob 的请求。
- 为度量创建表实体的请求。

如果已配置数据保留策略，则可以通过删除旧的日志记录和指标数据来减少支出。 有关保留策略的详细信息，请参阅[设置存储分析数据保留策略](/rest/api/storageservices/Setting-a-Storage-Analytics-Data-Retention-Policy)。

### <a name="understanding-billable-requests"></a>了解计费请求

向帐户的存储服务发出的每个请求是应计费或不计费的。 存储分析记录向服务发出的每个请求，包括指示如何处理请求的状态消息。 同样，存储分析存储服务及其 API 操作的度量数据，包括某些状态消息的百分比和计数。 总之，这些功能可以帮助分析计费请求，对应用程序进行改进，以及诊断向服务发出的请求的问题。 有关计费的详细信息，请参阅 [Understanding Azure Storage Billing - Bandwidth, Transactions, and Capacity](/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity)（了解 Azure 存储计费 - 带宽、事务和容量）。

查看存储分析数据时，可以使用[存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)主题中的表来确定计费的请求。 然后，可以将日志和指标数据与状态消息进行比较，以查看是否对你的特定请求进行收费。 也可以使用前述主题中的表来调查存储服务或各个 API 操作的可用性。

## <a name="next-steps"></a>后续步骤

- [在 Azure 门户中监视存储帐户](./manage-storage-analytics-logs.md)
- [存储分析度量值](storage-analytics-metrics.md)
- [存储分析日志记录](storage-analytics-logging.md)
