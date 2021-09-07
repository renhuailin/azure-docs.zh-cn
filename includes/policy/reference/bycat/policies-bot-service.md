---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a79aca306c86432f466071ce7042ec2f01dc3bfe
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102996"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[机器人服务终结点应是有效的 HTTPS URI](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |传输过程中数据可能会被篡改。 存在一些协议提供加密，可解决滥用和篡改问题。 若要确保机器人仅通过加密通道进行通信，请将终结点设置为有效的 HTTPS URI。 这样可确保使用 HTTPS 协议对传输中的数据进行加密，并且这通常也是符合法规或行业标准的要求。 请访问：[https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](/azure/bot-service/bot-builder-security-guidelines)。 |审核、拒绝、已禁用 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[机器人服务应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure 机器人服务自动加密你的资源，以保护数据并实现组织安全性和合规性承诺。 默认使用 Microsoft 管理的加密密钥。 为了能够更灵活地管理密钥或控制对订阅的访问，请选择“客户管理的密钥”（亦称为“创建自己的密钥 (BYOK)”）。 详细了解 Azure 机器人服务加密：[https://docs.microsoft.com/azure/bot-service/bot-service-encryption](/azure/bot-service/bot-service-encryption)。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
|[机器人服务应启用隔离模式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52152f42-0dda-40d9-976e-abb1acdd611e) |机器人应设置为“仅隔离”模式。 此设置会配置需要禁用公共 Internet 流量的机器人服务通道。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_NetworkIsolatedEnabled_Audit.json) |
|[机器人服务应已禁用本地身份验证方法](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fffea632e-4e3a-4424-bf78-10e179bb2e1a) |禁用本地身份验证方法可确保机器人仅将 AAD 专用于身份验证，从而提高安全性。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_DisableLocalAuth_Audit.json) |