---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fa8f0afe4467f53f37daadf6cf881114d03ebaf4
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090559"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[认知服务帐户应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |禁用公用网络访问可确保认知服务帐户不会在公共 Internet 上公开，从而提高安全性。 创建专用终结点可以限制认知服务帐户的公开。 有关详细信息，请访问：[https://go.microsoft.com/fwlink/?linkid=2129800](https://go.microsoft.com/fwlink/?linkid=2129800)。  |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
|[认知服务帐户应启用数据加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2bdd0062-9d75-436e-89df-487dd8e4b3c7) |此策略审核未使用数据加密的任何认知服务帐户。 对于具有存储的各个认知服务帐户，应启用使用客户托管密钥或 Microsoft 管理密钥的数据加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_Encryption_Audit.json) |
|[认知服务帐户应启用使用客户管理的密钥进行数据加密的功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67121cc7-ff39-4ab8-b7e3-95b84dab487d) |为了满足法规符合性标准，通常需要使用客户管理的密钥。 利用客户管理的密钥，可以使用由你创建并拥有的 Azure Key Vault 密钥对存储在认知服务中的数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 通过 [https://go.microsoft.com/fwlink/?linkid=2121321](https://go.microsoft.com/fwlink/?linkid=2121321) 详细了解客户管理的密钥。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_CustomerManagedKey_Audit.json) |
|[认知服务帐户应限制网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |应限制对认知服务帐户的网络访问。 配置网络规则，使只有来自允许的网络的应用程序才能访问认知服务帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络或到公共 Internet IP 地址范围的流量授予访问权限。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[认知服务帐户应使用托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe3fd216-4f83-4fc1-8984-2bbec80a3418) |向认知服务帐户分配托管标识有助于确保身份验证安全。 此认知服务帐户使用该标识以安全方式与其他 Azure 服务（如 Azure Key Vault）进行通信，你无需管理任何凭据。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_ManagedIdentity_Audit.json) |
|[认知服务帐户应使用客户自有存储](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F46aa9b05-0e60-4eae-a88b-1e9d374fa515) |使用客户拥有的存储来控制认知服务中静态存储的数据。 若要了解有关客户拥有的存储详细信息，请访问 [https://aka.ms/cogsvc-cmk](https://aka.ms/cogsvc-cmk)。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_UserOwnedStorage_Audit.json) |
|[认知服务帐户应使用客户自有存储或启用数据加密。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11566b39-f7f7-4b82-ab06-68d8700eb0a4) |此策略审核未使用客户自有存储或数据加密的任何认知服务帐户。 对于具有存储的各个认知服务帐户，应使用客户自有存储或启用数据加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_BYOX_Audit.json) |
|[将认知服务帐户配置为禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47ba1dd7-28d9-4b07-a8d5-9813bed64e0c) |禁用对认知服务资源的公用网络访问，确保无法通过公共 Internet 对其进行访问。 这样可以减少数据泄露风险。 有关详细信息，请访问：[https://go.microsoft.com/fwlink/?linkid=2129800](https://go.microsoft.com/fwlink/?linkid=2129800)。 |禁用、修改 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Modify.json) |
