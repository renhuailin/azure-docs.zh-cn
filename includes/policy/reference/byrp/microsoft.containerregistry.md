---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 07d603372da99a68502c32d44c02ffe37fe50719
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805339"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[容器注册表应使用客户托管密钥 (CMK) 进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |使用客户管理的密钥来管理注册表内容的静态加密。 默认情况下，使用服务管理的密钥对数据进行静态加密，但为了满足监管合规标准，通常需要使用客户管理的密钥 (CMK)。 CMK 允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 访问 [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK)，了解有关 CMK 加密的详细信息。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[容器注册表不得允许无限制的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |默认情况下，Azure 容器注册表接受来自任何网络上的主机的 Internet 连接。 为了防止注册表受到潜在的威胁，只允许来自特定的公共 IP 地址或地址范围的访问。 如果注册表没有 IP/防火墙规则或配置的虚拟网络，它将出现在不正常资源中。 有关容器注册表网络规则的详细信息，请访问 [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) 和 [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[容器注册表应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。通过将专用终结点映射到容器注册表，而不是整个服务，还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/acr/private-link](https://aka.ms/acr/private-link)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[容器注册表应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此策略审核任何未配置为使用虚拟网络服务终结点的容器注册表。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[应修正 Azure 容器注册表映像中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0f936f-2f01-4bf5-b6be-d423792fa562) |容器映像漏洞评估功能会扫描注册表中每个推送的容器映像上的安全漏洞，并显示每个映像的详细发现结果（由 Qualys 支持）。 修复这些漏洞可以极大改善容器的安全状况，并保护其不受攻击影响。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerRegistryVulnerabilityAssessment_Audit.json) |
