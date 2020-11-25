---
title: 利用 Azure Active Directory 在 IAM 基础结构中构建复原能力
description: 为架构师和 IT 管理员提供了一个指南，使其不会对其 IAM 基础结构的中断进行恢复。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ad97a822aaa6477616a6661a579df6c4ec82729
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919337"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>在标识和访问管理基础结构中构建复原能力

Azure Active Directory 是一种全局云标识和访问管理系统，它提供了对组织资源的身份验证和授权等关键服务。 本文档为您提供了了解、包含和缓解依赖于 Azure Active Directory (Azure AD) 的资源的身份验证或授权服务中断的风险。 

文档集设计用于

* 标识架构师

* 标识服务所有者

* 标识操作团队

另请参阅 [应用程序开发人员](https://aka.ms/azureadresilience/developer) 和 [Azure AD B2C 系统](resilience-b2c.md)的文档。

## <a name="what-is-resilience"></a>什么是复原能力？

在标识基础结构的上下文中，复原能力是指经受中断服务（如身份验证和授权）或其他组件故障的能力，对业务、用户和操作的影响最小或没有影响。 中断的影响可能会很严重，而且复原需要用心规划。

## <a name="why-worry-about-disruption"></a>为什么担心中断呢？

如果对该身份验证系统的 Azure AD 调用链中的任何组件发生故障，则每次调用该身份验证系统的情况都受到中断。 这意味着，如果你的基础结构的任何部分出现问题，则可能会中断，因为用户无法访问所需的应用程序。 因此，减少身份验证调用数和这些调用中的依赖关系数对于复原能力非常重要。 应用程序开发人员可以断言某些控制请求令牌的频率。 例如，与开发人员合作，以确保他们使用的应用程序 Azure AD 托管标识。 

在 Azure AD 之类的基于令牌的身份验证系统中，用户的应用程序 (客户端) 必须从标识系统获取安全令牌，然后才能访问应用程序或其他资源。 在有效期内，客户端可以多次提供同一令牌来访问应用程序。

当提供给应用程序的令牌过期时，应用程序会拒绝令牌，并且客户端必须从 Azure AD 获取新令牌。 获取新令牌可能需要用户交互，如凭据提示。 降低具有较长生存期令牌的身份验证呼叫的频率可降低这种风险。 但是，必须在令牌生存期与通过较少的策略评估所创建的风险之间取得平衡。 有关管理令牌生存期的详细信息，请参阅有关 [优化重新验证提示](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime)的文章。

## <a name="ways-to-increase-resilience"></a>提高复原能力的方法
下图显示了可以提高复原能力的六个具体方法。 本文的后续步骤部分链接的文章中详细介绍了每种方法。
  
![显示管理员复原能力概述的示意图](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>后续步骤
适用于管理员和架构师的复原资源
 
* [通过凭据管理构建复原能力](resilience-in-credentials.md)

* [用设备状态生成复原能力](resilience-with-device-states.md)

* [使用持续存取评估 (CAE) 生成复原能力 ](resilience-with-continuous-access-evaluation.md)

* [在外部用户身份验证中构建复原能力](resilience-b2b-authentication.md)

* [在混合身份验证中构建复原能力](resilience-in-hybrid.md)

* [使用应用程序代理通过应用程序访问生成恢复能力](resilience-on-premises-access.md)

面向开发人员的复原资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)
