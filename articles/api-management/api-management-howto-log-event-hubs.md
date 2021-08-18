---
title: 如何在 Azure API 管理中将事件记录到 Azure 事件中心 | Microsoft Docs
description: 了解如何在 Azure API 管理中将事件记录到 Azure 事件中心。 事件中心是高度可缩放的数据入口服务。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: dc36caf8e00a3f23928b95eedc2574072f9c474d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741575"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>如何在 Azure API 管理中将事件记录到 Azure 事件中心
事件中心是一个高度可缩放的引入服务，每秒可以引入数百万的事件，使用户能够处理和分析连接设备和应用程序生成的海量数据。 事件中心充当事件管道的“前门”，将数据收集到事件中心后，可以使用任何实时分析提供程序或批处理/存储适配器来转换和存储这些数据。 事件中心可将事件流的生成与这些事件的使用分离开来，因此，事件使用者可以根据自己的计划访问事件。

本文是[将 Azure API 管理与事件中心集成](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/)视频的配套内容，介绍了如何使用 Azure 事件中心记录 API 管理事件。

## <a name="create-an-azure-event-hub"></a>创建 Azure 事件中心

有关如何创建事件中心以及获取将事件发送到事件中心和从事件中心接收事件所需的连接字符串的详细步骤，请参阅[使用 Azure 门户创建事件中心命名空间和事件中心](../event-hubs/event-hubs-create.md)。

## <a name="create-an-api-management-logger"></a>创建 API 管理记录器
现在有了事件中心，下一步是在 API 管理服务中配置[记录器](/rest/api/apimanagement/2020-12-01/logger)，以便它可以将事件记录到事件中心。

使用 [API 管理 REST API](/rest/api/apimanagement/ApiManagementREST/API-Management-REST) 配置 API 管理记录器。 有关详细的请求示例，请参阅[如何创建记录器](/rest/api/apimanagement/2020-12-01/logger/create-or-update)。

## <a name="configure-log-to-eventhub-policies"></a>配置 log-to-eventhub 策略

在 API 管理中配置了记录器后，就可以配置 log-to-eventhub 策略以记录所需事件。 可在入站策略部分或出站策略部分中使用 log-to-eventhub 策略。

1. 浏览到自己的 APIM 实例。
2. 选择“API”选项卡。
3. 选择要将策略添加到的 API。 在此示例中，我们向 **Unlimited** 产品中的 **Echo API** 添加策略。
4. 选择“所有操作”。
5. 选择屏幕顶部的“设计”选项卡。
6. 在“入站或出站处理”窗口中，单击三角形（铅笔旁边）。
7. 选择“代码编辑器”。 有关详细信息，请参阅[如何设置或编辑策略](set-edit-policies.md)。
8. 将光标放在 `inbound` 或 `outbound` 策略部分中。
9. 在右侧窗口中，选择“高级策略” > “记录到 EventHub”。 这会插入 `log-to-eventhub` 策略语句模板。

```xml
<log-to-eventhub logger-id="logger-id">
    @{
        return new JObject(
            new JProperty("EventTime", DateTime.UtcNow.ToString()),
            new JProperty("ServiceName", context.Deployment.ServiceName),
            new JProperty("RequestId", context.RequestId),
            new JProperty("RequestIp", context.Request.IpAddress),
            new JProperty("OperationName", context.Operation.Name)
        ).ToString();
    }
</log-to-eventhub>
```
将 `logger-id` 替换为请求 URL 中用于 `{loggerId}` 的值，以创建上一步中的记录器。

可使用返回字符串作为 `log-to-eventhub` 元素值的任何表达式。 在此示例中，将记录一个 JSON 格式的字符串，其中包含日期和时间、服务名称、请求 ID、请求 IP 地址和操作名称。

单击“保存”保存更新后的策略配置。 保存后，策略立即处于活动状态，并且事件记录到指定的事件中心。

> [!NOTE]
> 允许从此 API 管理策略发送到事件中心的最大消息大小为 200 KB。 如果发送到事件中心的消息大于 200 KB，系统会自动截断该消息，而截断的消息则会传输到事件中心。

## <a name="preview-the-log-in-event-hubs-by-using-azure-stream-analytics"></a>使用 Azure 流分析在事件中心预览日志

可以使用 [Azure 流分析查询](../event-hubs/process-data-azure-stream-analytics.md)来预览事件中心中的日志。 

1. 在 Azure 门户中，浏览到记录器发送事件到的事件中心。 
2. 在“功能”下，选择“处理数据”选项卡。 
3. 在“启用来自事件的实时见解”卡片上选择“浏览”。 
4. 可以在“输入预览”选项卡上预览日志。如果显示的数据不是最新的，请选择“刷新”查看最新事件。

## <a name="next-steps"></a>后续步骤
* 了解有关 Azure 事件中心的详细信息
  * [Azure 事件中心入门](../event-hubs/event-hubs-c-getstarted-send.md)
  * [使用 EventProcessorHost 接收消息](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [事件中心编程指南](../event-hubs/event-hubs-programming-guide.md)
* 了解有关 API 管理和事件中心集成的详细信息
  * [记录器实体引用](/rest/api/apimanagement/2020-12-01/logger)
  * [log-to-eventhub 策略引用](./api-management-advanced-policies.md#log-to-eventhub)
  * [使用 Azure API 管理、事件中心和 Moesif 监视 API](api-management-log-to-eventhub-sample.md)  
* 详细了解如何[与 Azure Application Insights 集成](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
