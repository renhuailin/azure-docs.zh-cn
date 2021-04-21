---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 65a71e70e0bf48cb17dde58f48b66706238000f4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498577"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[将 Azure Migrate 资源配置为使用专用 DNS 区域](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7590a335-57cf-4c95-babd-ecbc8fafeb1f) |使用专用 DNS 区域来替代专用终结点的 DNS 解析。 专用 DNS 区域会链接到虚拟网络，以解析到 Azure Migrate 项目。 有关详细信息，请访问：[https://aka.ms/privatednszone](https://aka.ms/privatednszone)。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Migrate/Migrate_PrivateDNSZone_DeployIfNotExists.json) |
