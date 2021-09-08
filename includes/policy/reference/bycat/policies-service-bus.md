---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b45af4159b9a872ff081c5d25bae1d6caf29f1ae
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123476797"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应从服务总线命名空间中删除 RootManageSharedAccessKey 以外的所有授权规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |服务总线客户端不应使用提供对命名空间中所有队列和主题的访问的命名空间级访问策略。 为了与最低权限安全模型保持一致，应在实体级别为队列和主题创建访问策略，以便仅提供对特定实体的访问权限 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到服务总线命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../../articles/service-bus-messaging/private-link-service.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[将服务总线命名空间配置为使用专用 DNS 区域](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0fcf93c-c063-4071-9668-c47474bd3564) |使用专用 DNS 区域来替代专用终结点的 DNS 解析。 专用 DNS 区域会链接到虚拟网络，以解析到服务总线命名空间。 有关详细信息，请访问：[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../../articles/service-bus-messaging/private-link-service.md)。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateDNSZone_DeployIfNotExists.json) |
|[为服务总线命名空间配置专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d890f7f-100c-473d-baa1-2777e2266535) |专用终结点可在源或目标位置没有公共 IP 地址的情况下将虚拟网络连接到 Azure 服务。 通过将专用终结点映射到服务总线命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../../articles/service-bus-messaging/private-link-service.md)。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_DeployIfNotExists.json) |
|[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[服务总线命名空间应启用双重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febaf4f25-a4e8-415f-86a8-42d9155bef0b) |启用双重加密有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 启用双重加密后，将使用两种不同的加密算法和两个不同的密钥对存储帐户中的数据进行两次加密，一次在服务级别，一次在基础结构级别。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_InfrastructureEncryptionEnabled_Audit.json) |
|[服务总线高级命名空间应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |Azure 服务总线支持通过 Microsoft 管理的密钥（默认）或客户管理的密钥来加密静态数据。 如果选择使用客户管理的密钥加密数据，则可分配、轮换、禁用和撤销对服务总线将用于加密命名空间中的数据的密钥的访问权限。 请注意，服务总线仅支持使用客户管理的密钥对高级命名空间进行加密。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |