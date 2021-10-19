---
title: 在 FHIR 服务中查看和启用诊断设置-Azure 医疗保健 Api
description: 本文介绍如何在 FHIR 服务中启用诊断设置，并查看审核日志的一些示例查询。
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: how-to
ms.date: 10/12/2021
ms.author: zxue
ms.openlocfilehash: 7574d4abf20e07019016796e937cf80c7d915128
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2021
ms.locfileid: "130187402"
---
# <a name="view-and-enable-diagnostic-settings-in-the-fhir-service"></a>在 FHIR 服务中查看和启用诊断设置

诊断日志的访问权限对于任何医疗保健服务都是必需的。 符合健康保险便携性和责任法案 (HIPAA) 是必需的。 本文介绍如何在 Azure 医疗保健 Api 中选择 FHIR 服务中的诊断日志设置。 您还将查看这些日志的一些示例查询。

> [!IMPORTANT]
> Azure 医疗保健 Api 服务当前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包括适用于 Azure 功能的法律条款，这些功能处于 beta 版本、预览版中，或者其他情况下尚未公开上市。

## <a name="steps-to-enable-diagnostic-logs"></a>启用诊断日志的步骤

1. 在 Azure 门户中选择你的 FHIR 服务。

2. 在“监视”下，选择“诊断设置” 。

3. 选择 " **+ 添加诊断设置**"。

   [![Azure 门户中的 "诊断设置" 页的屏幕截图。 ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png) ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png#lightbox)

4. 输入 **诊断设置名称**。
 
   [![目标详细信息的屏幕截图和用于启用或禁用审核日志的复选框。 ](media/diagnostic-logs/fhir-diagnostic-settings-add.png) ](media/diagnostic-logs/fhir-diagnostic-settings-add.png#lightbox)

5. 选择要用于访问日志的方法：

   - **发送到 Log Analytics 工作区** 用于将日志和指标发送到 Azure Monitor 中的 Log Analytics 工作区。 你需要创建 Log Analytics 工作区，然后才能选择此选项。
   
   - **存档到存储帐户** 用于审核或手动检查。 需要使用的存储帐户需要已创建。 保留选项仅适用于存储帐户。 保留策略范围为1至365天。 如果不想应用任何保留策略并永久保留数据，请将保留期 (天) 设置为0。

   - **流式传输到事件中心** ，以供第三方服务或自定义分析解决方案引入。 需要先创建事件中心命名空间和事件中心策略，然后才能配置此选项。
   
   - 如果已启用 Azure 支持的合作伙伴解决方案，则应选择 "**发送到合作伙伴解决方案**"。 有关详细信息，请参阅 [利用合作伙伴提供的解决方案扩展 Azure](../../partner-solutions/overview.md)。

6. 选择 **AuditLogs**。

7. 选择“保存”。

> [!NOTE]
> 如果你选择了该方法，则第一个日志可能需要长达15分钟的时间才能显示在 "Log Analytics" 工作区中。 如果 FHIR 服务从一个资源组或订阅移动到另一个资源组或订阅，请在移动完成后更新设置。


## <a name="diagnostic-log-details"></a>诊断日志详细信息

此时，FHIR 服务将在诊断日志中返回以下字段：

|字段名称|类型|注释|
|----------|----|-----|
|`CallerIdentity` |动态|一个包含标识信息的泛型属性包。|
|`CallerIdentityIssuer` | 字符串| 发行人。|
|`CallerIdentityObjectId` | 字符串| 对象 ID。|
|`CallerIPAddress` | 字符串| 调用方的 IP 地址。|
|`CorrelationId` | 字符串| 相关 ID。|
|`FhirResourceType` | 字符串| 为其执行操作的资源类型。|
|`LogCategory` | 字符串| 日志类别。 本文中 (，我们将返回 `AuditLogs` 。 ) |
|`Location` | 字符串| 处理请求的服务器的位置。 例如：`South Central US`。|
|`OperationDuration` | int| 完成此请求所需的时间（以秒为单位）。|
|`OperationName` | 字符串| 操作的类型。 例如 `update` 或 `search-type`。|
|`RequestUri` | 字符串| 请求 URI。|
|`ResultType` | 字符串| 日志的状态。 可用值为 `Started` 、 `Succeeded` 或 `Failed` 。|
|`StatusCode` | int| HTTP 状态代码。 例如：`200`。|
|`TimeGenerated` | DateTime| 事件的日期和时间。|
|`Properties` | 字符串| 的属性 `FhirResourceType` 。|
|`SourceSystem` | 字符串| 源系统，始终 `Azure` 在此情况下。|
|`TenantId` | 字符串 | 租户 ID。|
|`Type` | 字符串| 日志类型， `MicrosoftHealthcareApisAuditLog` 在本例中为。|
|`_ResourceId` | 字符串| 有关资源的详细信息。|       
        
## <a name="sample-queries"></a>示例查询

可以使用以下基本 Application Insights 查询来浏览日志数据：

- 运行以下查询以查看 *100 最新* 日志：

  `Insights
  MicrosoftHealthcareApisAuditLogs
  | limit 100`

- 运行以下查询，按 *FHIR 资源类型* 对操作进行分组：

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | summarize count() by FhirResourceType`

- 运行以下查询以获取所有失败的 *结果*：

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | where ResultType == "Failed"`   

## <a name="conclusion"></a>结论

能够访问诊断日志对于监视服务和提供符合性报告至关重要。 本文介绍了如何为 FHIR 服务启用这些日志。 

> [!NOTE]
> Azure 医疗保健 Api 服务正式发布时，将添加指标。

## <a name="next-steps"></a>后续步骤

有关 FHIR 服务的概述，请参阅：

>[!div class="nextstepaction"]
>[什么是 FHIR 服务？](overview.md)   

 (FHIR&#174;) 是 [HL7](https://hl7.org/fhir/) 的注册商标，并与 HL7 的权限一起使用。
