---
title: 教程 - 在 Azure API 管理中监视已发布的 API | Microsoft Docs
description: 按本教程中的步骤操作，了解如何使用 Azure API 管理中的指标、警报、活动日志和资源日志来监视 API。
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: danlep
ms.openlocfilehash: 378fa83925808b1c9551522cfe4c47665095cd17
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642066"
---
# <a name="tutorial-monitor-published-apis"></a>教程：监视已发布的 API

通过 Azure Monitor，可直观显示、查询、路由和存档来自 Azure API 管理服务的指标或日志并对其执行操作。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 查看 API 的指标 
> * 设置警报规则 
> * 查看活动日志
> * 启用和查看资源日志

你还可以使用 API 管理的内置[分析](howto-use-analytics.md)来监视 API 的使用和性能。

## <a name="prerequisites"></a>先决条件

+ 了解 [Azure API 管理术语](api-management-terminology.md)。
+ 完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
+ 此外，请完成以下教程：[导入和发布第一个 API](import-and-publish.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>查看 API 的指标

API 管理每分钟发出一次[指标](../azure-monitor/essentials/data-platform-metrics.md)，几乎可让你实时了解 API 的状态和运行状况。 以下是两个最常用的指标。 有关所有可用指标的列表，请参阅[支持的指标](../azure-monitor/essentials/metrics-supported.md#microsoftapimanagementservice)。

* **容量** - 帮助做出有关升级/降级 APIM 服务的决策。 指标每分钟发出，在报告时反映网关容量。 指标范围为 0-100，是根据 CPU 和内存利用率等网关资源计算的。
* **请求** - 帮助分析通过 API 管理服务的 API 流量。 指标每分钟发出一次，并报告网关请求数，其维度包括响应代码、位置、主机名和错误。 

> [!IMPORTANT]
> 以下指标已于 2019 年 5 月弃用，并将于 2023 年 8 月停用：网关请求总数、成功的网关请求数、未经授权的网关请求数、失败的网关请求数、其他网关请求数。 请迁移到提供等效功能的请求指标。

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="API 管理概述中“指标”的屏幕截图":::

访问指标：

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。 在“概述”页中，查看 API 的关键指标。
1. 若要详细了解指标，请在靠近页面底部的菜单中选择“指标”。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="“监视”菜单中“指标”项的屏幕截图":::

1. 从下拉列表中选择所需的指标。 例如，“请求”。 
1. 该图显示 API 调用总数。
1. 可以使用 **请求** 指标的维度来筛选该图表。 例如，依次选择“添加筛选器”和“后端响应代码类别”，然后输入值 500 。 现在，该图表显示了 API 后端中失败的请求数。   

## <a name="set-up-an-alert-rule"></a>设置警报规则 

可以基于指标和活动日志接收[警报](../azure-monitor/alerts/alerts-metric-overview.md)。 通过 Azure Monitor 可[配置警报](../azure-monitor/alerts/alerts-metric.md)，使警报触发时执行以下操作：

* 发送电子邮件通知
* 调用 Webhook
* 调用 Azure 逻辑应用

若要基于请求指标配置示例警报规则，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 在靠近页面底部的菜单栏中选择“警报”。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="“监视”菜单中“警报”选项的屏幕截图":::

1. 选择“+ 新建警报规则”。
1. 在“创建警报规则”窗口中，选择“条件” 。
1. 在“配置信号逻辑”窗口中：
    1. 在“信号类型”中，选择“指标” 。
    1. 在“信号名称”中，选择“请求” 。
    1. 在“按维度拆分”的“维度名称”中，选择“网关响应代码类别”  。
    1. 在“维度值”中，选择“4xx”，表示“请求未经授权”或“请求无效”等客户端错误 。
    1. 在“警报逻辑”中指定触发警报的阈值，然后选择“完成” 。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="“配置信号逻辑”窗口的屏幕截图":::

1. 选择现有的操作组或创建新组。 在以下示例中，将新建操作组。 通知电子邮件将发送到 admin@contoso.com。 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="新操作组的通知的屏幕截图":::

1. 输入警报规则的名称和说明，然后选择严重级别。 
1. 选择“创建警报规则”。
1. 现在，在没有 API 密钥的情况下调用会议 API 来测试警报规则。 例如：

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    警报将基于评估时长触发，电子邮件将发送到 admin@contoso.com。 

    警报还会显示在 API 管理实例的“警报”页。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="门户中的警报的屏幕截图":::

## <a name="activity-logs"></a>活动日志

活动日志提供有关对 API 管理服务执行的操作的见解。 通过活动日志，可确定对 API 管理服务执行的任何写入操作 (PUT、POST、DELETE) 的“操作内容、操作人员和操作时间”。

> [!NOTE]
> 活动日志不包括读取 (GET) 操作或者通过 Azure 门户或原始管理 API 执行的操作。

可在 API 管理服务中访问活动日志，或在 Azure Monitor 中访问所有 Azure 资源的日志。 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="门户中活动日志的屏幕截图":::

若要查看活动日志，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。

1. 选择“活动日志”。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="“监视”菜单中“活动日志”项的屏幕截图":::
1. 选择所需的筛选范围，然后选择“应用”。

## <a name="resource-logs"></a>资源日志

资源日志提供了大量有关操作和错误的信息，这些信息对于审核和故障排除非常重要。 资源日志不同于活动日志。 活动日志提供对在 Azure 资源上执行的操作的见解。 资源日志提供对资源执行的操作的见解。

若要配置资源日志，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
2. 选择“诊断设置”。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="“监视”菜单中“诊断设置”项的屏幕截图":::

1. 选择“+ 添加诊断设置”。 
1. 选择要收集的日志或指标。

   可以将资源日志与指标一起存档到存储帐户，将其流式传输到事件中心，或者将其发送到 Log Analytics 工作区。 

有关详细信息，请参阅[创建诊断设置以将平台日志和指标发送到不同的目标](../azure-monitor/essentials/diagnostic-settings.md)。

## <a name="view-diagnostic-data-in-azure-monitor"></a>在 Azure Monitor 中查看诊断数据

如果在 Log Analytics 工作区中启用 GatewayLogs 或指标的收集，则数据可能需要几分钟才能在 Azure Monitor 中显示。 若要查看数据，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 在靠近页面底部的菜单中选择“日志”。

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="“监视”菜单中“日志”项的屏幕截图":::

运行查询以查看数据。 可以运行提供的多个[示例查询](../azure-monitor/logs/queries.md)，也可以运行自己的查询。 例如，以下查询检索 GatewayLogs 表中最近 24 小时的数据：

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

有关使用资源日志进行 API 管理的详细信息，请参阅：

* [Azure Monitor Log Analytics 入门](../azure-monitor/logs/log-analytics-tutorial.md)，或试用 [Log Analytics 演示环境](https://portal.loganalytics.io/demo)。

* [Azure Monitor 中的日志查询概述](../azure-monitor/logs/log-query-overview.md)。

以下 JSON 表示 GatewayLogs 中成功的 API 请求的示例条目。 有关详细信息，请参阅[架构参考](gateway-log-schema-reference.md)。 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 查看 API 的指标
> * 设置警报规则 
> * 查看活动日志
> * 启用和查看资源日志


转到下一教程：

> [!div class="nextstepaction"]
> [跟踪调用](api-management-howto-api-inspector.md)