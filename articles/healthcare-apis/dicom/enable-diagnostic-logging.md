---
title: 在 DICOM 服务中启用诊断日志记录 - Azure Healthcare APIs
description: 本文介绍如何在 DICOM 服务中启用诊断日志记录。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 51a162ccbad5813a559cf27c26adcd964adeaf07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778397"
---
# <a name="enable-diagnostic-logging-in-the-dicom-service"></a>在 DICOM 服务中启用诊断日志记录

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本文中，你将了解如何在 DICOM 服务中启用诊断日志记录，并能够查看这些日志的一些示例查询。 对于必须符合法规要求的所有医疗保健服务，访问诊断日志必不可少。 DICOM 服务中用于启用诊断日志的功能是 Azure 门户中的[诊断设置](../../azure-monitor/essentials/diagnostic-settings.md)。 

## <a name="enable-audit-logs"></a>启用审核日志

1. 若要启用 DICOM 服务的诊断日志记录，请在 Azure 门户中选择 DICOM 服务。
2. 选择“活动日志”边栏选项卡，然后选择“诊断设置”。

   [ ![Azure 活动日志。](media/dicom-activity-log.png) ](media/dicom-activity-log.png#lightbox)

3. 选择“+ 添加诊断设置”。 

   [ ![AAD 诊断设置。](media/add-diagnostic-settings.png) ](media/add-diagnostic-settings.png#lightbox)

4. 输入“诊断设置名称”。

   [ ![配置诊断设置。](media/configure-diagnostic-settings.png) ](media/configure-diagnostic-settings.png#lightbox)

5. 选择用于访问诊断日志的“类别”和“目标”详细信息。

   * Azure Monitor 中的“发送到 Log Analytics 工作区”。 需要先创建 Logs Analytics 工作区，然后才能选择此选项。 有关平台日志详细信息，请参阅 [Azure 平台日志概述](../../azure-monitor/essentials/platform-logs-overview.md)。
   * 存档到存储帐户以进行审核或手动检查。 你要使用的存储帐户必须已创建好。
   * 流式传输到事件中心，以便第三方服务或自定义分析解决方案引入。 需要先创建事件中心命名空间和事件中心策略，然后才能配置此步骤。
   * 发送给合作伙伴解决方案，你在 Azure 中作为合作伙伴组织使用。 有关潜在合作伙伴集成的信息，请参阅 [Azure 合作伙伴解决方案文档](../../partner-solutions/overview.md)

     有关支持的指标的信息，请参阅 [Azure Monitor 中支持的指标。](.././../azure-monitor/essentials/metrics-supported.md)

6. 选择“保存”。


   > [!Note] 
   > 第一个日志可能需要 15 分钟才能显示在 Log Analytics 中。 此外，如果 DICOM 服务从一个资源组或订阅移动到另一个资源组或订阅，请在移动完成后更新设置。 
 
   若要详细了解如何使用诊断日志，请参阅 [Azure 资源日志文档](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="audit-log-details"></a>审核日志详细信息

DICOM 服务在审核日志中返回以下字段： 

|字段名称  |类型  |说明  |
|---------|---------|---------|
|correlationId|字符串|相关性 ID
|category|字符串|日志类别（当前有“AuditLogs”） 
|operationName|字符串|描述操作类型（例如检索、存储、查询等） 
|time|DateTime|事件的日期和时间。 
|ResourceId|字符串| 资源的 Azure 路径。
|标识|动态|包含标识信息的泛型属性包（当前不适用于 DICOM）。
|callerIpAddress|字符串|调用方的 IP 地址。
|位置|字符串|处理请求的服务器的位置。
|uri|字符串|请求 URI。
|resultType|字符串| 当前可用的值为 Started、Succeeded 或 Failed。
|resultSignature|int|HTTP 状态代码（例如 200）
|properties|字符串|描述属性，包括资源类型、资源名称、订阅 ID、审核操作等。
|type|String|日志类型（在本例中始终为 MicrosoftHealthcareApisAuditLog）。
|级别|字符串|日志级别（信息、错误）。
|operationVersion|字符串| 当前为空。 将利用它来显示 API 版本。


## <a name="sample-queries"></a>示例查询

下面是可用于浏览日志数据的几个基本 Application Insights 查询。

运行以下查询可以查看 100 条最新的日志：

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

运行以下查询，按 DICOM 资源类型对操作进行分组：

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by DICOMResourceType
```

运行以下查询可以获取所有失败的结果

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>结论

能够访问诊断日志对于监视服务和提供符合性报告至关重要。 使用 DICOM 服务，可以通过诊断日志来执行这些操作。 

## <a name="next-steps"></a>后续步骤
在本文中，你已经了解了如何为 DICOM 服务启用审核日志。 有关 Azure 活动日志的信息，请参阅
 
>[!div class="nextstepaction"]
>[Azure 活动日志事件架构](.././../azure-monitor/essentials/activity-log-schema.md)
