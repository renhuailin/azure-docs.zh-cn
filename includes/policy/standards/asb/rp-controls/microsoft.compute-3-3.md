---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 118b4b6a3028456193176d5efd21bf480f4908b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910680"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[审核缺少管理员组中任何指定成员的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../../../../../articles/governance/policy/concepts/guest-configuration.md)。 如果本地管理员组不包含策略参数中列出的一个或多个成员，则计算机不合规。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[审核管理员组中具有额外帐户的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../../../../../articles/governance/policy/concepts/guest-configuration.md)。 如果本地管理员组包含策略参数中未列出的成员，则计算机不合规。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[审核具有管理员组中指定成员的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../../../../../articles/governance/policy/concepts/guest-configuration.md)。 如果本地管理员组包含策略参数中列出的一个或多个成员，则计算机不合规。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |