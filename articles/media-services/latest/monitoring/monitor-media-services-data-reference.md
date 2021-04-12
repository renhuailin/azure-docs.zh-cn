---
title: 监视媒体服务数据参考
description: 监视媒体服务时所需的重要参考资料
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 8908fd1acc64fb180121ac0b6a4dc38ce5a02572
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121160"
---
# <a name="monitoring-media-services-data-reference"></a>监视媒体服务数据参考

本文介绍对监视媒体服务有用的数据。 有关 Azure Monitor 支持的所有平台指标的详细信息，请查看 [Azure Monitor 支持的指标](../../../azure-monitor/essentials/metrics-supported.md)。

## <a name="media-services-metrics"></a>媒体服务指标

不管值是否变化，指标都按固定的时间间隔进行收集。 指标可用于警报，因为它们可以频繁采样，而警报则可以使用相对简单的逻辑快速触发。

媒体服务支持监视以下资源的指标：

* 帐户
* 流式处理终结点

### <a name="account"></a>帐户

可监视以下帐户指标。

|指标名称|Display name|说明|
|---|---|---|
|AssetCount|资产计数|帐户中的资产。|
|AssetQuota|资产配额|帐户中的资产配额。|
|AssetQuotaUsedPercentage|资产配额已用百分比|已使用的资产配额百分比。|
|ContentKeyPolicyCount|内容密钥策略计数|帐户中的内容密钥策略。|
|ContentKeyPolicyQuota|内容密钥策略配额|帐户中的内容密钥策略配额。|
|ContentKeyPolicyQuotaUsedPercentage|内容密钥策略配额已用百分比|已使用的内容密钥策略配额百分比。|
|StreamingPolicyCount|流式处理策略计数|帐户中的流式处理策略。|
|StreamingPolicyQuota|流式处理策略配额|帐户中的流式处理策略配额。|
|StreamingPolicyQuotaUsedPercentage|流式处理策略配额已用百分比|已使用的流式处理策略配额百分比。|

还应查看[帐户配额和限制](../limits-quotas-constraints-reference.md)。

### <a name="streaming-endpoint"></a>流式处理终结点

支持以下媒体服务[流式处理终结点](/rest/api/media/streamingendpoints)指标：

|指标名称|Display name|说明|
|---|---|---|
|请求|请求|提供由流式处理终结点提供服务的 HTTP 请求总数。|
|流出量|流出量|每个流式处理终结点每分钟流出的字节总数。|
|SuccessE2ELatency|成功端到端延迟|从流式处理终结点收到请求，到发送最后一个响应字节的持续时间。|
|CPU 使用率| | 高级流式处理终结点的 CPU 使用率。 此数据不可用于标准流式处理终结点。 |
|出口带宽 | | 出口带宽（位/秒）。|

## <a name="metric-dimensions"></a>指标维度

有关指标维度定义的详细信息，请参阅[多维指标](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。

<!--**PLACEHOLDER** for dimensions table.-->

## <a name="resource-logs"></a>资源日志

## <a name="media-services-diagnostic-logs"></a>媒体服务诊断日志

诊断日志提供有关 Azure 资源操作的丰富、频繁的数据。 有关详细信息，请参阅[如何从 Azure 资源收集和使用日志数据](../../../azure-monitor/essentials/platform-logs-overview.md)。

媒体服务支持以下诊断日志：

* 密钥传送

### <a name="key-delivery"></a>密钥传送

|名称|说明|
|---|---|
|密钥传送服务请求|显示密钥传送服务请求信息的日志。 有关详细信息，请参阅[架构](monitor-media-services-data-reference.md)。|

## <a name="schemas"></a>架构

有关顶级诊断日志架构的详细说明，请参阅 [Azure 诊断日志支持的服务、架构和类别](../../../azure-monitor/essentials/resource-logs-schema.md)。

## <a name="key-delivery-log-schema-properties"></a>密钥传递日志架构属性

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
