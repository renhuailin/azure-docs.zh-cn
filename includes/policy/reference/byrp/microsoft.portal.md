---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c3c9e698aeddbe4818e7b77596eed81e60cb38e5
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "95002136"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[共享仪表板不应具有内联内容的 markdown 磁贴](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |禁止创建在 markdown 磁贴中包含内联内容的共享仪表板，并强制内容应存储为在线托管的 markdown 文件。 如果在 markdown 磁贴中使用内联内容，则无法管理内容加密。 通过配置自己的存储，可以加密、双重加密甚至自带密钥。 如果启用此策略，则会限制用户使用 2020-09-01-preview 或更高版本的共享仪表板 REST API。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
