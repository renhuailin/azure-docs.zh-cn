---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0d09b938f820a1f1754c80c1881f0235652bfce1
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605280"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[将 Azure Migrate 资源配置为使用专用 DNS 区域](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7590a335-57cf-4c95-babd-ecbc8fafeb1f) |使用专用 DNS 区域来替代专用终结点的 DNS 解析。 专用 DNS 区域会链接到虚拟网络，以解析到 Azure Migrate 项目。 有关详细信息，请访问：[https://aka.ms/privatednszone](../../../../articles/private-link/private-endpoint-dns.md)。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Migrate/Migrate_PrivateDNSZone_DeployIfNotExists.json) |