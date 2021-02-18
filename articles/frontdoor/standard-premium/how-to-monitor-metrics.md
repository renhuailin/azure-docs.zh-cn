---
title: Azure 前门标准/高级监视指标
description: 本文介绍 Azure 前门标准/高级监视指标。
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: bb10fb337972db2696960b530f2d7538bd36a2fb
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098444"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Azure 前门标准/高级版中的实时监视

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

Azure 前门标准/高级版与 Azure Monitor 集成，具有11个指标，可帮助实时监视 Azure 前门标准/高级版，以跟踪、排查和调试问题。  

Azure 前门标准/高级度量值，并以60秒为间隔发送指标。 此指标最多可能需要3分钟才能在门户中显示。 度量值可在选择的图表或网格中显示，可通过门户、PowerShell、CLI 和 API 进行访问。 有关详细信息，请参阅 [Azure Monitor 指标](../../azure-monitor/platform/data-platform-metrics.md)。  

默认指标是免费的。 您可以启用其他指标来额外收费。 

你可以为每个指标（如4XXErrorRate 或5XXErrorRate 的阈值）配置警报。 当错误率超过阈值时，它会触发配置的警报。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../../azure-monitor/platform/alerts-metric.md)。 

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Azure 前门标准/高级版中支持的指标

| 指标  | 说明 | 维度 |
| ------------- | ------------- | ------------- |
| 字节命中率 | AFD 缓存的出口百分比，计算出的总出口数。 </br> **字节命中率** = 从/egress 开始，从原始位置传出) 从边缘传出 (传出。 </br> **排除的方案数（按字节数计算）**：</br> 1. 通过规则引擎或查询字符串缓存行为显式配置任何缓存。 </br> 2. 将缓存控制指令显式配置为不存储或专用缓存。 </br>3. 如果大多数流量转发到源，而不是根据配置或方案提供缓存，则字节命中率可能较低。 | 终结点 |
| RequestCount | CDN 提供的客户端请求数。 | 终结点，客户端国家/地区，HTTP 状态，HTTP 状态组 |
| ResponseSize | AFD 提供的客户端请求数。 |终结点，客户端国家/地区，HTTP 状态，HTTP 状态组 |
| TotalLatency | CDN 从 **cdn 发送到客户端之前**，CDN 收到的客户端请求的总时间。 |终结点，客户端国家/地区，HTTP 状态，HTTP 状态组 |
| RequestSize | 作为请求从客户端发送到 AFD 的字节数。 | 终结点，客户端国家/地区，HTTP 状态，HTTP 状态组 |
| 4XX% ErrorRate | 响应状态代码为4XX 的所有客户端请求的百分比。 | 终结点，客户端国家/地区，客户端区域 |
| 5XX% ErrorRate | 响应状态代码为5XX 的所有客户端请求的百分比。 | 终结点，客户端国家/地区，客户端区域 |
| OriginRequestCount  | 从 AFD 发送到源的请求数 | 终结点，源，HTTP 状态，HTTP 状态组 |
| OriginLatency | 从 AFD 边缘向后端发送请求到后一条时间，直到 AFD 从后端收到最后一个响应字节时得出的时间。 | 终结点，原点 |
| OriginHealth% | 成功的运行状况探测从 AFD 到源的百分比。| 源、源组 |
| WAF 请求计数 | 匹配的 WAF 请求。 | 操作、规则名称、策略名称 |

## <a name="access-metrics-in-azure-portal"></a>Azure 门户中的访问度量值

1. 从 "Azure 门户" 菜单中，选择 "**所有资源**"  >>  **\<your-AFD Standard/Premium (Preview) -profile>** 。

2. 在 " **监视**" 下，选择 " **指标**"：

3. 在 " **度量值**" 中，选择要添加的度量值：

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="&quot;指标&quot; 页的屏幕截图。" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. 选择 " **添加筛选** 器" 以添加筛选器：

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="向指标添加筛选器的屏幕截图。" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. 选择 " **应用拆分** "，按不同维度拆分数据：

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="将维度添加到指标的屏幕截图。" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. 选择 " **新建图表** " 以添加新图表：

## <a name="configure-alerts-in-azure-portal"></a>在 Azure 门户中配置警报

1. 通过选择 "**监视** 警报"，设置有关 Azure 前门标准/高级 (预览版) 的警报  >>  。

1. 为 "度量值" 部分中列出的指标选择 " **新建警报规则** "。

将根据 Azure Monitor 对警报进行收费。 有关警报的详细信息，请参阅 [Azure Monitor 警报](../../azure-monitor/platform/alerts-overview.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 前门标准/高级报告](how-to-reports.md)。
- 了解 [Azure 前门标准/高级日志](how-to-logs.md)。
