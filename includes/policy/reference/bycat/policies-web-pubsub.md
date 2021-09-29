---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fca687bae89cd1db7a7420abf5454e6cba2e91cf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654745"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Web PubSub 服务应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf45113f-264e-4a87-88f9-29ac8a0aca6a) |禁用公用网络访问可确保 Azure Web PubSub 服务不会在公共 Internet 上公开，从而提高安全性。 创建专用终结点可以限制 Azure Web PubSub 服务的公开。 有关详细信息，请访问：[https://aka.ms/awps/networkacls](https://aka.ms/awps/networkacls)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PublicNetworkAccessDisabled_AuditDeny.json) |
|[Azure Web PubSub 服务应使用支持专用链接的 SKU](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82909236-25f3-46a6-841c-fe1020f95ae1) |使用受支持的 SKU 时，Azure 专用链接可让你在源或目标位置没有公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure Web PubSub 服务，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_AllowedSKU_AuditDeny.json) |
|[Azure Web PubSub 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 Azure Web PubSub 服务可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[配置 Azure Web PubSub 服务以禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b1213e4-06e4-4ccc-81de-4201f2f7131a) |禁用对 Azure Web PubSub 资源的公用网络访问，确保无法通过公共 Internet 对其进行访问。 这样可以减少数据泄露风险。 有关详细信息，请访问：[https://aka.ms/awps/networkacls](https://aka.ms/awps/networkacls)。  |修改，已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PublicNetworkAccessDisabled_Modify.json) |
|[将 Azure Web PubSub 服务配置为使用专用 DNS 区域](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b026355-49cb-467b-8ac4-f777874e175a) |使用专用 DNS 区域来替代专用终结点的 DNS 解析。 专用 DNS 区域会链接到虚拟网络，以解析到 Azure Web PubSub 服务。 有关详细信息，请访问：[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointDNSZone_DeployIfNotExists.json) |
|[使用专用终结点配置 Azure Web PubSub 服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b9c0b58-fc7b-42c8-8010-cdfa1d1b8544) |在没有公共 IP 地址的情况下，专用终结点可在源或目标位置将虚拟网络连接到 Azure 服务。 通过将专用终结点映射到 Azure Web PubSub 服务，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)。  |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_DeployIfNotExists.json) |
