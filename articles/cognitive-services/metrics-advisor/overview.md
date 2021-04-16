---
title: 什么是指标顾问服务？
titleSuffix: Azure Cognitive Services
description: 什么是指标顾问？
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: dfdd7286013bbb6462fb8e5b1bdf52e6ed738029
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384674"
---
# <a name="what-is-metrics-advisor-preview"></a>什么是指标顾问（预览版）？ 

指标顾问是 Azure 认知服务的一部分，可使用 AI 对时序数据执行数据监视和异常情况检测。 该服务自动执行将模型应用于数据的过程，并提供了一组 API 和基于 Web 的工作区，用于数据引入、异常检测和诊断，无需了解机器学习即可使用。 开发人员可以在服务之上构建 AIOps、预测维护和业务监视应用程序。 使用指标顾问执行以下操作：

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

## <a name="real-time-alerts-through-multiple-channels"></a>通过多个通道发送实时警报

每当检测到异常时，指标顾问可以使用挂钩（例如：电子邮件挂钩、Webhook 和 Azure DevOps 挂钩）通过多个通道[发送实时警报](how-tos/alerts.md)。 通过灵活的警报规则可自定义要发送的警报和目标位置。

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>通过分析异常生成智能诊断见解

分析在多维指标上检测到的异常，并生成[智能诊断见解](how-tos/diagnose-incident.md)，包括最可能的根本原因、诊断树、指标钻取等。 通过配置[指标图](how-tos/metrics-graph.md)，你可以启用跨指标分析来帮助你可视化事件。


## <a name="typical-workflow"></a>典型工作流

此工作流非常简单：载入数据后，可以微调异常检测，并创建适合场景的配置。

1. 为指标顾问[创建 Azure 资源](https://go.microsoft.com/fwlink/?linkid=2142156)。 
2. 使用 Web 门户生成首个监视器。
    1. 载入数据
    2. 微调异常情况检测
    3. 订阅警报
    4. 查看诊断见解
3. 使用 REST API 自定义实例。

## <a name="next-steps"></a>后续步骤

* 浏览快速入门：[监视你关于 web 的第一个指标](quickstarts/web-portal.md)。
* 浏览快速入门：[使用 REST API 自定义解决方案](./quickstarts/rest-api-and-client-library.md)。
