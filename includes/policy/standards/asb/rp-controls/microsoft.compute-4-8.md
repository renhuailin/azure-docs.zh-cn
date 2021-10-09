---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8d5ad743c425d921a4fa9abbf3948aa3b69b8390
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909466"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应当加密未附加的磁盘](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |此策略会审核未启用加密的所有未附加磁盘。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |
|[虚拟机应该加密计算和存储资源之间的临时磁盘、缓存和数据流](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |默认情况下，虚拟机的操作系统和数据磁盘使用平台管理的密钥进行静态加密。 临时磁盘、数据缓存以及在计算和存储之间流动的数据不加密。 如果存在以下情况，请忽略此建议：1. 使用主机加密，或 2. 托管磁盘上的服务器端加密满足安全要求。 若要了解详细信息，请参阅 [Azure 磁盘存储的服务器端加密](../../../../../articles/virtual-machines/disk-encryption.md)。 和[不同的磁盘加密产品/服务](../../../../../articles/virtual-machines/disk-encryption-overview.md#comparison)。 |AuditIfNotExists、Disabled |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |