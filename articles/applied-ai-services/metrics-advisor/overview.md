---
title: 什么是 Azure 指标顾问服务？
titleSuffix: Azure Applied AI Services
description: 什么是指标顾问？
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 07/06/2021
ms.author: mbullwin
ms.openlocfilehash: 295c166ec80eba638798dd452e27de945e41e9a6
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341444"
---
# <a name="what-is-azure-metrics-advisor"></a>什么是 Azure 指标顾问？ 

指标顾问是 [Azure 应用 AI 服务](../../applied-ai-services/what-are-applied-ai-services.md)的一部分，可使用 AI 对时序数据执行数据监视和异常情况检测。 该服务自动执行将模型应用于数据的过程，并提供了一组 API 和基于 Web 的工作区，用于数据引入、异常检测和诊断，无需了解机器学习即可使用。 开发人员可以在服务之上构建 AIOps、预测维护和业务监视应用程序。 使用指标顾问执行以下操作：

* 分析来自多个数据源的多维数据
* 识别并关联异常
* 配置并微调用于数据的异常情况检测模型
* 诊断异常情况，并帮助进行根本原因分析

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="指标顾问概述":::

本文档包含以下类型的文章：
* [快速入门](./Quickstarts/web-portal.md)是分步说明，可按照其调用服务，并在短时间内获得结果。 
* [操作指南](./how-tos/onboard-your-data.md)包含以更具体的方式或自定义方式使用服务的说明。
* [概念性文章](glossary.md)对服务的功能和特性进行了深入说明。

## <a name="connect-to-a-variety-of-data-sources"></a>连接到各种数据源

指标顾问可以连接到多个数据存储，并从这些数据存储中[引入多维指标](how-tos/onboard-your-data.md)数据，包括：SQL Server、Azure Blob 存储、MongoDB 等。

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>易于使用且可自定义的异常情况检测

* 指标顾问会自动为你的数据选择最佳模型，而无需了解任何机器学习。
* 在[多维指标](glossary.md#multi-dimensional-metric)中自动监视每个时序。
* 使用[参数优化](how-tos/configure-metrics.md)和[交互式反馈](how-tos/anomaly-feedback.md)自定义应用于数据的模型以及将来的异常情况检测结果。

## <a name="real-time-notification-through-multiple-channels"></a>通过多个通道发送实时通知

每当检测到异常时，指标顾问可以使用挂钩（例如：电子邮件挂钩、Webhook、Teams 挂钩和 Azure DevOps 挂钩）通过多个通道[发送实时通知](how-tos/alerts.md)。 通过配置灵活的警报，可自定义通知的发送时间和发送位置。

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>通过分析异常生成智能诊断见解

### <a name="analyze-root-cause-into-specific-dimension"></a>通过特定维度分析根本原因 

指标顾问将在同一多维指标上检测到的异常合并到诊断树中，这有助于通过特定维度分析根本原因。 此外，还通过分析每个维度占比最大的因素提供自动分析的见解。 

### <a name="cross-metrics-analysis-using-metrics-graph"></a>使用指标图进行跨指标分析

[指标图](./how-tos/metrics-graph.md)指示指标之间的关系。 可启用跨指标分析帮助你在整体视图中捕获所有相关指标的异常状态。 最后找到最终的根本原因。

有关详细信息，请参阅[如何诊断事件](./how-tos/diagnose-an-incident.md)。

## <a name="typical-workflow"></a>典型工作流

此工作流非常简单：载入数据后，可以微调异常检测，并创建适合场景的配置。

1. 为指标顾问[创建 Azure 资源](https://go.microsoft.com/fwlink/?linkid=2142156)。 
2. 使用 Web 门户生成首个监视器。
    1. [载入数据](./how-tos/onboard-your-data.md)
    2. [微调异常情况检测配置](./how-tos/configure-metrics.md)
    3. [订阅异常以接收通知](./how-tos/alerts.md)
    4. [查看诊断见解](./how-tos/diagnose-an-incident.md)
3. 使用 REST API 自定义实例。

## <a name="video"></a>视频
* [指标顾问简介](https://www.youtube.com/watch?v=0Y26cJqZMIM)
* [初次使用认知服务](https://www.youtube.com/watch?v=7tCLJHdBZgM)

## <a name="data-retention--limitation"></a>数据保留和限制： 

指标顾问最多保留 10000 个时间间隔（[什么是间隔？](tutorials/write-a-valid-query.md#what-is-an-interval)），从当前时间戳向前计数（无论是否有可用数据）。 超出此时间范围的数据将被删除。  数据保留映射的天数因指标粒度而异： 

| 粒度（分钟） |    保留（天） |
|------------------| ------------------|
|  1 | 6.94 |
|  5 | 34.72|
| 15 | 104.1|
| 60（1 小时） | 416.67 |
| 1440（1 天）|10000.00|

此外还有更多限制，请参阅[常见问题解答](faq.yml#what-are-the-data-retention-and-limitations-of-metrics-advisor-)了解更多详细信息。 

## <a name="next-steps"></a>后续步骤

* 浏览快速入门：[监视你关于 web 的第一个指标](quickstarts/web-portal.md)。
* 浏览快速入门：[使用 REST API 自定义解决方案](./quickstarts/rest-api-and-client-library.md)。