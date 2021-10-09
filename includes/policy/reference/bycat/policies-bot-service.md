---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 84e64159a53f8df5a311c2e8a3cc930f1e17cbf7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654676"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[机器人服务终结点应是有效的 HTTPS URI](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |传输过程中数据可能会被篡改。 存在一些协议提供加密，可解决滥用和篡改问题。 若要确保机器人仅通过加密通道进行通信，请将终结点设置为有效的 HTTPS URI。 这样可确保使用 HTTPS 协议对传输中的数据进行加密，并且这通常也是符合法规或行业标准的要求。 请访问：[https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](/azure/bot-service/bot-builder-security-guidelines)。 |审核、拒绝、已禁用 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[机器人服务应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure 机器人服务自动加密你的资源，以保护数据并实现组织安全性和合规性承诺。 默认使用 Microsoft 管理的加密密钥。 为了能够更灵活地管理密钥或控制对订阅的访问，请选择“客户管理的密钥”（亦称为“创建自己的密钥 (BYOK)”）。 详细了解 Azure 机器人服务加密：[https://docs.microsoft.com/azure/bot-service/bot-service-encryption](/azure/bot-service/bot-service-encryption)。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
|[机器人服务应启用隔离模式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52152f42-0dda-40d9-976e-abb1acdd611e) |机器人应设置为“仅隔离”模式。 此设置会配置需要禁用公共 Internet 流量的机器人服务通道。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_NetworkIsolatedEnabled_Audit.json) |
|[机器人服务应已禁用本地身份验证方法](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fffea632e-4e3a-4424-bf78-10e179bb2e1a) |禁用本地身份验证方法可确保机器人仅将 AAD 专用于身份验证，从而提高安全性。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_DisableLocalAuth_Audit.json) |
|[BotService 资源应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad5621d6-a877-4407-aa93-a950b428315e) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 BotService 资源，可以降低数据泄露风险。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_PrivateEndpoint_Audit.json) |
|[将 BotService 资源配置为使用专用 DNS 区域](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a4e6f44-f2af-4082-9702-033c9e88b9f8) |使用专用 DNS 区域来替代专用终结点的 DNS 解析。 专用 DNS 区域会链接到虚拟网络，以解析到 BotService 相关资源。 有关详细信息，请访问：[https://aka.ms/privatednszone](../../../../articles/private-link/private-endpoint-dns.md)。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_PrivateDNSZone_DeployIfNotExists.json) |
|[使用专用终结点配置 BotService 资源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F29261f8e-efdb-4255-95b8-8215414515d6) |专用终结点可在源或目标位置没有公共 IP 地址的情况下将虚拟网络连接到 Azure 服务。 将专用终结点映射到 BotService 资源，可以降低数据泄露风险。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_PrivateEndpoint_DeployIfNotExists.json) |