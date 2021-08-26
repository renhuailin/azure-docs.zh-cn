---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: acac6f337a9bdaa131deeaaa545ebcc71e52d2bd
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605409"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 数据资源管理器静态加密应使用客户管理的密钥](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |对 Azure 数据资源管理器群集使用客户管理的密钥启用静态加密，可提供针对静态加密所使用密钥的额外控制。 此功能通常适用于具有特殊合规性要求并且需要使用 Key Vault 管理密钥的客户。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[应为 Azure 数据资源管理器启用磁盘加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |启用磁盘加密有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[应为 Azure 数据资源管理器启用双重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |启用双重加密有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 启用双重加密后，将使用两种不同的加密算法和两个不同的密钥对存储帐户中的数据进行两次加密，一次在服务级别，一次在基础结构级别。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[应为 Azure 数据资源管理器启用虚拟网络注入](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |通过虚拟网络注入保护网络边界，借助该虚拟网络注入，可以强制实施网络安全组规则，在本地连接并使用服务终结点保护数据连接源。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
