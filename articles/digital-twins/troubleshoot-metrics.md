---
title: 使用 Azure Monitor 查看指标
titleSuffix: Azure Digital Twins
description: 了解如何在 Azure Monitor 中查看 Azure 数字孪生指标。
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8dc18662431e750301db7e3d2c4e56d5fbaea674
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770930"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>排查 Azure 数字孪生指标问题

可通过本文中描述的指标了解 Azure 订阅中的 Azure 数字孪生资源的状态。 Azure 数字孪生指标可帮助你评估 Azure 数字孪生服务及其关联的资源的整体运行状况。 这些面向用户的统计信息可帮助你了解 Azure 数字孪生的当前情况，还有助于分析问题的根本原因，而无需联系 Azure 支持。

默认启用度量值。 可通过 [Azure 门户](https://portal.azure.com)查看 Azure 数字孪生指标。

## <a name="how-to-view-azure-digital-twins-metrics"></a>如何查看 Azure 数字孪生指标

1. 创建一个 Azure 数字孪生实例。 有关如何设置 Azure 数字孪生实例的说明，可查看[设置实例和身份验证](how-to-set-up-instance-portal.md)。

2. 在 [Azure 门户](https://portal.azure.com)中找到你的 Azure 数字孪生实例（可将其名称输入门户搜索栏打开其页面）。 

    在实例的菜单中，选择“指标”。
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="此屏幕截图显示了 Azure 门户中 Azure 数字孪生的“指标”页面。":::

    此页面显示了 Azure 数字孪生实例的指标。 还可从列表中选择要查看的指标来创建指标的自定义视图。
    
3. 可从菜单中选择“诊断设置”，然后单击“添加诊断设置”，选择将指标数据发送到事件中心终结点或 Azure 存储帐户 。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="此屏幕截图显示了 Azure 门户中“诊断设置”页面和“添加”按钮。":::

    有关此过程的详细信息，请参阅故障排除：设置诊断。

4. 可从菜单中选择“警报”，然后选择“+新建警报规则”，选择为指标数据设置警报 。
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="此屏幕截图显示了 Azure 门户中的“警报”页面和“添加”按钮。":::

    有关此过程的详细信息，请参阅故障排除：设置警报。

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure 数字孪生指标及其使用方法

Azure 数字孪生提供了几项指标，便于你简要了解你的实例及其关联资源的运行状况。 你也可组合来自多个指标的信息，绘制出更详尽的实例状态图。 

下表描述了每个 Azure 数字孪生实例跟踪的指标，以及每个指标与实例整体状态之间的关系。

#### <a name="metrics-for-tracking-service-limits"></a>用于跟踪服务限制的指标

你可配置这些指标，在解决方案的某些方面快要达到[已发布的服务限制](reference-service-limits.md#functional-limits)时进行跟踪。 

若要设置跟踪，请使用 Azure Monitor 中的[警报](troubleshoot-alerts.md)功能。 你可为这些指标定义阈值，以便在指标达到其发布限制的某个百分比时接收警报。

| 指标 | 指标显示名称 | 计价单位 | 聚合类型| 说明 | 维度 |
| --- | --- | --- | --- | --- | --- |
| TwinCount | 孪生体计数（预览） | 计数 | 总计 | Azure 数字孪生实例中的孪生体总数。 使用此指标来确定是否快要达到每个实例允许的最大孪生体数的[服务限制](reference-service-limits.md#functional-limits)。 |  无 |
| ModelCount | 模型计数（预览） | 计数 | 总计 | Azure 数字孪生实例中的模型总数。 使用此指标来确定是否快要达到每个实例允许的最大模型数的[服务限制](reference-service-limits.md#functional-limits)。 | 无 |

#### <a name="api-request-metrics"></a>API 请求指标

与 API 请求相关的指标：

| 指标 | 指标显示名称 | 计价单位 | 聚合类型| 说明 | 维度 |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API 请求 | 计数 | 总计 | 为数字孪生读取、写入、删除和查询操作发出的 API 请求数。 |  身份验证、 <br>操作、 <br>协议、 <br>状态代码、 <br>状态代码类、 <br>状态文本 |
| ApiRequestsFailureRate | API 请求失败率 | 百分比 | 平均值 | 服务为实例收到的，针对数字孪生读取、写入、删除和查询操作显示内部错误 (500) 响应代码的 API 请求百分比。 | 身份验证、 <br>操作、 <br>协议、 <br>状态代码、 <br>状态代码类、 <br>状态文本
| ApiRequestsLatency | API 请求延迟 | 毫秒 | 平均值 | API 请求的响应时间。 此值指从 Azure 数字孪生收到请求到服务发送数字孪生读取、写入、删除和查询操作的结果（成功/失败）之间的时间。 | 身份验证、 <br>操作、 <br>协议 |

#### <a name="billing-metrics"></a>计费指标

与计费相关的指标：

| 指标 | 指标显示名称 | 计价单位 | 聚合类型| 说明 | 维度 |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | 计费 API 操作 | 计数 | 总计 | 针对 Azure 数字孪生服务发出的所有 API 请求的计数的计费指标。 | Meter ID |
| BillingMessagesProcessed | 计费已处理的消息数 | 计数 | 总计 | 从 Azure 数字孪生发送到外部终结点的消息数量的计费指标。<br><br>若要在计费时将其看作一条消息，一个有效负载不得大于 1 KB。 大于此限值的有效负载将按 1 KB 的增量计为附加消息（因此，1 KB 到 2 KB 之间的消息将计为 2 条消息，2 KB 到 3 KB 之间的消息将计为 3 条消息，依此类推）。<br>此限制也适用于响应；例如，在响应正文中返回 1.5 KB 内容的调用将作为 2 个操作进行计费。 | Meter ID |
| BillingQueryUnits | 计费查询单元 | 计数 | 总计 | 用于执行查询的查询单位数（服务资源使用情况的内部计算度量值）。 还有一个可用于度量查询单元的帮助程序 API：[QueryChargeHelper 类](/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet&preserve-view=true) | Meter ID |

若要更详细地了解 Azure 数字孪生的计费方式，请参阅 [Azure 数字孪生定价](https://azure.microsoft.com/pricing/details/digital-twins/)。

#### <a name="ingress-metrics"></a>入口指标

与数据入口相关的指标：

| 指标 | 指标显示名称 | 计价单位 | 聚合类型| 说明 | 维度 |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | 入口事件 | 计数 | 总计 | 传入 Azure 数字孪生的遥测事件数。 | 结果 |
| IngressEventsFailureRate | 入口事件失败率 | 百分比 | 平均值 | 服务对其返回内部错误 (500) 响应代码的传入遥测事件百分比。 | 结果 |
| IngressEventsLatency | 入口事件延迟 | 毫秒 | 平均值 | 从事件到达到可由 Azure 数字孪生传出之间的时间，传出时服务会发送结果（成功/失败）。 | 结果 |

#### <a name="routing-metrics"></a>路由指标

与路由相关的指标：

| 指标 | 指标显示名称 | 计价单位 | 聚合类型| 说明 | 维度 |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | 路由的消息数 | 计数 | 总计 | 路由到终结点 Azure 服务（如事件中心、服务总线或事件网格）的消息数。 | 终结点类型、 <br>结果 |
| RoutingFailureRate | 路由失败率 | 百分比 | 平均值 | 在将事件从 Azure 数字孪生路由到终结点 Azure 服务（如事件中心、服务总线或事件网格）时导致出现错误的事件百分比。 | 终结点类型、 <br>结果 |
| RoutingLatency | 路由延迟 | 毫秒 | 平均值 | 事件从 Azure 数字孪生路由到将其发布到终结点 Azure 服务（如事件中心、服务总线或事件网格）之间经过的时间。 | 终结点类型、 <br>结果 |

## <a name="dimensions"></a>维度

维度有助于标识有关指标的更多详细信息。 某些路由指标提供了每个终结点的信息。 下表列出了这些维度可能具有的值。

| 维度 | 值 |
| --- | --- |
| 身份验证 | OAuth |
| 操作（对于 API 请求） | Microsoft.DigitalTwins/digitaltwins/delete、 <br>Microsoft.DigitalTwins/digitaltwins/write、 <br>Microsoft.DigitalTwins/digitaltwins/read、 <br>Microsoft.DigitalTwins/eventroutes/read、 <br>Microsoft.DigitalTwins/eventroutes/write、 <br>Microsoft.DigitalTwins/eventroutes/delete、 <br>Microsoft.DigitalTwins/models/read、 <br>Microsoft.DigitalTwins/models/write、 <br>Microsoft.DigitalTwins/models/delete、 <br>Microsoft.DigitalTwins/query/action |
| 终结点类型 | 事件网格、 <br>事件中心、 <br>服务总线 |
| 协议 | HTTPS |
| 结果 | 成功、 <br>失败 |
| 状态代码 | 200、404、500 等等。 |
| 状态代码类 | 2xx、4xx、5xx 等等。 |
| 状态文本 | “内部服务器错误”、“找不到”等等。 |

## <a name="next-steps"></a>后续步骤

若要详细了解如何管理为 Azure 数字孪生记录的指标，请参阅排除故障：设置诊断。
