---
title: 保护 Azure Active Directory 中的外部协作
description: 架构师和 IT 管理员指南：保护对内部资源的外部访问
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
ms.openlocfilehash: bc8ec364380fc5cb9f6e33a29cd8dac96ea1fb69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219821"
---
# <a name="securing-external-collaboration-in-azure-active-directory-and-microsoft-365"></a>保护 Azure Active Directory 和 Microsoft 365 中的外部协作

保护与外部合作伙伴之间的协作可确保合适的外部合作伙伴可以在合适的时间范围内适当访问内部资源。 通过全面的管理方法，可以降低安全风险，满足合规性目标，并确保你了解具有访问权限的人员。

不受管理的协作将导致访问所有权不清晰，还可能会暴露敏感资源。 转向安全和受管理的协作可以确保明确外部用户访问的所有权和责任。 这包括：

* 管理有权访问资源的外部组织和其中的用户。

* 确保访问是恰当的、经过审核的、有时间限制的（视具体情况而定）。

* 使业务负责人能够在 IT 搭建的防护栏内管理协作。

如果必须满足合规性框架的要求，则受管理的协作可以让你证明访问是否合理。

Microsoft 提供了全面的工具套件来保护外部访问。  Azure Active Directory (Azure AD) B2B 协作是所有外部协作计划的核心。 Azure AD B2B 可以与 Azure AD 中的其他工具和 Microsoft 365 服务中的工具集成，帮助保护和管理外部访问。

此文档集旨在让你能够从临时或松散的外部协作转向更安全的状态。 

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出顺序执行这些操作。


1. [确定外部访问的安全状况](1-secure-access-posture.md)

2. [了解当前状况](2-secure-access-current-state.md)

3. [创建治理计划](3-secure-access-plan.md)

4. [使用组以确保安全性](4-secure-access-groups.md)

5. [转换到 Azure AD B2B](5-secure-access-b2b.md)

6. [通过权利管理实现安全访问](6-secure-access-entitlement-managment.md)

7. [通过条件访问策略实现安全访问](7-secure-access-conditional-access.md)

8. [通过敏感度标签实现安全访问](8-secure-access-sensitivity-labels.md)

9. [实现对 Microsoft Teams、OneDrive 和 SharePoint 的安全访问](9-secure-access-teams-sharepoint.md)
