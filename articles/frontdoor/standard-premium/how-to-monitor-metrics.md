---
title: Azure Front Door 标准版/高级版的监视指标
description: 本文介绍了 Azure Front Door 标准版/高级版的监视指标。
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: bb10fb337972db2696960b530f2d7538bd36a2fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101098444"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Azure Front Door 标准版/高级版中的实时监视

> [!Note]
> 本文档适用于 Azure Front Door 标准版/高级版（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

Azure Front Door 标准版/高级版与 Azure Monitor 集成，具有 11 个指标，可帮助实时监视 Azure Front Door 标准版/高级版，从而跟踪、排查和调试问题。  

在 Azure Front Door 标准版/高级版中，每隔 60 秒衡量并发送一次指标。 最长 3 分钟后，这些指标才会显示在门户中。 依据选择，指标以图表或网格形式显示，并可通过门户、PowerShell、CLI 和 API 进行访问。 有关详细信息，请参阅  [Azure Monitor 指标](../../azure-monitor/platform/data-platform-metrics.md)。  

默认指标是免费的。 但若启用其他指标，则需额外收费。 

可以为每个指标配置警报，例如 4XXErrorRate 或5XXErrorRate 的阈值。 当错误率超过阈值时，将按配置触发警报。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../../azure-monitor/platform/alerts-metric.md)。 

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Azure Front Door 标准版/高级版中支持的指标

| 指标  | 说明 | 维度 |
| ------------- | ------------- | ------------- |
| 字节命中率 | 从 AFD 缓存的流出量占总流出量的百分比。 </br> **字节命中率** =（从边缘的流出量 - 从源点的流出量）/从边缘的流出量。 </br> **字节命中率计算中不包含的方案**：</br> 1.通过规则引擎或查询字符串缓存行为，显式配置无缓存。 </br> 2.显式配置了包含无存储或专用缓存的缓存控制指令。 </br>3.如果大多数流量转发到源点，而不是根据配置或方案从缓存提供，则字节命中率可能较低。 | 端点 |
| RequestCount | 由 CDN 为之服务的客户端请求数。 | 终结点、客户端所在国家/地区、客户端所在区域、HTTP 状态、HTTP 状态组 |
| ResponseSize | 由 AFD 为之服务的客户端请求数。 |终结点、客户端所在国家/地区、客户端所在区域、HTTP 状态、HTTP 状态组 |
| TotalLatency | **至 CDN 发送到客户端的最后一条响应为止**，CDN 收到客户端请求的总时间。 |终结点、客户端所在国家/地区、客户端所在区域、HTTP 状态、HTTP 状态组 |
| RequestSize | 以请求形式从客户端发送到 AFD 的字节数。 | 终结点、客户端所在国家/地区、客户端所在区域、HTTP 状态、HTTP 状态组 |
| 4XX % ErrorRate | 响应状态代码为 4XX 的所有客户端请求所占的百分比。 | 终结点、客户端所在国家/地区、客户端所在区域 |
| 5XX % ErrorRate | 响应状态代码为 5XX 的所有客户端请求所在的百分比。 | 终结点、客户端所在国家/地区、客户端所在区域 |
| OriginRequestCount  | 从 AFD 发送到源点的请求数 | 终结点、源点、HTTP 状态、HTTP 状态组 |
| OriginLatency | 从 AFD 边缘向后端发送请求起算，至 AFD 接收到来自后端的最后一个响应字节为止，所持续的总时间。 | 终结点、源点 |
| OriginHealth% | 从 AFD 到源点的运行状况探测中的成功百分比。| 源点、源点组 |
| WAF 请求计数 | 匹配的 WAF 请求。 | 操作、规则名称、策略名称 |

## <a name="access-metrics-in-azure-portal"></a>在 Azure 门户中访问指标

1. 从 Azure 门户菜单中，选择“所有资源” >>  **\<your-AFD Standard/Premium (Preview) -profile>** 。

2. 在“监视”下，选择“指标”：

3. 在“指标”中，选择要添加的指标：

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="指标页的屏幕截图。" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. 选择“添加筛选器”以添加筛选器：

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="向指标添加筛选器的屏幕截图。" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. 选择“应用拆分”以按不同维度拆分数据：

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="向指标添加维度的屏幕截图。" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. 选择“新建图表”以添加新图表：

## <a name="configure-alerts-in-azure-portal"></a>在 Azure 门户中配置警报

1. 通过选择“监视” >> “警报”，设置 Azure Front Door 标准版/高级版（预览版）上的警报。

1. 为“指标”部分中列出的指标选择“新建警报规则”。

警报将根据 Azure Monitor 进行收费。 有关警报的详细信息，请参阅 [Azure Monitor 警报](../../azure-monitor/platform/alerts-overview.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Front Door 标准版/高级版报告](how-to-reports.md)。
- 了解 [Azure Front Door 标准版/高级版日志](how-to-logs.md)。
