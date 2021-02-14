---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8f09bc58c536dc7262405a79c1dedacf76b6ffa0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096243"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[自动化帐户变量应加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |存储敏感数据时，请务必启用自动化帐户变量资产加密 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Azure 自动化帐户应使用客户管理的密钥来加密静态数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56a5ee18-2ae6-4810-86f7-18e39ce5629b) |使用客户管理的密钥来管理 Azure 自动化帐户的静态加密。 默认情况下，使用服务托管密钥对客户数据进行加密，但客户管理的密钥通常需要满足法规遵从性标准。 使用客户管理的密钥，可以通过由你创建并拥有的 Azure Key Vault 密钥来加密数据。 你可以完全控制并负责关键生命周期，包括轮换和管理。 更多信息请访问 [https://aka.ms/automation-cmk](https://aka.ms/automation-cmk)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_CMK_Audit.json) |
