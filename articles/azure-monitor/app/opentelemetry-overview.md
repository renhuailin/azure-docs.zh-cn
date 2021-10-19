---
title: 借助 Azure Monitor 使用 OpenTelemetry 的概述
description: 概述如何将 OpenTelemetry 与 Azure Monitor 结合使用。
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 268d7d8372fef1568a369f956e3caa91749f0e8e
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859569"
---
# <a name="opentelemetry-overview"></a>OpenTelemetry 概述

Microsoft 很高兴能够在未来使用 [OpenTelemetry](https://opentelemetry.io/) 检测遥测数据。 作为我们的客户，你曾经请求 Microsoft 提供与供应商无关的工具，因此我们现在很高兴能够与 OpenTelemetry 社区达成合作，创建以不同语言呈现的一致 API/SDK。

Microsoft 与 [OpenCensus](https://opencensus.io/) 和 [OpenTracing](https://opentracing.io/) 这两个热门开源遥测项目中的项目利益干系人顺利达成合作，以帮助创建 OpenTelemetry 这一项目。 OpenTelemetry 包括所有主要云和应用程序性能管理 (APM) 供应商发布的内容，由[云本机计算基础 (CNCF)](https://www.cncf.io/) 托管，其中 Microsoft 为白金成员。

## <a name="concepts"></a>概念

遥测数据（为观察应用程序而进行收集的数据）可以分为三种类型或包含三种构成要素：
1.  分布式跟踪
2.  指标
3.  日志

OpenTelemetry 社区最初采用分布式跟踪。 指标和日志方面的工作仍在进行中。 完整的可观察性故事包含所有三个支柱构成要素，但目前我们[针对 .net、Python 和 JavaScript 推出的基于 Azure Monitor OpenTelemetry 的导出器预览版套餐](opentelemetry-enable.md)仅包含分布式跟踪 。

可以通过多个源来详细说明这三种构成要素，其中包括 [OpenTelemetry 社区网站](https://opentelemetry.io/docs/concepts/data-sources/)、[OpenTelemetry 规范](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/overview.md)和 Cindy Sridharan [分布式系统可观测性](https://www.oreilly.com/library/view/distributed-systems-observability/9781492033431/ch04.html)。

我们将在以下部分介绍一些遥测收集的基础知识。

### <a name="instrumenting-your-application"></a>检测应用程序

就基础级别而言，“检测”只是使应用程序能够捕获遥测数据。

有两种检测应用程序的方法：
1.  手动检测
2.  自动检测

手动检测指针对 OpenTelemetry API 进行编码。 在最终用户的上下文中，其通常是指在应用程序中安装特定于语言的 SDK。 手动检测包中包含我们[针对 .NET、Python 和 JavaScript 推出的基于 Azure Monitor OpenTelemetry 导出器预览版套餐](opentelemetry-enable.md)。

> [!IMPORTANT]
> “手动”并不意味着需要编写复杂的代码来定义分布式跟踪的范围（尽管这仍不失为一种选择）。 使用由 OpenTelemetry 贡献者维护的丰富且不断扩充的一组检测库，可以轻松地跨通用框架和库捕获遥测信号。 根据客户的反馈信息，我们将在 Azure Monitor 中提供 OpenTelemetry 检测库的子集。 此外，我们正在致力于检测最热门的 Azure 服务 SDK 以发出 OpenTelemetry 协议 (OTLP)，并针对 Azure Monitor 优化这些协议。

另一方面，自动检测通过配置来启用遥测收集，无需接触应用程序的代码。 虽然非常方便，但自动检测的可配置性较低，并且只提供部分语言版本。 基于 Azure Monitor OpenTelemetry 的自动检测产品包含[基于 Java 3.X OpenTelemetry 的 GA 产品/服务](java-in-process-agent.md)，我们也将继续根据客户反馈做出相应投资。 OpenTelemetry 社区还尝试使用 C# 和 Python 自动检测，但 Azure Monitor 主要用于在短期内创建简单有效的手动检测。

### <a name="sending-your-telemetry"></a>发送遥测数据

还可以通过两种方式将数据发送给 Azure Monitor（或任何供应商）。
1. 直接导出程序
2. 通过代理

直接导出程序会将正在进行的遥测（从应用程序的代码）直接发送到 Azure Monitor 的引入终结点。 此方法的主要优点是加入过程非常简单。

Azure Monitor 当前支持的所有 OpenTelemetry 产品/服务均使用直接导出程序。 

或者通过代理发送遥测，这将通过 [OTLP](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/protocol/README.md) 为 OpenTelemetry 支持的任何语言提供发送到 Azure Monitor 的路径。 这样，客户就不仅能够观测以我们[支持的语言](platforms.md)编写的应用程序，而且还能观测以其他语言编写的应用程序。 

> [!NOTE]
> 有些客户已开始使用 [OpenTelemetry-Collector](https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/design.md) 作为代理替代服务，即使 Microsoft 尚未正式支持基于代理的应用程序监视方法。 与此同时，开源社区已贡献了一个 OpenTelemetry-Collector Azure Monitor 导出器，某些客户正在使用它向 Azure Monitor Application Insights 发送数据。

## <a name="terms"></a>术语

请参阅 OpenTelemetry 规范中的[词汇表](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/glossary.md)。

鉴于 OpenTelemetry 上的行业整合，Application Insights 中的一些旧术语会引发混淆问题。 下表突出显示了这些差异。 最后，Application Insights 条款将替换为 OpenTelemetry 条款。

Application Insights | OpenTelemetry
------ | ------
跟踪   | 日志
通道   | 导出程序  
无代码/基于代理   | 自动检测

## <a name="next-step"></a>后续步骤

以下页面包含用于启用和配置 Microsoft OpenTelemetry 产品/服务的语言指南。 重要的是，我们共享了每个产品/服务的可用功能和限制，以便你确定 OpenTelemetry 是否适用于你的项目。
- [.NET](opentelemetry-enable.md) 
- [Java](java-in-process-agent.md)
- [JavaScript](opentelemetry-enable.md)
- [Python](opentelemetry-enable.md)
