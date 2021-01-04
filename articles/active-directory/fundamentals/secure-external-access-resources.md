---
title: 在 Azure Active Directory 中保护资源的外部访问
description: 有关保护内部资源的外部访问的架构师和 IT 管理员指南
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
ms.openlocfilehash: 39300c887245a99dce2913661f6492c85a1e6722
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743865"
---
# <a name="securing-external-access-to-resources"></a>保护对资源的外部访问

与外部合作伙伴的安全协作可确保适当的外部合作伙伴可以在适当的时间段内访问内部资源。 通过全面的管理方法，你可以降低安全风险，满足符合性目标，并确保你知道谁有权访问。

Ungoverned 协作导致缺乏对访问权限的明晰度，以及敏感资源的可能暴露。 迁移到安全和受管辖的协作可以确保有明确的所有权和责任来保证外部用户的访问权限。 这包括：

* 管理可以访问资源的外部组织和其中的用户。

* 确保访问适当、经过评审和时间限制。

* 使业务所有者能够管理 IT 创建的防护导轨中的协作。

如果必须满足符合性框架的要求，则可以通过管控协作证明适当性的访问权限。

Microsoft 提供了全面的工具套件来实现安全的外部访问。  Azure Active Directory (Azure AD) B2B 协作在任何外部协作计划的中心。 Azure AD B2B 可以与 Azure AD 中的其他工具和 Microsoft 365 services 中的工具集成，以帮助保护和管理外部访问。

此文档集旨在使你能够从即席或松散的外部协作迁移到更安全的状态。 

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出的顺序执行这些操作。


1. [确定外部访问所需的安全状况](1-secure-access-posture.md)

2. [发现当前状态](2-secure-access-current-state.md)

3. [创建调控计划](3-secure-access-plan.md)

4. [使用组进行安全保护](4-secure-access-groups.md)

5. [过渡到 Azure AD B2B](5-secure-access-b2b.md)

6. [使用权限管理的安全访问](6-secure-access-entitlement-managment.md)

7. [使用条件性访问策略的安全访问](7-secure-access-conditional-access.md)

8. [使用敏感度标签进行安全访问](8-secure-access-sensitivity-labels.md)

9. [安全访问 Microsoft 团队、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)
