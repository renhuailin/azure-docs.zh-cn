---
title: 外部可用性错误代码 - Azure 流分析
description: 通过外部可用性错误代码排查 Azure 流分析问题。
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 71625c4b81fc0795c376a89397e98659f4c72ff0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020547"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Azure 流分析外部可用性错误代码

可以使用活动日志和资源日志来帮助调试 Azure 流分析作业中的意外行为。 本文列出了每个外部可用性错误代码的说明。 依赖服务不可用时，会发生外部可用性错误。

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **原因**：服务暂时不可用。
* **建议**：流分析将继续尝试访问该服务。

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **原因**：流分析在与 EventHub 通信时遇到错误。 


## <a name="next-steps"></a>后续步骤

* [对输入连接进行故障排除](stream-analytics-troubleshoot-input.md)
* [对 Azure 流分析输出进行故障排除](stream-analytics-troubleshoot-output.md)
* [对 Azure 流分析查询进行故障排除](stream-analytics-troubleshoot-query.md)
* [使用资源日志对 Azure 流分析进行故障排除](stream-analytics-job-diagnostic-logs.md)
* [Azure 流分析数据错误](data-errors.md)
