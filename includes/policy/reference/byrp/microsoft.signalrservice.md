---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 53d3aa63314be0d16eac0166f923d9c672447237
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99561799"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SignalR 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 SignalR 资源（而不是整个服务），还可以防范数据泄露风险。有关详细信息，请访问：[https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
