---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4c32e51a54905a819eebc5e6686f3d73b8365e77
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985004"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SignalR 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |审核没有达到“至少有一个已批准的专用终结点连接”标准的 Azure SignalR 服务资源。 虚拟网络中的客户端可以安全地访问通过专用链接获得专用终结点连接的资源。 有关详细信息，请访问：[https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
