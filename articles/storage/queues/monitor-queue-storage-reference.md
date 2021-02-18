---
title: Azure 队列存储监视数据引用
description: 用于监视 Azure 队列存储中的数据的日志和指标参考。
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 10/02/2020
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 95f20737b044140fe12ea939e71cd2397cb4826d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576687"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Azure 队列存储监视数据引用

请参阅[监视 Azure 存储](monitor-queue-storage.md)，详细了解如何收集和分析 Azure 存储的监视数据。

## <a name="metrics"></a>指标

以下各表列出了为 Azure 存储收集的平台指标。

### <a name="capacity-metrics"></a>容量度量值

容量指标值每日刷新 (最多24小时) 。 时间粒度定义呈现指标值的时间间隔。 所有容量指标的受支持时间粒度为一小时 (PT1H)。

Azure 存储在 Azure Monitor 中提供以下容量指标。

#### <a name="account-level-capacity-metrics"></a>帐户级别容量指标

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>队列存储指标

此表显示了 [队列存储指标](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)。

| 指标 | 说明 |
| ------------------- | ----------------- |
| **QueueCapacity** | 存储帐户使用的队列存储量。 <br><br> 单位：`Bytes` <br> 聚合类型： `Average` <br> 值示例： `1024` |
| **QueueCount** | 存储帐户中的队列数目。 <br><br> 单位：`Count` <br> 聚合类型： `Average` <br> 值示例： `1024` |
| **QueueMessageCount** | 存储帐户中队列消息的大致数目。 <br><br> 单位：`Count` <br> 聚合类型： `Average` <br> 值示例： `1024` |

### <a name="transaction-metrics"></a>事务指标

从 Azure 存储到 Azure Monitor 的每个存储帐户请求都会发出事务指标。 如果存储帐户中没有任何活动，则在此期间不会有关于事务指标的数据。 所有事务指标均可在帐户和队列存储服务级别上使用。 时间粒度定义呈现指标值的时间间隔。 所有事务指标的受支持时间粒度为 PT1H 和 PT1M。

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>指标维度

Azure 存储支持对 Azure Monitor 中的指标使用以下维度。

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>资源日志（预览版）

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中用于预览测试。 此预览版为常规用途 v1 和常规用途 v2 存储帐户中的 Blob（包括 Azure Data Lake Storage Gen2）、文件、队列、表和高级存储帐户启用日志。 不支持经典存储帐户。

下表列出了在 Azure Monitor 日志或 Azure 存储中收集 Azure 存储资源日志时这些资源日志的属性。 属性描述了操作、服务以及用来执行该操作的授权类型。

### <a name="fields-that-describe-the-operation"></a>描述操作的字段

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>描述如何对操作进行身份验证的字段

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>描述服务的字段

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>另请参阅

- 有关监视 Azure 队列存储的说明，请参阅 [监视 Azure 队列存储](monitor-queue-storage.md) 。
- 有关监视 Azure 资源的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](../../azure-monitor/essentials/monitor-azure-resource.md)。
