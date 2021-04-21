---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8a6d26eb0fb795f0ca047ff4a976a5b31b9493ee
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513419"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[密钥保管库应启用清除保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |恶意删除密钥保管库可能会导致永久丢失数据。 你组织中的恶意内部人员可能会删除和清除密钥保管库。 清除保护通过强制实施软删除密钥保管库的强制保留期来保护你免受内部攻击。 你的组织内的任何人都无法在软删除保留期内清除你的密钥保管库。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
