---
title: 分析 Azure CDN 使用模式
description: 本文介绍适用于 Azure CDN 产品的不同类型的分析报告。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2020
ms.author: allensu
ms.openlocfilehash: 2c593cda7761ce1defa6bdb31e0dbb528b9e5eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96483982"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>分析 Azure CDN 使用模式

为应用程序启用 CDN 后，可以监视 CDN 使用情况、检查交付内容的运行状况以及排除潜在问题。 Azure CDN 通过以下方式提供这些功能： 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Microsoft 推出的 Azure CDN 的原始日志
使用标准 Microsoft 配置文件，可以启用原始日志，并选择将日志流式传输到以下位置：

* Azure 存储
* 事件中心
* Azure Log Analytics

使用 Azure Log Analytics，可以查看监视指标并设置警报。 

有关详细信息，请参阅 [Azure CDN HTTP 原始日志](monitoring-and-access-log.md)。


## <a name="core-analytics-via-azure-diagnostic-logs"></a>通过 Azure 诊断日志进行核心分析

核心分析可用于所有定价层的 CDN 终结点。 Azure 诊断日志可以将核心分析导出到 Azure 存储、事件中心或 Azure Monitor 日志。 Azure Monitor 日志提供的解决方案中具有用户可配置且可自定义的图。 有关 Azure 诊断日志的详细信息，请参阅 [Azure 诊断日志](cdn-azure-diagnostic-logs.md)。

## <a name="verizon-core-reports"></a>Verizon 核心报表

“Verizon 的 Azure CDN 标准版”和“Verizon 的 Azure CDN 高级版”配置文件提供了核心报表。 可以在 Verizon 补充门户中查看核心报表。 可通过 Azure 门户的“管理”选项访问 Verizon 核心报表，这些报表提供了各种图和视图。 有关详细信息，请参阅 [Verizon 中的核心报表](cdn-analyze-usage-patterns.md)。

## <a name="verizon-custom-reports"></a>Verizon 自定义报表

“Verizon 的 Azure CDN 标准版”和“Verizon 的 Azure CDN 高级版”配置文件提供了自定义报表。 可以在 Verizon 补充门户中查看自定义报表。 可通过 Azure 门户的“管理”选项访问 Verizon 自定义报表。 

自定义报表显示了每个边缘 CNAME 的命中数或传输的数据。 数据将按照一段时间内的 HTTP 响应代码或缓存状态进行分组。 有关详细信息，请参阅 [Verizon 中的自定义报表](cdn-verizon-custom-reports.md)。

## <a name="azure-cdn-premium-from-verizon-reports"></a>来自 Verizon 的高级 Azure CDN 报告

使用 **Verizon 提供的 Azure CDN 高级版**，还可以访问以下报告：
   * [高级 HTTP 报告](cdn-advanced-http-reports.md)
   * [实时统计信息](cdn-real-time-stats.md)
   * [Azure CDN 边缘节点性能](cdn-edge-performance.md)

## <a name="next-steps"></a>后续步骤
本文介绍了 Azure CDN 的分析报表的不同选项。

有关 Azure CDN 和本文中所述的其他 Azure 服务的详细信息，请参阅：

* [什么是 Azure CDN？](cdn-overview.md)
* [Azure CDN HTTP 原始日志](monitoring-and-access-log.md)
