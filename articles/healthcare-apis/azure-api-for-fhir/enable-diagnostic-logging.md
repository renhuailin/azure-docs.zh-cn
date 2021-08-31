---
title: 在 Azure API for FHIR 中启用诊断日志记录
description: 本文介绍如何在 Azure API for FHIR® 中启用诊断日志记录
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 03/03/2021
ms.openlocfilehash: a884dac90273e98868fed6bfe1cbed23b939d286
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778496"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中启用诊断日志记录

在本文中，你将了解如何在 Azure API for FHIR 中启用诊断日志记录，并能够查看这些日志的一些示例查询。 对于必须符合法规要求（如 HIPAA）的所有医疗保健服务，访问诊断日志必不可少。 Azure API for FHIR 中用于启用诊断日志的功能是 Azure 门户中的 [**诊断设置**](../../azure-monitor/essentials/diagnostic-settings.md)。 

## <a name="view-and-download-fhir-metrics-data"></a>查看和下载 FHIR 指标数据

可以从门户的“监视 | 指标”下查看指标。 度量值包括请求数、平均延迟、错误数、数据大小、使用的 RU、超出容量的请求数，以及可用性（以 % 为单位）。 以下屏幕截图显示了 RU 在过去 7 天内用于示例环境的活动很少。 可以下载 JSON 格式的数据。

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="门户中 Azure API for FHIR 的指标" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>启用审核日志
1. 若要在 Azure API for FHIR 中启用诊断日志记录，请在 Azure 门户中选择 Azure API for FHIR 服务 
2. 导航到“诊断设置” 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="添加 Azure FHIR 诊断设置。" lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. 选择“+ 添加诊断设置”

4. 输入设置名称

5. 选择要用于访问诊断日志的方法：

    1. 存档到存储帐户以进行审核或手动检查。 你要使用的存储帐户必须已创建好。
    2. 流式传输到事件中心，以便第三方服务或自定义分析解决方案引入。 需要先创建事件中心命名空间和事件中心策略，然后才能配置此步骤。
    3. 流式传输到 Azure Monitor 中的 Log Analytics 工作区。 需要先创建 Logs Analytics 工作区，然后才能选择此选项。

6. 选择“AuditLogs”和/或“AllMetrics” 。 指标包括服务名称、可用性、数据大小、总延迟、请求总数、总错误数和时间戳。 你可以找到有关[支持的指标](../../azure-monitor/essentials/metrics-supported.md#microsofthealthcareapisservices)的更多详细信息。 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Azure FHIR“诊断设置”。选择“AuditLogs”和/或“AllMetrics”" lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::。

7. 选择“保存”


> [!Note] 
> 第一个日志可能需要 15 分钟才能显示在 Log Analytics 中。 此外，如果 Azure API for FHIR 从一个资源组或订阅移动到另一个资源组或订阅，请在移动完成后更新设置。 
 
若要详细了解如何使用诊断日志，请参阅 [Azure 资源日志文档](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="audit-log-details"></a>审核日志详细信息
此时，Azure API for FHIR 服务将在审核日志中返回以下字段： 

|字段名称  |类型  |说明  |
|---------|---------|---------|
|CallerIdentity|动态|一个包含标识信息的泛型属性包
|CallerIdentityIssuer|字符串|颁发者 
|CallerIdentityObjectId|字符串|Object_Id 
|CallerIPAddress|字符串|调用方的 IP 地址 
|CorrelationId|字符串| 相关性 ID
|FhirResourceType|字符串|已针对其执行操作的资源类型
|LogCategory|字符串|日志类别（当前返回“AuditLogs”LogCategory）
|位置|字符串|已处理请求的服务器的位置（例如，美国中南部）
|OperationDuration|int|完成此请求所花费的时间（以秒为单位）
|OperationName|String| 描述操作类型（例如更新、搜索类型）
|RequestUri|字符串|请求 URI 
|ResultType|字符串|当前可用的值为 Started、Succeeded 或 Failed  
|StatusCode|int|HTTP 状态代码。 （例如，200） 
|TimeGenerated|DateTime|事件的日期和时间|
|属性|字符串| 描述 fhirResourceType 的属性
|SourceSystem|字符串| 源系统（在本例中始终为 Azure）
|TenantId|字符串|租户 ID
|类型|字符串|日志类型（在本例中始终为 MicrosoftHealthcareApisAuditLog）
|_ResourceId|字符串|有关资源的详细信息

## <a name="sample-queries"></a>示例查询

下面是可用于浏览日志数据的几个基本 Application Insights 查询。

运行以下查询可以查看 100 条最新的日志：

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

运行以下查询可以按 FHIR 资源类型对操作进行分组：

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

运行以下查询可以获取所有失败的结果

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>结论 
访问诊断日志对于监视服务和提供符合性报告至关重要。 使用 Azure API for FHIR，可以通过诊断日志来执行这些操作。 
 
FHIR 是 HL7 的注册商标，经 HL7 许可使用。

## <a name="next-steps"></a>后续步骤
在本文中，你已经了解了如何启用 Azure API for FHIR 的审核日志。 接下来，了解可在 Azure API for FHIR 中配置的其他设置
 
>[!div class="nextstepaction"]
>[其他设置](azure-api-for-fhir-additional-settings.md)