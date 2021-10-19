---
title: Azure 医疗保健 API 的日志记录
description: 本文介绍日志记录的工作原理以及如何为 Azure 医疗保健 API 启用日志记录
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: ginle
ms.openlocfilehash: b71e6088a1a60c17ec16c1a5a265cf20fec53777
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787520"
---
# <a name="logging-for-azure-healthcare-apis-preview"></a>Azure 医疗保健 API (预览版) 

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

Azure 平台提供三种类型的日志、活动日志、资源日志和Azure Active Directory日志。 有关活动日志 [的详细信息，请参阅](../azure-monitor/essentials/platform-logs-overview.md)。 本文介绍 Azure 医疗保健 API 的日志记录工作原理。

## <a name="auditlogs"></a>AuditLogs
虽然活动日志可用于来自 Azure 门户 的每个 Azure 资源，但医疗保健 API 会发出资源日志，其中包括两类日志：AuditLogs 和 DiagnosticLogs。

- AuditLogs 提供医疗保健服务的审核线索，例如，用户或应用程序访问 FHIR 服务时调用方的 IP 地址和资源 URL。 每个服务都会发出所需的属性，并选择性地实现其他属性。
- DiagnosticLogs 提供服务操作见解，例如日志级别 (信息、警告或错误) 日志消息。

目前，医疗保健 API 仅支持公共预览版 AuditLogs。 当服务已普遍可用时，DiagnosticLogs 将可用。

下面是 AuditLog 的一个示例。

```
{
    "time": "2021-08-02 16:01:29Z",
    "resourceId": "/SUBSCRIPTIONS/xxx/RESOURCEGROUPS/xxx/PROVIDERS/MICROSOFT.HEALTHCAREAPIS/SERVICES/xxx",
    "operationName": "Microsoft.HealthcareApis/services/fhir-R4/search-type",
    "category": "AuditLogs",
    "resultType": "Started",
    "resultSignature": 0,
    "durationMs": 0,
    "callerIpAddress": "::ffff:73.164.17.31",
    "correlationId": "5d04211aaf172d43b83d9eb500464ec5",
    "identity": {
        "claims": {
            "iss": "https://sts.windows.net/xxx/",
            "oid": "xxx"
        }
    },
    "level": "Informational",
    "location": "South Central US",
    "uri": "https://xxx.azurehealthcareapis.com:443/Patient",
    "properties": {
        "fhirResourceType": "Patient"
    }
}
```

## <a name="next-steps"></a>后续步骤

可在此处找到医疗保健 API 的最新Azure Monitor [指标](../azure-monitor/essentials/metrics-supported.md)。

有关 DICOM 服务日志和指标的更多详细信息，请参阅 [此处](./dicom/enable-diagnostic-logging.md)。

有关日志和IoT Connector的详细信息，请参阅[此处](./azure-api-for-fhir/iot-metrics-display.md)。