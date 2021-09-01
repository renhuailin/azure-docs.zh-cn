---
title: 将 Azure API 管理与 Azure Application Insights 集成
titleSuffix: Azure API Management
description: 了解如何在 Azure Application Insights 中记录和查看来自 Azure API 管理的事件。
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/04/2021
ms.author: apimpm
ms.openlocfilehash: f67da2c2090dd99730324512248854d5e2fee259
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182079"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>如何将 Azure API 管理与 Azure Application Insights 集成

Azure API 管理可以轻松地与 Azure Application Insights 集成（一个可扩展的服务，可让 Web 开发人员在多个平台上生成和管理应用）。 本指南逐步讲解这种集成的每个步骤，并介绍可以采取哪些策略来降低对 API 管理服务实例的性能影响。

## <a name="prerequisites"></a>先决条件

若要执行本指南中的步骤，需要一个 Azure API 管理实例。 如果没有该实例，请先完成[此教程](get-started-create-service-instance.md)。

## <a name="create-an-application-insights-instance"></a>创建 Application Insights 实例

在使用 Application Insights 之前，需要先创建一个服务实例。 有关使用 Azure 门户创建实例的步骤，请参阅[基于工作区的 Application Insights 资源](../azure-monitor/app/create-workspace-resource.md)。
## <a name="create-a-connection-between-application-insights-and-api-management"></a>在 Application Insights 和 API 管理之间创建连接

1. 在 **Azure 门户** 中导航到自己的 **Azure API 管理服务实例**。
1. 在左侧菜单中选择“Application Insights”。 
1. 选择“+ 添加”。  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="显示在何处添加新连接的屏幕截图":::
1. 选择前面创建的 **Application Insights** 实例并提供简短说明。
1. 如要启用 Application Insights 中的 API 管理实例的[可用性监视](../azure-monitor/app/monitor-web-app-availability.md)，请选择“添加可用性监视”复选框。

    此设置会定期验证 API 管理服务终结点是否正在响应。 结果显示在 Application Insights 实例的“可用性”窗格中。
1. 选择“创建”。
1. 你刚刚创建了一个具有检测密钥的 Application Insights 记录器。 该记录器现在应已显示在列表中。  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="此屏幕截图显示了在何处使用检测密钥查看新建的 Application Insights 记录器":::

> [!NOTE]
> 在后台，将在 API 管理实例中创建一个 [Logger](/rest/api/apimanagement/2019-12-01/logger/createorupdate) 实体，其中包含 Application Insights 实例的检测密钥。

## <a name="enable-application-insights-logging-for-your-api"></a>为 API 启用 Application Insights 日志记录

1. 在 **Azure 门户** 中导航到自己的 **Azure API 管理服务实例**。
1. 在左侧菜单中选择“API”  。
1. 单击你的 API（在本例中为“演示会议 API”）。  选择一个版本（如果已配置）。
1. 从顶部栏转到“设置”选项卡。 
1. 向下滚动到“诊断日志”部分。   
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="App Insights 记录器":::
1. 选中“启用”框。 
1. 在“目标”下拉列表中选择附加的记录器。 
1. 输入 **100** 作为“采样率”，并选中“始终记录错误”复选框。 
1. 选择“保存”。

> [!WARNING]
> 替代“要记录的有效负载字节数”设置中的默认值 **0** 可能会显著降低 API 的性能。

> [!NOTE]
> 在后台，将在 API 级别创建一个名为“applicationinsights”的 [Diagnostic](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) 实体。

| 设置名称                        | 值类型                        | 说明                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 启用                              | boolean                           | 指定是否要启用此 API 的日志记录。                                                                                                                                                                                                                                                                                                |
| 目标                         | Azure Application Insights 记录器 | 指定要使用的 Azure Application Insights 记录器                                                                                                                                                                                                                                                                                           |
| 采样率                        | decimal                           | 值为 0 到 100（百分比）。 <br/> 指定要记录到 Application Insights 的请求数百分比。 0% 采样表示不记录任何请求，100% 采样表示记录所有请求。 <br/> 使用此设置可以降低将请求记录到 Application Insights 时对性能造成的影响。 请参阅[性能影响和日志采样](#performance-implications-and-log-sampling)。 |
| 始终记录错误                   | boolean                           | 如果选择此设置，则会将所有错误记录到 Application Insights，不管“采样率”设置如何。   
| 记录客户端 IP 地址 | |  如果选择此设置，则会将 API 请求的客户端 IP 地址记录到 Application Insights。                                         |
| 详细程度         |                                   | 指定详细程度。 将仅记录具有较高严重性级别的自定义跟踪。 默认值：信息。      | 
| 关联协议 |  |  选择用于关联多个组件发送的遥测数据的协议。 默认值：旧版 <br/>有关信息，请参阅 [Application Insights 中的遥测关联](../azure-monitor/app/correlation.md)。  |
| 基本选项：要记录的头              | list                              | 指定要记录到 Application Insights 的请求和响应头。  默认值：不记录标头。                                                                                                                                                                                                             |
| 基本选项：要记录的有效负载字节数 | 整型                           | 指定要将请求和响应正文的最前面多少个字节记录到 Application Insights。  默认值：0。                                                                                                                                                                                                    |
| 高级选项：前端请求  |                                   | 指定是否以及如何将前端请求记录到 Application Insights。 前端请求是传入 Azure API 管理服务的请求。                                                                                                                                                                         |
| 高级选项：前端响应 |                                   | 指定是否以及如何将前端响应记录到 Application Insights。 前端响应是传出 Azure API 管理服务的响应。                                                                                                                                                                    |
| 高级选项：后端请求   |                                   | 指定是否以及如何将后端请求记录到 Application Insights。 后端请求是传出 Azure API 管理服务的请求。                                                                                                                                                                         |
| 高级选项：后端响应  |                                   | 指定是否以及如何将后端响应记录到 Application Insights。 后端响应是传入 Azure API 管理服务的响应。                                                                                                                                                                        |

> [!NOTE]
> 可以在不同的级别指定记录器 - 指定单个 API 记录器，或者对所有 API 使用一个记录器。
>  
> 如果同时指定这两个选项：
> + 如果它们是不同的记录器，则会同时使用两者（多路复用日志）；
> + 如果它们是相同的记录器但采用不同的设置，则单个 API 的记录器（粒度级更高）将替代所有 API 的记录器。

## <a name="what-data-is-added-to-application-insights"></a>将哪些数据添加到 Application Insights

Application Insights 接收：

+ 针对每个传入请求的请求遥测项：
    + 前端请求，前端响应
+ 针对转发到后端服务的每个请求的依赖项遥测项：
    + 后端请求，后端响应
+ 每个失败的请求的异常遥测项：
    + 由于客户端连接关闭而失败
    + 触发了 API 策略的 *on-error* 节
    + 具有匹配 4xx 或 5xx 的响应 HTTP 状态代码
+ 跟踪遥测项（如果配置[跟踪](api-management-advanced-policies.md#Trace)策略）。 
    + `trace` 策略中的设置 `severity` 必须等于或大于 Application Insights 日志记录中的 `verbosity` 设置。

还可通过配置 [`emit-metric`](api-management-advanced-policies.md#emit-metrics) 策略来发出自定义指标。

> [!NOTE]
> 有关每个 Application Insights 实例的指标和事件的最大大小与数量的信息，请参阅 [Application Insights 限制](../azure-monitor/service-limits.md#application-insights)。

## <a name="performance-implications-and-log-sampling"></a>性能影响和日志采样

> [!WARNING]
> 记录所有事件可能会产生严重的性能影响，具体取决于传入请求的速率。

根据内部负载测试，当请求速率超过每秒 1,000 个请求时，启用此功能会导致吞吐量下降 40%-50%。 Application Insights 设计为使用统计分析来评估应用程序的性能。 它并非旨在用作审核系统，并且不适合用于记录高流量 API 的每个请求。

可以通过调整“采样率”设置来操控所记录的请求数（参阅前面的步骤）。 值 100% 表示记录所有请求，0% 表示不进行日志记录。 “采样率”有助于减少遥测量，可有效防止性能出现明显下降，同时仍可保持日志记录的优势。

跳过请求和响应的标头与正文的日志记录也有利于缓解性能问题。

## <a name="video"></a>视频

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>后续步骤

+ 详细了解 [Azure Application Insights](/azure/application-insights/)。
+ 考虑[使用 Azure 事件中心进行日志记录](api-management-howto-log-event-hubs.md)。
