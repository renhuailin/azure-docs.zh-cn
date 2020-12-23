---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001134"
---
此 `ApplicationInsights` 设置允许向容器添加 [Azure Application Insights](/azure/application-insights) 遥测支持。 Application Insights 可深入监视容器。 可以轻松监视容器的可用性、性能和使用情况。 还可以快速识别和诊断容器中的错误。

下表描述了 `ApplicationInsights` 节支持的配置设置。

|必选| 名称 | 数据类型 | 说明 |
|--|------|-----------|-------------|
|否| `InstrumentationKey` | String | 容器遥测数据要发送到的 Application Insights 实例的检测密钥。 有关详细信息，请参阅[适用于 ASP.NET Core 的 Application Insights](../articles/azure-monitor/app/asp-net-core.md)。 <br><br>示例：<br>`InstrumentationKey=123456789`|