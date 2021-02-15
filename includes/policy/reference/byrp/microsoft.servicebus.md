---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5ea5b6fabaaed9d2c23b6cdd7ba094029ff98a31
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098294"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应从服务总线命名空间中删除 RootManageSharedAccessKey 以外的所有授权规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |服务总线客户端不应使用提供对命名空间中所有队列和主题的访问的命名空间级访问策略。 为了与最低权限安全模型保持一致，应在实体级别为队列和主题创建访问策略，以便仅提供对特定实体的访问权限 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[将服务总线的诊断设置部署到事件中心](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b51af03-9277-49a9-a3f8-1c69c9ff7403) |在创建或更新缺少服务总线的诊断设置的任何服务总线时，将此诊断设置流式部署到区域事件中心。 |DeployIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ServiceBus_DeployDiagnosticLog_Deploy_EventHub.json) |
|[将服务总线的诊断设置部署到 Log Analytics 工作区](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04d53d87-841c-4f23-8a5b-21564380b55e) |在创建或更新缺少服务总线的诊断设置的任何服务总线时，将此诊断设置流式部署到区域 Log Analytics 工作区。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ServiceBus_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[应启用 Service Bus 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |审核资源日志的启用。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[服务总线高级命名空间应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |Azure 服务总线支持通过 Microsoft 管理的密钥（默认）或客户管理的密钥来加密静态数据。 如果选择使用客户管理的密钥加密数据，则可分配、轮换、禁用和撤销对服务总线将用于加密命名空间中的数据的密钥的访问权限。 请注意，服务总线仅支持使用客户管理的密钥对高级命名空间进行加密。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |
|[服务总线应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |此策略审核任何未配置为使用虚拟网络服务终结点的服务总线。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
