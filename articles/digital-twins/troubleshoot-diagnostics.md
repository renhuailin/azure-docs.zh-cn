---
title: 启用和查询诊断日志
titleSuffix: Azure Digital Twins
description: 请参阅如何使用诊断设置启用日志记录和查询日志以便立即查看。
author: baanders
ms.author: baanders
ms.date: 11/9/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c600ced8896a3847b80d854c9e230310cca4c98d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588594"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure 数字孪生故障排除：诊断日志记录

Azure 数字孪生可以收集服务实例的日志来监视其性能、访问权限和其他数据。 你可以使用这些日志来了解 Azure 数字孪生实例中发生的情况，并对问题执行根本原因分析，而无需联系 Azure 支持部门。

本文介绍如何在 [Azure 门户](https://portal.azure.com)中 [**配置诊断设置**](#turn-on-diagnostic-settings)，以开始从 Azure 数字孪生实例收集日志。 你还可以指定日志的存储位置 (例如 Log Analytics 或所选) 的存储帐户。

本文还包含 Azure 数字孪生收集的所有 [日志类别](#log-categories) 和 [日志架构](#log-schemas) 的列表。

设置日志后，还可以 [**查询日志**](#view-and-query-logs) 以快速收集自定义的见解。

## <a name="turn-on-diagnostic-settings"></a>启用诊断设置 

启用诊断设置，开始收集 Azure 数字孪生实例上的日志。 你还可以选择要在其中存储已导出日志的目标。 下面介绍如何为 Azure 数字孪生实例启用诊断设置。

1. 登录到 [Azure 门户](https://portal.azure.com) 并导航到 Azure 数字孪生实例。 可以通过在门户搜索栏中键入其名称来找到它。 

2. 从菜单中选择 " **诊断设置** "，然后单击 " **添加诊断设置**"。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="显示 &quot;诊断设置&quot; 页和要添加的按钮的屏幕截图" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. 在下面的页面上，填写以下值：
     * **诊断设置名称**：为诊断设置指定一个名称。
     * **类别详细信息**：选择要监视的操作，并选中相应的复选框以对这些操作启用诊断。 诊断设置可以报告的操作如下：
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        有关这些类别及其包含的信息的详细信息，请参阅下面的 " [*日志类别*](#log-categories) " 一节。
     * **目标详细信息**：选择要将日志发送到的位置。 可选择以下三个选项的任意组合：
        - 发送到 Log Analytics
        - 存档到存储帐户
        - 流式传输到事件中心

        如果目标选择需要，系统可能会要求你填写其他详细信息。  
    
4. 保存新设置。 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="显示 &quot;诊断设置&quot; 页的屏幕截图，其中用户填写了诊断设置名称，并对类别详细信息和目标详细信息进行了一些复选框选择。突出显示 &quot;保存&quot; 按钮。" lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

新设置在大约 10 分钟后生效。 之后，日志会在实例的 " **诊断设置** " 页上显示在配置的目标中。 

有关诊断设置及其设置选项的更多详细信息，可以访问 [*创建诊断设置，将平台日志和指标发送到不同的目标*](../azure-monitor/essentials/diagnostic-settings.md)。

## <a name="log-categories"></a>日志类别

下面是有关 Azure 数字孪生收集的日志类别的更多详细信息。

| 日志类别 | 说明 |
| --- | --- |
| ADTModelsOperation | 记录与模型有关的所有 API 调用 |
| ADTQueryOperation | 记录与查询相关的所有 API 调用 |
| ADTEventRoutesOperation | 记录与事件路由相关的所有 API 调用，以及从 Azure 数字孪生传出事件到终结点服务（如事件网格、事件中心和服务总线）的事件 |
| ADTDigitalTwinsOperation | 记录与 Azure 数字孪生有关的所有 API 调用 |

每个日志类别由写入、读取、删除和操作操作组成。  它们映射到 REST API 调用，如下所示：

| 事件类型 | REST API 操作 |
| --- | --- |
| 写入 | PUT 和 PATCH |
| 读取 | GET |
| 删除 | DELETE |
| 操作 | POST |

下面是在每个类别中记录的操作和相应的 [Azure 数字孪生 REST API 调用](/rest/api/azure-digitaltwins/) 的完整列表。 

>[!NOTE]
> 每个日志类别包含若干个操作/REST API 调用。 在下表中，每个日志类别映射到其下的所有操作/REST API 调用，直到列出下一个日志类别。 

| 日志类别 | Operation | REST API 调用和其他事件 |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | 数字克隆模型更新 API |
|  | Microsoft.DigitalTwins/models/read | 数字克隆模型按 ID 和列表 Api 获取 |
|  | Microsoft.DigitalTwins/models/delete | 数字克隆模型删除 API |
|  | DigitalTwins/模型/操作 | 数字克隆模型添加 API |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | 查询孪生 API |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | 事件路由添加 API |
|  | Microsoft.DigitalTwins/eventroutes/read | 事件路由由 ID 和列表 Api 获取 |
|  | Microsoft.DigitalTwins/eventroutes/delete | 事件路由删除 API |
|  | DigitalTwins/eventroutes/action | 尝试将事件发布到终结点服务时失败， (不是 API 调用)  |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | 数字孪生添加、添加关系、更新、更新组件 |
|  | Microsoft.DigitalTwins/digitaltwins/read | 数字孪生按 ID、获取组件、按 ID 获取关系、列出传入关系、列表关系 |
|  | Microsoft.DigitalTwins/digitaltwins/delete | 数字孪生删除，删除关系 |
|  | DigitalTwins/DigitalTwins/action | 数字孪生发送组件遥测数据，发送遥测数据 |

## <a name="log-schemas"></a>日志架构 

每个日志类别都有一个架构，该架构定义如何报告该类别中的事件。 每个单独的日志条目都作为文本存储，并格式化为 JSON blob。 下面为每种日志类型提供了日志中的字段和示例 JSON 正文。 

`ADTDigitalTwinsOperation`、 `ADTModelsOperation` 和 `ADTQueryOperation` 使用一致的 API 日志架构; `ADTEventRoutesOperation` 具有其自己的单独架构。

### <a name="api-log-schemas"></a>API 日志架构

此日志架构对于、和是一致的 `ADTDigitalTwinsOperation` `ADTModelsOperation` `ADTQueryOperation` 。 它包含与 Azure 数字孪生实例的 API 调用相关的信息。

下面是 API 日志的字段和属性描述。

| 字段名称 | 数据类型 | 说明 |
|-----|------|-------------|
| `Time` | DateTime | 此事件发生的日期和时间（UTC） |
| `ResourceID` | 字符串 | 发生事件的资源的 Azure 资源管理器资源 ID |
| `OperationName` | 字符串  | 事件期间执行的操作类型 |
| `OperationVersion` | 字符串 | 事件期间使用的 API 版本 |
| `Category` | 字符串 | 正在发出的资源的类型 |
| `ResultType` | 字符串 | 事件的结果 |
| `ResultSignature` | 字符串 | 事件的 Http 状态代码 |
| `ResultDescription` | 字符串 | 有关事件的其他详细信息 |
| `DurationMs` | 字符串 | 执行事件所花的时间（以毫秒为单位） |
| `CallerIpAddress` | 字符串 | 事件的掩码源 IP 地址 |
| `CorrelationId` | Guid | 客户提供的事件的唯一标识符 |
| `Level` | 字符串 | 事件的日志记录严重性 |
| `Location` | 字符串 | 发生事件的区域 |
| `RequestUri` | Uri | 事件发生时使用的终结点 |

下面是这些日志类型的示例 JSON 正文。

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
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31"
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
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
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
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
}
```

### <a name="egress-log-schemas"></a>出口日志架构

这是日志的架构 `ADTEventRoutesOperation` 。 其中包含有关异常的详细信息，以及有关连接到 Azure 数字孪生实例的出口终结点的 API 操作的详细信息。

|字段名称 | 数据类型 | 说明 |
|-----|------|-------------|
| `Time` | DateTime | 此事件发生的日期和时间（UTC） |
| `ResourceId` | 字符串 | 发生事件的资源的 Azure 资源管理器资源 ID |
| `OperationName` | 字符串  | 事件期间执行的操作类型 |
| `Category` | 字符串 | 正在发出的资源的类型 |
| `ResultDescription` | 字符串 | 有关事件的其他详细信息 |
| `Level` | 字符串 | 事件的日志记录严重性 |
| `Location` | 字符串 | 发生事件的区域 |
| `EndpointName` | 字符串 | 在 Azure 数字孪生中创建的出口终结点的名称 |

下面是这些日志类型的示例 JSON 正文。

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="view-and-query-logs"></a>查看和查询日志

本文前面介绍了如何配置要存储的日志类型，以及如何指定其存储位置。

若要解决此类日志的问题并生成见解，可以生成 **自定义查询**。 若要开始，你还可以利用服务为你提供的几个示例查询，以解决客户可能遇到的有关其实例的常见问题。

下面介绍如何查询实例的日志。

1. 登录到 [Azure 门户](https://portal.azure.com) 并导航到 Azure 数字孪生实例。 可以通过在门户搜索栏中键入其名称来找到它。 

2. 从菜单中选择 " **日志** "，打开 "日志查询" 页。 页面将打开一个名为 " *查询*" 的窗口。

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="显示 Azure 数字孪生实例的日志页的屏幕截图。它与 &quot;查询&quot; 窗口重叠，其中显示了在不同日志选项后命名的预生成查询，如 DigitalTwin API 滞后时间和模型 API 滞后时间。" lightbox="media/troubleshoot-diagnostics/logs.png":::

    这些是为各种日志编写的预生成的示例查询。 您可以选择其中一个查询以将其加载到查询编辑器中，并运行它以查看实例的这些日志。

    您还可以在不运行任何内容的情况下关闭 " *查询* " 窗口，直接转到 "查询编辑器" 页，您可以在其中编写或编辑自定义的查询代码。

3. 在退出 " *查询* " 窗口后，您将看到 "查询编辑器" 主页面。 可在此处查看和编辑示例查询的文本，或者从头开始编写自己的查询。
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="显示 Azure 数字孪生实例的日志页的屏幕截图。&quot;查询&quot; 窗口已消失，相反，有一个列表，其中包含不同的日志、一个显示可编辑的查询代码的编辑窗格和一个显示查询历史记录的窗格。" lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    在左窗格中， 
    - " *表* " 选项卡显示了可在你的查询中使用的不同 Azure 数字孪生 [日志类别](#log-categories) 。 
    - " *查询* " 选项卡包含可加载到编辑器中的示例查询。
    - 利用 " *筛选器* " 选项卡，您可以自定义查询返回的数据的筛选视图。

有关日志查询以及如何编写日志查询的更多详细信息，可以访问 [*Azure Monitor 中日志查询的概述*](../azure-monitor/logs/log-query-overview.md)。

## <a name="next-steps"></a>后续步骤

* 有关配置诊断的详细信息，请参阅 [*从 Azure 资源收集和使用日志数据*](../azure-monitor/essentials/platform-logs-overview.md)。
* 有关 Azure 数字孪生指标的信息，请参阅 [*故障排除：查看包含 Azure Monitor 的指标*](troubleshoot-metrics.md)。
* 若要了解如何启用指标警报，请参阅 [*故障排除：设置警报*](troubleshoot-alerts.md)。