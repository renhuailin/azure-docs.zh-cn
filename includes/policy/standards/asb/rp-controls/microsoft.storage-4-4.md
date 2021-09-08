---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ac4b82dead75a52c858c8876ab6b30e3649aa905
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123484750"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用安全传输到存储帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |审核存储帐户中安全传输的要求。 安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 使用 HTTPS 可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听和会话劫持等网络层攻击 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
