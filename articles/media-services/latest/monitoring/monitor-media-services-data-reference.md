---
title: 监视媒体服务数据参考
description: 监视媒体服务时所需的重要参考资料
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 04/21/2021
ms.openlocfilehash: a9bc17528e1314a033cf2e45fee4b112eb088cc3
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109715567"
---
# <a name="monitoring-media-services-data-reference"></a>监视媒体服务数据参考

本文介绍对监视媒体服务有用的数据。 有关 Azure Monitor 支持的所有平台指标的详细信息，请查看 [Azure Monitor 支持的指标](../../../azure-monitor/essentials/metrics-supported.md)。

## <a name="metrics"></a>指标

不管值是否变化，指标都按固定的时间间隔进行收集。 指标可用于警报，因为它们可以频繁采样，而警报则可以使用相对简单的逻辑快速触发。


媒体服务支持监视以下资源的指标：

|指标类型 | 资源提供程序/类型命名空间<br/> 和到各个指标的链接 |
|-------|-----|
| 常规媒体服务 | 常规 |
| 直播活动 | [Microsoft.Media/mediaservices/liveEvents](../../../azure-monitor/essentials/metrics-supported.md#microsoftmediamediaservicesliveevents) 
| 流式处理终结点 | 与[流式处理终结点 REST API](/rest/api/media/streamingendpoints) 相关的 [Microsoft.Media/mediaservices/streamingEndpoints](../../../azure-monitor/essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints)。 


还应查看[帐户配额和限制](../limits-quotas-constraints-reference.md)。


## <a name="metric-dimensions"></a>指标维度

有关指标维度定义的详细信息，请参阅[多维指标](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。

媒体服务具有以下指标维度。  根据其所支持的指标，非常易于理解它们。  请参阅上面的[指标链接](#metrics)了解详细信息。   
- OutputFormat
- HttpStatusCode 
- ErrorCode 
- TrackName 

## <a name="resource-logs"></a>资源日志

诊断日志提供有关 Azure 资源操作频繁生成的丰富数据。 有关详细信息，请参阅[如何从 Azure 资源收集和使用日志数据](../../../azure-monitor/essentials/platform-logs-overview.md)。

媒体服务支持以下资源日志：[Microsoft.Media/mediaservices](../../../azure-monitor/essentials/resource-logs-categories.md#microsoftmediamediaservices)

## <a name="schemas"></a>架构

有关顶级诊断日志架构的详细说明，请参阅 [Azure 诊断日志支持的服务、架构和类别](../../../azure-monitor/essentials/resource-logs-schema.md)。

### <a name="key-delivery"></a>密钥传送 

这些属性特定于密钥传递日志架构。

|名称|说明|
|---|---|
|keyId|所请求密钥的 ID。|
|keyType|可以是以下值之一：“Clear”（不加密）、“FairPlay”、“PlayReady”或“Widevine”。|
|policyName|策略的 Azure 资源管理器名称。|
|tokenType|令牌类型。|
|statusMessage|状态消息。|

### <a name="example"></a>示例

密钥传递请求架构的属性。

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

>[!NOTE]
> Widevine 是 Google Inc. 提供的一项服务，并受 Google Inc. 服务条款和隐私策略的约束。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
