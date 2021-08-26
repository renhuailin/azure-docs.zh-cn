---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e1b794bd2ad64337728789c12fc3d4bddd678a7d
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122636626"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用网络观察程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 需要在存在虚拟网络的每个区域中创建一个网络观察程序资源组。 如果网络观察程序资源组在特定区域中不可用，则会启用警报。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
