---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0bbd833b840809828cb13cf807a8c150d42283d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107912458"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Active Directory 域服务托管域应使用仅限 TLS 1.2 模式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |为托管域使用仅限 TLS 1.2 模式。 默认情况下，Azure AD 域服务允许使用 NTLM v1 和 TLS v1 等密码。 某些旧版应用程序可能需要这些密码，但这些密码视为弱密码，如果不需要，可以将其禁用。 如果启用仅限 TLS 1.2 模式，那么任何发出请求但未使用 TLS 1.2 的客户端都将失败。 更多信息请访问 [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |
