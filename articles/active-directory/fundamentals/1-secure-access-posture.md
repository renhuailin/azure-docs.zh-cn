---
title: 确定与 Azure Active Directory 进行外部协作的安全状况
description: 必须先确定要实现的目标，然后才能执行外部访问安全性计划。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fb5d74f0c912db098b5e309793963d5f07fbd58
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730345"
---
# <a name="determine-your-security-posture-for-external-access"></a>确定外部访问的安全状况 

在考虑管理外部访问时，你将需要在每种场景中评估整个组织的安全和协作需求。 在组织级别，考虑你的 IT 团队在日常协作方面所需的控制量。 受管制行业的组织可能需要更多 IT 控制。 例如，防御承包商可能需要正确识别和记录每位外部用户、其访问权限以及访问权限的删除情况。 此要求可能适用于所有访问，也可能适用于特定场景工作负载。 在另一种完全相反的情况下，咨询公司通常可能要允许最终用户在特定 IT 防护范围内确定他们需要与之协作的外部用户。 

![IT 与最终用户对协作的控制](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> 对协作进行过于严格的控制会导致 IT 预算增加、生产效率降低和业务成果延迟。 如果认为正式协作渠道过于麻烦，最终用户往往会绕过 IT 提供的系统来完成他们的工作，例如通过电子邮件发送不安全的文档。

## <a name="think-in-terms-of-scenarios"></a>对各种场景进行考虑

在许多情况下，IT 可以委托合作伙伴访问，同时提供安全防护（至少在某些情况下是这样）。 IT 防护有助于确保知识产权处于安全状态，同时助力员工与合作伙伴协作来完成工作。

在考虑组织中的场景时，请评估员工与业务合作伙伴对资源的访问需求。 银行可能有一些合规性需求，需要将对某些资源（如用户帐户信息）的访问权限限制给一小部分内部员工。 相反，同一家银行可能为从事市场营销活动的合作伙伴启用委托访问。

![每种场景的连续管理](media\secure-external-access\1-scenarios.png)

在每种场景中，请考虑 

* 存在风险的信息的敏感性

* 是否需要限制可查看其他用户相关信息的合作伙伴

* 违反的成本与集中控制和最终用户摩擦的权重

 还可以在开始时采取集中管理的控制，以满足合规性目标，然后随时间进展将控制委托给最终用户。 所有访问管理模型可在一个组织中同时共存。 

使用[合作伙伴管理的凭据](../external-identities/what-is-b2b.md)可为组织提供非常重要的信号，当外部用户失去对其公司资源的访问权限后，该信号便可终止该用户访问你的资源。

## <a name="goals-of-securing-external-access"></a>保护外部访问的目标

IT 管理的访问目标不同于委托的访问目标。

**IT 管理的访问的主要目标是**：

* 满足管理、监管和合规性 (GRC) 目标。 

* 严格控制合作伙伴访问权限，以及合作伙伴可查看的有关成员用户、组和其他合作伙伴的信息。

**委托访问的主要目标是**：

* 使业务所有者能够在 IT 约束范围内管理他们与之协作的人员。

* 使业务合作伙伴能够根据业务所有者定义的规则请求访问权限。

无论你为组织和各种场景实现哪种目标，都需要做到以下几点： 

* 控制对应用程序、数据和内容的访问。 这可以通过多种方法来实现，具体取决于 [Azure AD](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing) 和 [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans) 的版本。 

* 缩小攻击面。 [特权标识管理](../privileged-identity-management/pim-configure.md)、[数据丢失防护 (DLP)](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) 和[加密功能](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention)可缩小攻击面。

* 定期查看活动和审核日志以确认合规性。 IT 可以通过权利管理将访问决策委托给业务所有者，同时访问评审提供了一种定期确认连续访问的方法。 带有敏感度标签的自动数据分类有助于自动执行敏感内容加密，从而使员工最终用户能够更轻松地符合要求。

## <a name="next-steps"></a>后续步骤 

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出顺序执行这些操作。

1. [确定外部访问的安全状况](1-secure-access-posture.md)（本文）

2. [了解当前状态](2-secure-access-current-state.md)

3. [创建治理计划](3-secure-access-plan.md)

4. [使用组以确保安全性](4-secure-access-groups.md)

5. [转换到 Azure AD B2B](5-secure-access-b2b.md)

6. [通过权利管理实现安全访问](6-secure-access-entitlement-managment.md)

7. [通过条件访问策略实现安全访问](7-secure-access-conditional-access.md)

8. [通过敏感度标签实现安全访问](8-secure-access-sensitivity-labels.md)

9. [实现对 Microsoft Teams、OneDrive 和 SharePoint 的安全访问](9-secure-access-teams-sharepoint.md)
 

