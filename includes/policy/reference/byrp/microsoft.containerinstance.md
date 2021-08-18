---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/13/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cd6c96d23d924df3a1d4f0f0e285b14ea1a11344
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183419"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 容器实例容器组应部署到虚拟网络](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |容器与 Azure 虚拟网络之间进行安全通信。 指定虚拟网络时，虚拟网络中的资源可以彼此安全且私密地进行通信。 |审核、已禁用、拒绝 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[Azure 容器实例容器组应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |使用客户管理的密钥更灵活地保护容器。 指定客户托管密钥时，该密钥用于保护和控制对数据加密密钥的访问。 使用客户管理的密钥可提供附加功能来控制密钥加密密钥的轮换或以加密方式擦除数据。 |审核、已禁用、拒绝 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
