---
title: 启用和查询诊断日志
titleSuffix: Azure Digital Twins
description: 了解如何使用诊断设置启用日志记录，以及查询可供立即查看的日志。
author: baanders
ms.author: baanders
ms.date: 8/24/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1479e2b6b715e8f80ea9e02b0b57a3995da2bfd9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219713"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>排查 Azure 数字孪生问题：诊断日志记录

Azure 数字孪生可以收集服务实例的日志，以监视其性能、访问和其他数据。 你可以使用这些日志来大致了解 Azure 数字孪生实例中发生的情况，并分析问题的根本原因，而无需联系 Azure 支持部门。

本文介绍如何在 Azure 门户中[[配置诊断设置](#turn-on-diagnostic-settings)](https://portal.azure.com)，以开始从 Azure 数字孪生实例收集日志。 你还可以指定要将日志存储到的位置（例如 Log Analytics，或者你选择的存储帐户）。

本文还包含 Azure 数字孪生收集的所有[日志类别](#log-categories)和[日志架构](#log-schemas)列表。

设置日志后，还可以[查询日志](#view-and-query-logs)以快速收集自定义见解。

## <a name="turn-on-diagnostic-settings"></a>启用诊断设置 

启用诊断设置，以开始收集 Azure 数字孪生实例上的日志。 你还可以选择要将导出的日志存储到的目标。 下面介绍如何为 Azure 数字孪生实例启用诊断设置。

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到你的 Azure 数字孪生实例。 在门户搜索栏中键入该实例的名称即可找到它。 

2. 从菜单中选择“诊断设置”，然后选择“添加诊断设置”。 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="显示 Azure 门户中“诊断设置”页面和用于添加的按钮的屏幕截图。" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. 在随后出现的页面上填写以下值：
     * **诊断设置名称**：为诊断设置指定一个名称。
     * **类别详细信息**：选择要监视的操作，并选中相应的复选框以针对这些操作启用诊断。 诊断设置可以报告的操作如下：
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        有关这些类别及其包含的信息的更多详细信息，请参阅下面的日志类别部分。
     * **目标详细信息**：选择要将日志发送到的位置。 可选择以下三个选项的任意组合：
        - 发送到 Log Analytics
        - 存档到存储帐户
        - 流式传输到事件中心

        如果选择目标时需要提供更多详细信息，系统可能会要求你填写这些信息。  
    
4. 保存新设置。 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="显示 Azure 门户中的诊断设置页的屏幕截图，其中用户已填写诊断设置信息。" lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

新设置在大约 10 分钟后生效。 然后，日志将重新显示在实例的“诊断设置”页上已配置的目标中。 

有关诊断设置及其设置选项的更多详细信息，请访问创建诊断设置以将平台日志和指标发送到不同的目标。

## <a name="log-categories"></a>日志类别

下面提供了有关 Azure 数字孪生收集的日志类别的更多详细信息。

| 日志类别 | 说明 |
| --- | --- |
| ADTModelsOperation | 记录与模型相关的所有 API 调用 |
| ADTQueryOperation | 记录与查询相关的所有 API 调用 |
| ADTEventRoutesOperation | 记录与事件路由相关的所有 API 调用，以及从 Azure 数字孪生到事件网格、事件中心和服务总线等终结点服务的出口事件 |
| ADTDigitalTwinsOperation | 记录与单个孪生相关的所有 API 调用 |

每个日志类别包括写入、读取、删除和动作操作。 这些类别按如下所示映射到 REST API 调用：

| 事件类型 | REST API 操作 |
| --- | --- |
| 写入 | PUT 和 PATCH |
| 读取 | GET |
| 删除 | DELETE |
| 操作 | POST |

下面是在每个类别中记录的操作和相应 [Azure 数字孪生 REST API 调用](/rest/api/azure-digitaltwins/)的完整列表。 

>[!NOTE]
> 每个日志类别包含若干操作/REST API 调用。 在下表中，每个日志类别映射到其下面的所有操作/REST API 调用，直到列出下一个日志类别。 

| 日志类别 | 操作 | REST API 调用和其他事件 |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | 数字孪生模型更新 API |
|  | Microsoft.DigitalTwins/models/read | 数字孪生模型按 ID 获取 API 和列出 API |
|  | Microsoft.DigitalTwins/models/delete | 数字孪生模型删除 API |
|  | Microsoft.DigitalTwins/models/action | 数字孪生模型添加 API |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | 查询孪生 API |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | 事件路由添加 API |
|  | Microsoft.DigitalTwins/eventroutes/read | 事件路由按 ID 获取 API 和列出 API |
|  | Microsoft.DigitalTwins/eventroutes/delete | 事件路由删除 API |
|  | Microsoft.DigitalTwins/eventroutes/action | 尝试将事件发布到终结点服务时失败（不是 API 调用） |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | 数字孪生添加、添加关系、更新、更新组件 |
|  | Microsoft.DigitalTwins/digitaltwins/read | 数字孪生按 ID 获取、获取组件、按 ID 获取关系、列出传入关系、列出关系 |
|  | Microsoft.DigitalTwins/digitaltwins/delete | 数字孪生删除、删除关系 |
|  | Microsoft.DigitalTwins/digitaltwins/action | 数字孪生发送组件遥测数据、发送遥测数据 |

## <a name="log-schemas"></a>日志架构 

每个日志类别都有一个架构，该架构定义如何报告该类别中的事件。 每个日志条目以文本形式存储，采用 JSON Blob 格式。 下面针对每种日志类型提供了日志中的字段和示例 JSON 正文。 

`ADTDigitalTwinsOperation`、`ADTModelsOperation` 和 `ADTQueryOperation` 使用一致的 API 日志架构。 `ADTEventRoutesOperation` 扩展架构以包含属性中的 `endpointName` 字段。

### <a name="api-log-schemas"></a>API 日志架构

对于 `ADTDigitalTwinsOperation`、`ADTModelsOperation` 和 `ADTQueryOperation`，此日志架构是一致的。 同一架构还用于 `ADTEventRoutesOperation`，但 `Microsoft.DigitalTwins/eventroutes/action` 操作名称除外（有关该架构的详细信息，请参阅下一部分：[出口日志架构](#egress-log-schemas)）。

该架构包含与 Azure 数字孪生实例的 API 调用相关的信息。

下面是 API 日志的字段和属性说明。

| 字段名称 | 数据类型 | 说明 |
|-----|------|-------------|
| `Time` | DateTime | 此事件发生的日期和时间 (UTC) |
| `ResourceId` | 字符串 | 发生该事件的资源的 Azure 资源管理器资源 ID |
| `OperationName` | 字符串  | 发生该事件期间执行的操作类型 |
| `OperationVersion` | 字符串 | 发生该事件期间使用的 API 版本 |
| `Category` | 字符串 | 正在发出的资源的类型 |
| `ResultType` | 字符串 | 事件结果 |
| `ResultSignature` | 字符串 | 事件的 HTTP 状态代码 |
| `ResultDescription` | 字符串 | 有关事件的其他详细信息 |
| `DurationMs` | 字符串 | 执行事件所花费的时间，以毫秒为单位 |
| `CallerIpAddress` | 字符串 | 事件的掩码源 IP 地址 |
| `CorrelationId` | Guid | 客户提供的事件唯一标识符 |
| `ApplicationId` | Guid | 持有者授权中使用的应用程序 ID |
| `Level` | int | 事件的日志记录严重性 |
| `Location` | 字符串 | 发生该事件的区域 |
| `RequestUri` | Uri | 发生该事件期间使用的终结点 |
| `TraceId` | 字符串 | `TraceId`，作为 [W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)的一部分。 用于唯一标识跨系统分布式跟踪的整个跟踪的 ID。 |
| `SpanId` | 字符串 | `SpanId`，作为 [W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)的一部分。 跟踪中此请求的 ID。 |
| `ParentId` | 字符串 | `ParentId`，作为 [W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)的一部分。 没有父 ID 的请求是跟踪的根。 |
| `TraceFlags` | 字符串 | `TraceFlags`，作为 [W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)的一部分。 控制采样、跟踪级别等跟踪标志。 |
| `TraceState` | 字符串 | `TraceState`，作为 [W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)的一部分。 跨越不同分布式跟踪系统的其他供应商特定跟踪标识信息。 |

下面是这些类型的日志的示例 JSON 正文。

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": 8,
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "80",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

下面是一个非 `Microsoft.DigitalTwins/eventroutes/action` 类型的 `ADTEventRoutesOperation` 的 JSON 正文示例（有关该架构的详细信息，请参阅下一部分：[出口日志架构](#egress-log-schemas)）。

```json
  {
    "time": "2020-10-30T22:18:38.0708705Z",
    "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
    "operationName": "Microsoft.DigitalTwins/eventroutes/write",
    "operationVersion": "2020-10-31",
    "category": "EventRoutesOperation",
    "resultType": "Success",
    "resultSignature": "204",
    "resultDescription": "",
    "durationMs": 42,
    "callerIpAddress": "212.100.32.*",
    "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
    "identity": {
      "claims": {
        "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
      }
    },
    "level": "4",
    "location": "southcentralus",
    "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/EventRoutes/egressRouteForEventHub?api-version=2020-10-31",
    "properties": {},
    "traceContext": {
      "traceId": "95ff77cfb300b04f80d83e64d13831e7",
      "spanId": "b630da57026dd046",
      "parentId": "9f0de6dadae85945",
      "traceFlags": "01",
      "tracestate": "k1=v1,k2=v2"
    }
  },
```

### <a name="egress-log-schemas"></a>出口日志架构

以下示例是特定于 `Microsoft.DigitalTwins/eventroutes/action` 操作名称的 `ADTEventRoutesOperation` 日志的架构。 这些日志包含有关连接到 Azure 数字孪生实例的出口终结点的异常和 API 操作的详细信息。

|字段名 | 数据类型 | 说明 |
|-----|------|-------------|
| `Time` | DateTime | 此事件发生的日期和时间 (UTC) |
| `ResourceId` | 字符串 | 发生该事件的资源的 Azure 资源管理器资源 ID |
| `OperationName` | 字符串  | 发生该事件期间执行的操作类型 |
| `Category` | 字符串 | 正在发出的资源的类型 |
| `ResultDescription` | 字符串 | 有关事件的其他详细信息 |
| `CorrelationId` | Guid | 客户提供的事件唯一标识符 |
| `ApplicationId` | Guid | 持有者授权中使用的应用程序 ID |
| `Level` | int | 事件的日志记录严重性 |
| `Location` | 字符串 | 发生该事件的区域 |
| `TraceId` | 字符串 | `TraceId`，作为 [W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)的一部分。 用于唯一标识跨系统分布式跟踪的整个跟踪的 ID。 |
| `SpanId` | 字符串 | `SpanId`，作为 [W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)的一部分。 跟踪中此请求的 ID。 |
| `ParentId` | 字符串 | `ParentId`，作为 [W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)的一部分。 没有父 ID 的请求是跟踪的根。 |
| `TraceFlags` | 字符串 | `TraceFlags`，作为 [W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)的一部分。 控制采样、跟踪级别等跟踪标志。 |
| `TraceState` | 字符串 | `TraceState`，作为 [W3C 跟踪上下文](https://www.w3.org/TR/trace-context/)的一部分。 跨越不同分布式跟踪系统的其他供应商特定跟踪标识信息。 |
| `EndpointName` | 字符串 | 在 Azure 数字孪生中创建的出口终结点的名称 |

下面是这些类型的日志的示例 JSON 正文。

#### <a name="adteventroutesoperation-for-microsoftdigitaltwinseventroutesaction"></a>Microsoft.DigitalTwins/eventroutes/action 的 ADTEventRoutesOperation

下面是 `Microsoft.DigitalTwins/eventroutes/action` 类型的 `ADTEventRoutesOperation` 的 JSON 正文示例。

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "operationVersion": "",
  "category": "EventRoutesOperation",
  "resultType": "",
  "resultSignature": "",
  "resultDescription": "Unable to send EventHub message to [myPath] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "durationMs": -1,
  "callerIpAddress": "",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "",
  "properties": {
    "endpointName": "myEventHub"
  },
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
},
```

## <a name="view-and-query-logs"></a>查看和查询日志

在本文的前面部分，你已配置了要存储的日志类型并指定了其存储位置。

若要排查问题并从这些日志生成见解，可以生成 **自定义查询**。 若要开始，你还可以利用服务为你提供的几个示例查询，这些查询可以解决客户在使用其实例时可能遇到的常见问题。

下面介绍如何查询实例的日志。

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到你的 Azure 数字孪生实例。 在门户搜索栏中键入该实例的名称即可找到它。 

2. 从菜单中选择“日志”打开日志查询页。 该页中将打开一个名为“查询”的窗口。

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="显示 Azure 门户中 Azure 数字孪生实例的日志页的屏幕截图，其中叠加了“查询”窗口，显示了预构建的查询。" lightbox="media/troubleshoot-diagnostics/logs.png":::

    这些查询是为各种日志编写的预生成示例。 可以选择其中一个查询以将其载入查询编辑器，然后运行该查询以查看实例的这些日志。

    还可以在不运行任何查询的情况下关闭“查询”窗口以直接转到查询编辑器页，在其中可以编写或编辑自定义查询代码。

3. 退出“查询”窗口后，你将看到查询编辑器主页。 在此页中可以查看和编辑示例查询的文本，或者从头开始编写你自己的查询。
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="显示 Azure 门户中 Azure 数字孪生实例的日志页的屏幕截图。其中包含了日志列表、查询代码和查询历史记录。" lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    在左窗格中： 
    - “表”选项卡显示可在查询中使用的不同 Azure 数字孪生[日志类别](#log-categories)。 
    - “查询”选项卡包含可加载到编辑器中的示例查询。
    - 在“筛选器”选项卡中可以自定义查询返回的数据的筛选视图。

有关日志查询以及如何编写日志查询的更多详细信息，请访问 Azure Monitor 中的日志查询概述。

## <a name="next-steps"></a>后续步骤

* 有关配置诊断的详细信息，请参阅[从 Azure 资源收集和使用日志数据](../azure-monitor/essentials/platform-logs-overview.md)。
* 若要了解 Azure 数字孪生指标，请参阅故障排除：使用 Azure Monitor 查看指标。
* 若要了解如何对指标启用警报，请参阅故障排除：设置警报。