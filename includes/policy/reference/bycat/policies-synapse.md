---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2b4acda582188d4057f19d4065e5662873436365
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429126"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Synapse 工作区应该只允许传送到已批准目标的出站数据流量](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3484ce98-c0c5-4c83-994b-c5ac24785218) |通过只允许传送到已批准目标的出站数据流量来提升 Synapse 工作区的安全性。 此做法会在发送数据之前验证目标，有助于防止数据外泄。 |审核、已禁用、拒绝 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_RestrictOutboundDataTraffic_Audit.json) |
|[Azure Synapse 工作区应使用客户管理的密钥来加密静态数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |使用客户管理的密钥来控制对 Azure Synapse 工作区中存储的数据的静态加密。 客户管理的密钥提供双重加密，方法是在使用服务托管密钥完成的默认加密层上添加另一层加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[应删除 Azure Synapse 工作区上的 IP 防火墙规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |删除所有 IP 防火墙规则可确保只能从专用终结点访问 Azure Synapse 工作区，从而提高安全性。 此配置会对创建允许公用网络访问工作区的防火墙规则进行审核。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[应启用 Azure Synapse 工作区上的托管工作区虚拟网络](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |启用托管工作区虚拟网络可确保你的工作区网络与其他工作区隔离。 在此虚拟网络中部署的数据集成和 Spark 资源还为 Spark 活动提供了用户级隔离。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[应启用 Azure Synapse 工作区上的专用终结点连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |可以将专用终结点配置为以私密方式连接到 Azure Synapse 工作区。 这用于实施到 Azure Synapse 工作区的安全信道。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Synapse 托管专用终结点应仅连接到已批准的 Azure Active Directory 租户中的资源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |仅允许连接到已批准的 Azure Active Directory (Azure AD) 租户中的资源，以保护 Synapse 工作区。 可以在策略分配过程中定义已批准的 Azure AD 租户。 |审核、已禁用、拒绝 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |
|[应对 Synapse 工作区启用漏洞评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0049a6b3-a662-4f3e-8635-39cf44ace45a) |通过在 Synapse 工作区上配置定期 SQL 漏洞评估扫描来发现、跟踪和修复潜在的漏洞。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/ASC_SQLVulnerabilityAssessmentOnSynapse_Audit.json) |
