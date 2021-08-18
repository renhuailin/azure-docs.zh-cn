---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/13/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7d1566c40a06746970667f748c0a7f488c533501
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178574"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[自动化帐户变量应加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |存储敏感数据时，请务必启用自动化帐户变量资产加密 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[自动化帐户应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F955a914f-bf86-4f0e-acd5-e0766b0efcb6) |禁用公用网络访问可确保资源不会在公共 Internet 上公开，从而提高了安全性。 可以改为通过创建专用终结点来限制自动化帐户资源的公开。 有关详细信息，请访问：[https://docs.microsoft.com/azure/automation/how-to/private-link-security](../../../../articles/automation/how-to/private-link-security.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_PublicNetworkAccess_Audit.json) |
|[Azure 自动化帐户应使用客户管理的密钥来加密静态数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56a5ee18-2ae6-4810-86f7-18e39ce5629b) |使用客户管理的密钥来管理 Azure 自动化帐户的静态加密。 默认情况下，使用服务管理的密钥对客户数据进行加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 更多信息请访问 [https://aka.ms/automation-cmk](../../../../articles/automation/automation-secure-asset-encryption.md#:~:text=Secure assets in Azure Automation include credentials, certificates, connections,,Using Microsoft-managed keys)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_CMK_Audit.json) |
|[将 Azure 自动化帐户配置为禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F23b36a7c-9d26-4288-a8fd-c1d2fa284d8c) |禁用对 Azure 自动化帐户的公用网络访问，确保无法通过公共 Internet 访问该帐户。 此配置有助于这些帐户防范数据泄露风险。 可以改为通过创建专用终结点来限制自动化帐户资源的公开。 有关详细信息，请访问：[https://aka.ms/privateendpoints](../../../../articles/event-grid/configure-private-endpoints.md)。 |修改，已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_PublicNetworkAccess_Modify.json) |
|[在 Azure 自动化帐户上配置专用终结点连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0c3130e-7dda-4187-aed0-ee4a472eaa60) |利用专用终结点连接，无需源或目标的公共 IP 地址，即可与 Azure 自动化帐户建立专用连接，从而实现安全通信。 若要详细了解 Azure 自动化中的专用终结点，请访问 [https://docs.microsoft.com/azure/automation/how-to/private-link-security](../../../../articles/automation/how-to/private-link-security.md)。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_PrivateEndpoints_DeployIfNotExist.json) |
|[应启用自动化帐户上的专用终结点连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c2b3618-68a8-4034-a150-ff4abc873462) |利用专用终结点连接，无需源或目标的公共 IP 地址，即可与自动化帐户建立专用连接，从而实现安全通信。 若要详细了解 Azure 自动化中的专用终结点，请访问 [https://docs.microsoft.com/azure/automation/how-to/private-link-security](../../../../articles/automation/how-to/private-link-security.md) |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_PrivateEndpoint_AuditIfNotExist.json) |