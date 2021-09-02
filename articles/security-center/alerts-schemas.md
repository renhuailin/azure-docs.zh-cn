---
title: Azure 安全中心警报的架构
description: 本文介绍了 Azure 安全中心用于安全警报的不同架构。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2021
ms.author: memildin
ms.openlocfilehash: e65fd5a0c672e500a0a4bc08f45e9ced32d89047
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463497"
---
# <a name="security-alerts-schemas"></a>安全警报架构

如果订阅已启用 Azure Defender，则当安全中心检测到资源受到威胁时，你将收到安全警报。

可以在 Azure 安全中心的“威胁防护”页中或通过以下外部工具查看这些安全警报：

- [Azure Sentinel](../sentinel/index.yml) - Microsoft 的云原生 SIEM。 Sentinel 连接器从 Azure 安全中心获取警报，并将它们发送到 Azure Sentinel 的 [Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。
- 第三方 SIEM - 向 [Azure 事件中心](../event-hubs/index.yml)发送数据。 然后，将事件中心数据与第三方 SIEM 集成。 有关详细信息，请参阅[将警报流式传输到 SIEM、SOAR 或 IT 服务管理解决方案](export-to-siem.md)。
- [REST API](/rest/api/securitycenter/) - 如果你使用 REST API 访问警报，请参阅[在线警报 API 文档](/rest/api/securitycenter/alerts)。

如果你通过任何编程方法来使用警报，将需要正确的架构来查找与你相关的字段。 此外，如果你要导出到事件中心或尝试使用泛型 HTTP 连接器来触发工作流自动化，请使用架构来正确分析 JSON 对象。

>[!IMPORTANT]
> 对于每种场景，架构都略有不同，因此请确保选择下面的相关选项卡。


## <a name="the-schemas"></a>架构 


### <a name="azure-sentinel"></a>[Azure Sentinel](#tab/schema-sentinel)

Sentinel 连接器从 Azure 安全中心获取警报，并将它们发送到 Azure Sentinel 的 Log Analytics 工作区。

若要创建使用安全中心警报的 Sentinel 案例或事件，你需要以下所示警报的架构。 

有关 Azure Sentinel 的详细信息，请参阅[文档](../sentinel/index.yml)。

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]


### <a name="azure-activity-log"></a>[Azure 活动日志](#tab/schema-activitylog)

Azure 安全中心将 Azure 活动日志中生成的安全警报作为事件进行审核。

可以通过按如下所示方式搜索“激活警报”事件来查看活动日志中的安全警报事件：

[![在活动日志中搜索“激活警报”事件。](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>发送到 Azure 活动日志的警报的示例 JSON

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>架构的数据模型

|字段|描述|
|----|----|
|**channels**|常量“Operation”|
|**correlationId**|Azure 安全中心警报 ID|
|description|警报的描述|
|**eventDataId**|请参阅 correlationId|
|**eventName**|值和 localizedValue 子字段包含警报显示名称|
|**category**|值和 localizedValue 子字段是常量“Security”|
|**eventTimestamp**|生成警报时的 UTC 时间戳|
|**id**|完全限定的警报 ID|
|**level**|常量“Informational”|
|**operationId**|请参阅 correlationId|
|**operationName**|值字段是常量“Microsoft.Security/locations/alerts/activate/action”，本地化值将是“激活警报”（可以根据用户区域设置进行本地化）|
|**resourceGroupName**|将包含资源组名称|
|**resourceProviderName**|值和 localizedValue 子字段是常量“Microsoft.Security”|
|**resourceType**|值和 localizedValue 子字段是常量“Microsoft.Security/locations/alerts”|
|**resourceId**|完全限定的 Azure 资源 ID|
|**status**|值和 localizedValue 子字段是常量“Active”|
|**subStatus**|值和 localizedValue 子字段为空|
|**submissionTimestamp**|事件提交到活动日志的 UTC 时间戳|
|**subscriptionId**|已盗用的资源的订阅 ID|
|**properties**|与警报相关的其他属性的 JSON 包。 这些项在警报中各不相同，但是以下字段将出现在所有警报中：<br>- 严重性：攻击的严重性<br>- compromisedEntity：被盗用的资源的名称<br>- remediationSteps：要执行的一系列修正步骤<br>- 意向：警报的终止链意向。 [意向表](alerts-reference.md#intentions)中记录了可能的意向|
|**relatedEvents**|常量 - 空数组|
|||


### <a name="workflow-automation"></a>[工作流自动化](#tab/schema-workflow-automation)

有关使用工作流自动化时警报架构的信息，请参阅[连接器文档](/connectors/ascalert/)。


### <a name="continuous-export"></a>[连续导出](#tab/schema-continuousexport)

安全中心的连续导出功能将警报数据传递给：

- Azure 事件中心使用与[警报 API](/rest/api/securitycenter/alerts) 相同的架构。
- Log Analytics 工作区，根据 Azure Monitor 数据参考文档中的 [SecurityAlert 架构](/azure/azure-monitor/reference/tables/SecurityAlert)。




### <a name="ms-graph-api"></a>[MS 图形 API](#tab/schema-graphapi)

Microsoft Graph 是 Microsoft 365 中数据和智能的网关。 它提供了一个统一的可编程性模型，你可以使用该模型访问 Microsoft 365、Windows 10 和企业移动性 + 安全性中的大量数据。 使用 Microsoft Graph 中丰富的数据为与数百万用户交互的组织和使用者构建应用。

[Microsoft Graph 文档](/graph/api/resources/alert)中提供了发送到 MS Graph 的安全警报的架构和 JSON 表示形式。

---


## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 安全中心的威胁防护工具在发送安全警报信息时使用的架构。

有关从外部安全中心访问安全警报的方法的详细信息，请参阅以下页面：

- [Azure Sentinel](../sentinel/index.yml) - Microsoft 的云原生 SIEM
- [Azure 事件中心](../event-hubs/index.yml) - Microsoft 的完全托管的实时数据引入服务
- [连续导出安全中心数据](continuous-export.md)
- [Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md) - Azure Monitor 将日志数据存储在 Log Analytics 工作区中，该工作区是一个包含数据和配置信息的容器