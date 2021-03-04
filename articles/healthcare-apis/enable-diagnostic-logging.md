---
title: 在 Azure API for FHIR 中启用诊断日志记录
description: 本文介绍如何在 Azure API for FHIR 中启用诊断日志记录®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 03/03/2021
ms.openlocfilehash: 8dde5c6206087c8bb1c1f456a0c858abbccf0cc4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096303"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中启用诊断日志记录

本文介绍如何在 Azure API for FHIR 中启用诊断日志记录，以及如何查看这些日志的一些示例查询。 对于符合法规要求 (如 HIPAA) 的任何医疗保健服务，访问诊断日志是必需的。 启用诊断日志的 Azure API for FHIR 中的功能是 Azure 门户中的 [**诊断设置**](../azure-monitor/essentials/diagnostic-settings.md) 。 

## <a name="view-and-download-fhir-metrics-data"></a>查看和下载 FHIR 指标数据

你可以查看 "监视" 下的指标 |门户中的指标。 度量值包括请求数、平均延迟、错误数、数据大小、使用的 ru、超出容量的请求数，以及% ) 中的可用性 (。 下面的屏幕截图显示了用于在过去7天内非常少的活动的示例环境中使用的 ru。 您可以下载 Json 格式的数据。

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="门户中用于 FHIR 指标的 Azure API" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>启用审核日志
1. 若要在 Azure API for FHIR 中启用诊断日志记录，请在 Azure 门户中选择 Azure API for FHIR 服务 
2. 导航到 "**诊断设置**" 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="添加 Azure FHIR 诊断设置。" lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. 选择“+ 添加诊断设置”

4. 输入设置的名称

5. 选择要用于访问诊断日志的方法：

    1. **存档到存储帐户** 进行审核或手动检查。 需要使用的存储帐户需要已创建。
    2. **流式传输到事件中心** ，以供第三方服务或自定义分析解决方案引入。 你将需要创建事件中心命名空间和事件中心策略，然后才能配置此步骤。
    3. **流到 Azure Monitor 中的 Log Analytics** 工作区。 你将需要创建日志分析工作区，然后才能选择此选项。

6. 选择 **AuditLogs** 和/或 **AllMetrics**。 指标包括服务名称、可用性、数据大小、总延迟、请求总数、错误总数和时间戳。 可以在 [支持的指标](https://docs.microsoft.com/azure/azure-monitor/essentials/metrics-supported#microsofthealthcareapisservices)中找到更多详细信息。 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Azure FHIR 诊断设置。选择 AuditLogs 和/或 AllMetrics。" lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. 选择“保存”


> [!Note] 
> 最多可能需要15分钟才能在 Log Analytics 中显示第一个日志。 此外，如果将 FHIR 的 Azure API 从一个资源组或订阅移动到另一个资源组或订阅，请在移动完成后更新设置。 
 
有关如何使用诊断日志的详细信息，请参阅 [Azure 资源日志文档](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="audit-log-details"></a>审核日志详细信息
目前，Azure API for FHIR 服务会在审核日志中返回以下字段： 

|字段名称  |类型  |说明  |
|---------|---------|---------|
|CallerIdentity|动态|包含标识信息的泛型属性包
|CallerIdentityIssuer|String|颁发者 
|CallerIdentityObjectId|String|Object_Id 
|CallerIPAddress|String|调用方的 IP 地址 
|CorrelationId|String| 相关性 ID
|FhirResourceType|String|已对其执行操作的资源类型
|LogCategory|String|日志类别 (我们当前返回的是 "AuditLogs" LogCategory) 
|位置|String|处理请求的服务器的位置 (例如，美国中南部) 
|OperationDuration|int|完成此请求所需的时间（秒）
|OperationName|String| 描述操作类型 (例如更新、搜索类型) 
|RequestUri|String|请求 URI 
|ResultType|String|当前 **已启动**、**成功** 或 **失败** 的可用值
|StatusCode|int|HTTP 状态代码。  (，例如 200)  
|TimeGenerated|DateTime|事件的日期和时间|
|属性|String| 介绍 fhirResourceType 的属性
|SourceSystem|String| 在这种情况下，源系统 (总是 Azure) 
|TenantId|String|租户 ID
|类型|字符串|在这种情况下，日志类型 (始终 MicrosoftHealthcareApisAuditLog) 
|_ResourceId|String|有关资源的详细信息

## <a name="sample-queries"></a>示例查询

下面是一些可以用来浏览日志数据的基本 Application Insights 查询。

运行此查询可查看 **100 个最新** 日志：

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

运行以下查询，按 **FHIR 资源类型** 对操作进行分组：

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

运行此查询以获取所有 **失败的结果**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>结论 
有权访问诊断日志对于监视服务和提供相容性报告非常重要。 使用用于 FHIR 的 Azure API 可以通过诊断日志执行这些操作。 
 
FHIR 是 HL7 的注册商标，经 HL7 许可使用。

## <a name="next-steps"></a>后续步骤
本文介绍了如何为适用于 FHIR 的 Azure API 启用审核日志。 接下来，了解可以在用于 FHIR 的 Azure API 中配置的其他其他设置
 
>[!div class="nextstepaction"]
>[其他设置](azure-api-for-fhir-additional-settings.md)
