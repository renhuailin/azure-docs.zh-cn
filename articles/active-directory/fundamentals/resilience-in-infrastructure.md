---
title: 使用 Azure Active Directory 在 IAM 基础结构中构建复原能力
description: 指导架构师和 IT 管理员在 IAM 基础结构中构建复原能力，以应对中断。
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
ms.openlocfilehash: 3fc12433a187f030a0349247680c731c04a76274
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402804"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>在标识和访问管理基础结构中构建复原能力

Azure Active Directory 是一个全局云标识和访问管理系统，可为组织资源提供身份验证、授权等关键服务。 本文档将为你提供指导，让你了解、控制和减轻身份验证或授权服务中断给依赖于 Azure Active Directory (Azure AD) 的资源带来的风险。 

本文档集面向

* 标识架构师

* 标识服务所有者

* 标识运营团队

另请参阅面向[应用程序开发人员](./resilience-app-development-overview.md)和 [Azure AD B2C 系统](resilience-b2c.md)的文档。

## <a name="what-is-resilience"></a>什么是复原能力？

就标识基础结构而言，复原能力是指能够承受身份验证和授权等服务的中断或其他组件的故障，并对业务、用户和运营的影响极小，甚至没有影响。 中断带来的影响可能会很严重，因此需要认真规划复原能力。

## <a name="why-worry-about-disruption"></a>为什么要担心服务中断？

如果调用的任何组件失败，则对身份验证系统的每次调用都会中断。 当身份验证由于底层组件故障而中断时，你的用户将无法访问其应用程序。 因此，减少身份验证调用次数和这些调用中的依赖项数量对于复原能力至关重要。 应用程序开发人员可以对令牌请求频率进行一些控制。 例如，与开发人员合作，确保他们尽可能为其应用程序使用 Azure AD 托管标识。 

在基于令牌的身份验证系统（如 Azure AD）中，用户的应用程序（客户端）必须先从标识系统获取安全令牌，然后才能访问应用程序或其他资源。 在有效期内，客户端可以多次提供同一令牌来访问应用程序。

当提供给应用程序的令牌过期时，应用程序会拒绝该令牌，客户端必须从 Azure AD 获取新令牌。 获取新令牌可能需要用户交互，例如凭据提示或满足身份验证系统的其他要求。 通过使用生存期较长的令牌降低身份验证调用频率，可以减少不必要的交互。 但是，你必须在令牌生存期与策略评估减少所造成的风险之间取得平衡。 有关管理令牌生存期的详细信息，请参阅[优化重新验证提示](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)一文。

## <a name="ways-to-increase-resilience"></a>提高复原能力的方法
下图展示了六种可以提高复原能力的具体方法。 本文“后续步骤”部分中链接的文章详细说明了每种方法。
  
![管理员复原能力概览图](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>后续步骤
面向管理员和架构师的复原能力资源
 
* [利用凭据管理构建复原能力](resilience-in-credentials.md)

* [使用设备状态构建复原能力](resilience-with-device-states.md)

* [使用连续访问评估 (CAE) 构建复原能力](resilience-with-continuous-access-evaluation.md)

* [构建外部用户身份验证的复原能力](resilience-b2b-authentication.md)

* [在混合身份验证中构建复原能力](resilience-in-hybrid.md)

* [利用应用程序代理构建应用程序访问的复原能力](resilience-on-premises-access.md)

适用于开发人员的复原能力资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)
