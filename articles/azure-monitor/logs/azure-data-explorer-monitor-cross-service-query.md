---
title: 'Azure Monitor 和 Azure 数据资源管理器 (预览版之间跨服务查询) '
description: 若要在同一位置加入和分析所有数据，请通过 Azure Log Analytics 工具查询 Azure 数据资源管理器数据。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 7a259af17943643e722633592e53f219726c4437
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031202"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>跨服务查询-Azure Monitor 和 Azure 数据资源管理器 (预览) 
在 [Azure 数据资源管理器](/azure/data-explorer/)、 [Application Insights](../app/app-insights-overview.md)和 [Log Analytics](../logs/data-platform-logs.md)之间创建跨服务查询。
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Azure Monitor 和 Azure 数据资源管理器跨服务查询
利用这种体验，你可以在 [Azure 数据资源管理器和 Azure Monitor 之间创建跨服务查询](/azure/data-explorer/query-monitor-data) ，以及在 [Azure Monitor 与 Azure 数据资源管理器之间创建跨服务查询](./azure-monitor-data-explorer-proxy.md)。

例如，从 Log Analytics) 查询 Azure 数据资源管理器 (：
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
其中，外部查询在工作区中查询表，然后与 Azure 数据资源管理器群集中的另一个表联接 (在这种情况下，clustername = help，databasename = samples) 通过使用新的 "adx () " 函数来执行相同的操作，以便从查询文本内查询其他工作区。

> [!NOTE]
> * 现可通过直接使用 Azure 数据资源管理器客户端工具，或者通过在 Azure 数据资源管理器群集上运行查询以间接方式从 Azure 数据资源管理器中查询 Azure Monitor 数据 - 此查询功能现处于预览版模式。
> * 如有任何问题，请联系[跨服务查询](mailto:adxproxy@microsoft.com)团队。

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>从 Azure Blob 存储帐户导出 Log Analytics 数据的查询

将数据从 Azure Monitor 导出到 Azure 存储帐户可以实现低成本保留，并能够将日志重新分配到不同的区域。

使用 Azure 数据资源管理器可查询从 Log Analytics 工作区导出的数据。 配置后，从你的工作区发送到 Azure 存储帐户的受支持的表将可用作 Azure 数据资源管理器的数据源。 [使用 Azure 数据资源管理器 (预览) 从 Azure Monitor 中查询导出的数据 ](../logs/azure-data-explorer-query-storage.md)。

[来自存储流的 Azure 数据资源管理器查询](media\azure-data-explorer-query-storage\exported-data-query.png)

>[!tip] 
> * 若要将所有数据从 Log Analytics 工作区导出到 Azure 存储帐户或事件中心，请使用 Azure Monitor 日志的 Log Analytics 工作区数据导出功能。 [请参阅 Azure Monitor (预览) 中 Log Analytics 工作区数据导出 ](/azure/data-explorer/query-monitor-data)。

## <a name="next-steps"></a>后续步骤
了解有关以下方面的详细信息：
* [在 Azure 数据资源管理器和 Azure Monitor 之间创建跨服务查询](/azure/data-explorer/query-monitor-data)。 从 Azure 数据资源管理器查询 Azure Monitor 数据
* [在 Azure Monitor 和 Azure 数据资源管理器之间创建跨服务查询](./azure-monitor-data-explorer-proxy.md)。 从 Azure Monitor 中查询 Azure 数据资源管理器数据
* [ (预览) Azure Monitor Log Analytics 工作区数据导出 ](/azure/data-explorer/query-monitor-data)。 链接和查询具有 Log Analytics 导出数据的 Azure Blob 存储帐户。