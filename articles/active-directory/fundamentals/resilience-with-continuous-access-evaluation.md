---
title: 在 Azure Active Directory 中使用持续存取评估生成复原能力
description: 使用 CAE 的架构师和 IT 管理员指南
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
ms.openlocfilehash: 8ad36c2a7f47948d9362b85e78355e6046cda703
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919333"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>使用持续存取评估构建复原能力

[连续访问评估](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) (CAE) 允许 Azure AD 应用程序订阅关键事件，然后对这些事件进行评估和强制执行。 这包括对以下事件的评估：

* 正在删除或禁用的用户帐户

* 用户的密码已更改

* 为用户启用 MFA。

* 管理员显式撤销令牌。

* 检测到提升的用户风险。

因此，应用程序可以根据由 Azure AD 发出信号的事件拒绝未过期的令牌，如下图所示。

![CAE 的 conceptualiagram](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>CAE 有何帮助？

这种机制允许 Azure AD 颁发更长生存期的令牌，同时使应用程序能够仅在需要时撤销访问权限并强制重新进行身份验证。 此模式的最终结果是更少的调用以获取令牌，这意味着端到端流更具复原性。 

若要使用 CAE，服务和客户端都必须支持 CAE。 Microsoft 365 服务（如 Exchange Online、团队和 SharePoint Online 支持 CAE）。 在客户端，使用这些 Office 365 服务的基于浏览器的体验 (例如，Outlook Web App) 和 Office 365 native client 的特定版本都支持 CAE。 更多的 Microsoft 云服务将 CAE。

Microsoft 正在与行业合作构建 [标准](https://openid.net/wg/sse/) ，使第三方应用程序能够使用此功能。 你还可以开发支持 CAE 的应用程序。 有关详细信息，请参阅如何在应用程序中构建复原能力。

## <a name="how-do-i-implement-cae"></a>如何实现实现 CAE？

* 在 Azure AD 安全配置中[启用 CAE](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) 。

* 确保你的组织使用 Microsoft Office 本机应用程序的 [兼容版本](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) 。

* [优化重新验证的提示](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime)。

 
## <a name="next-steps"></a>后续步骤
适用于管理员和架构师的复原资源
 
* [通过凭据管理构建复原能力](resilience-in-credentials.md)

* [用设备状态生成复原能力](resilience-with-device-states.md)

* [在外部用户身份验证中构建复原能力](resilience-b2b-authentication.md)

* [在混合身份验证中构建复原能力](resilience-in-hybrid.md)

* [使用应用程序代理通过应用程序访问生成恢复能力](resilience-on-premises-access.md)

面向开发人员的复原资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)
