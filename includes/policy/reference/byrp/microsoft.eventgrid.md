---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f03688012100c31865fb25087f7b4049c087b43e
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050896"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 事件网格域应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |使用 azure 专用链接，可以将虚拟网络连接到 Azure 服务，而无需在源或目标上使用公共 IP 地址。 专用链接平台通过 Azure 主干网络处理消费者与服务之间的连接。通过将专用终结点映射到事件网格域而不是整个服务，你还可以防范数据泄露风险。有关详细信息，请参阅： [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) 。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Azure 事件网格主题应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |使用 azure 专用链接，可以将虚拟网络连接到 Azure 服务，而无需在源或目标上使用公共 IP 地址。 专用链接平台通过 Azure 主干网络处理消费者与服务之间的连接。 通过将专用终结点映射到主题而不是整个服务，你还可以防范数据泄露风险。 有关详细信息，请参阅： [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) 。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
