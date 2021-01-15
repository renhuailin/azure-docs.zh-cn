---
title: 确定与 Azure Active Directory 的外部协作的安全状况
description: 在可以执行外部访问安全计划之前，必须确定要实现的目标。
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
ms.openlocfilehash: e6814cafcf6dafa6f007bdd9d3623d30ef079084
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222439"
---
# <a name="determine-your-security-posture-for-external-access"></a>确定外部访问的安全状况 

当你考虑管理外部访问权限时，你将需要在每个方案中评估你的组织的安全和协作需求。 在组织级别，请考虑你的 IT 团队在日常协作上所需的控制量。 管控行业中的组织可能需要更多 IT 控制。 例如，防御承包商可能需要识别和记录每个外部用户、访问权限以及删除访问权限。 此要求可能适用于所有访问，或者针对特定方案或工作负荷。 在另一种极端情况下，咨询公司通常可以允许最终用户在特定 IT 防护导轨内确定他们需要与之协作的外部用户。 

![它与最终用户对协作的控制](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> 过度控制协作可能会导致更高的 IT 预算，降低生产力并降低业务成果。 当正式的协作渠道被认为过于繁重时，最终用户往往会绕过它来完成其工作，例如通过电子邮件发送不安全的文档。

## <a name="think-in-terms-of-scenarios"></a>考虑方案

在许多情况下，它可以至少在某些情况下委派合作伙伴访问权限，同时提供防护导轨来保证安全性。 IT 防护导轨有助于确保知识产权保持安全，同时可让员工与合作伙伴协作完成工作。

当你考虑你的组织中的方案时，请评估对员工和业务合作伙伴访问资源的需求。 银行可能需要将对某些资源（如用户帐户信息）的访问权限限制为一小组内部员工。 相反，同一银行可能会为正在处理市场营销活动的合作伙伴启用委托访问。

![每个方案的治理](media\secure-external-access\1-scenarios.png)

在每个方案中，请考虑 

* 风险的敏感信息

* 是否需要限制合作伙伴可查看的有关其他用户的信息

* 入侵成本与集中控制和最终用户摩擦的权重

 你还可以从集中管理的控件开始，以满足符合性目标，并随着时间的推移向最终用户委托控制权。 所有访问管理模型都可以在组织中同时共存。 

当外部用户失去了对自己公司的资源的访问权限时，使用 [合作伙伴管理的凭据](../external-identities/what-is-b2b.md) 可为你的组织提供基本信号，以终止对资源的访问。

## <a name="goals-of-securing-external-access"></a>保护外部访问的目标

IT 控制和委托访问的目标不同。

**IT 控制访问的主要目标是：**

* 满足监管、法规和合规性 (GRC) 目标。 

* 严格控制合作伙伴的访问权限，以及合作伙伴可以看到的关于成员用户、组和其他合作伙伴的信息。

**委托访问的主要目标是：**

* 使业务所有者能够在 IT 限制范围内管理他们与之协作的人员。

* 允许业务合作伙伴根据业务所有者定义的规则请求访问权限。

无论你对组织和方案执行哪种操作，你都需要： 

* **控制对应用程序、数据和内容的访问**。 这可以通过多种方法实现，具体取决于您的 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) 和 [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)的版本。 

* **减小攻击面**。 [特权标识管理](../privileged-identity-management/pim-configure.md)、 [数据丢失防护 (DLP) ](https://docs.microsoft.com/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) 和 [加密功能](https://docs.microsoft.com/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) 降低了攻击面。

* **定期查看活动和审核日志以确认符合性**。 它可以通过授权管理将访问决策委派给企业所有者，而访问评审则提供了一种定期确认持续访问的方法。 具有敏感性标签的自动数据分类有助于自动加密敏感内容，使员工最终用户能够更轻松地遵守这些内容。

## <a name="next-steps"></a>后续步骤 

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出的顺序执行这些操作。

1. [确定你所在 (的外部访问安全状况](1-secure-access-posture.md) 。 ) 

2. [发现当前状态](2-secure-access-current-state.md)

3. [创建调控计划](3-secure-access-plan.md)

4. [使用组进行安全保护](4-secure-access-groups.md)

5. [过渡到 Azure AD B2B](5-secure-access-b2b.md)

6. [使用权限管理的安全访问](6-secure-access-entitlement-managment.md)

7. [使用条件性访问策略的安全访问](7-secure-access-conditional-access.md)

8. [使用敏感度标签进行安全访问](8-secure-access-sensitivity-labels.md)

9. [安全访问 Microsoft 团队、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)
 

 
