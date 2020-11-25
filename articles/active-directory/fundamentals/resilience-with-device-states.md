---
title: 在 Azure Active Directory 中使用设备状态生成复原能力
description: 使用设备状态构建恢复能力的架构师和 IT 管理员指南
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
ms.openlocfilehash: 4075cb71cb4dae7ac506c16e3987070cfa10bb09
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919331"
---
# <a name="build-resilience-with-device-states"></a>用设备状态生成复原能力

通过使用 Azure AD 启用 [设备状态](../devices/overview.md) ，管理员可以创建 [条件性访问策略](../conditional-access/overview.md) ，根据设备状态控制对应用程序的访问。 设备的新增优点是，它满足对资源访问的强身份验证要求，从而减少了额外的 MFA 身份验证请求并改善了复原能力。 

以下流程图显示了在启用设备状态 Azure AD 中载入设备的不同方法。 你可以在你的组织中使用多个。

![选择设备状态的流程图](./media/resilience-with-device-states/admin-resilience-devices.png)

当你使用 [设备状态](../devices/overview.md)时，用户在大多数情况下都可以通过 [主刷新令牌](../devices/concept-primary-refresh-token.md) 对资源进行单一登录， (PRT) 。 PRT 包含有关用户和设备的声明，可用于获取用于从设备访问应用程序的身份验证令牌。 PRT 的有效期为14天，并且只要用户主动使用设备，就会持续续订，从而为用户提供弹性体验。 PRT 也可以通过多种方式获取多重身份验证声明。 有关详细信息，请参阅 [WHEN PRT get a MFA 声明](../devices/concept-primary-refresh-token.md)。

## <a name="how-do-device-states-help"></a>设备状态如何？

当使用 PRT 请求访问应用程序时，其设备、会话和 MFA 声明受 Azure AD 信任。 当管理员创建需要基于设备的控件或多重身份验证控制的策略时，可以通过设备状态满足策略要求，而无需尝试多重身份验证。 用户在同一设备上不会看到其他多重身份验证提示。 这会增加 Azure MFA 服务中断的复原能力，或本地电信提供商等依赖项。

## <a name="how-do-i-implement-device-states"></a>如何实现实现设备状态吗？

* 为公司拥有的 Windows 设备启用 [混合 Azure AD](../devices/hybrid-azuread-join-plan.md) 联接和 [Azure AD 联接](../devices/azureadjoin-plan.md) ，并在可能的情况下要求它们加入。 如果不可能，则要求它们已注册。

  如果你的组织中有较早版本的 Windows，请将这些设备升级为使用 Windows 10。

* 将用户浏览器访问的功能标准化，以将 [Microsoft Edge](https://docs.microsoft.com/deployedge/microsoft-edge-security-identity) 或 Google Chrome 与 [支持](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)的 [扩展](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) 结合使用，从而使使用 PRT 的 WEB 应用程序实现无缝 SSO。

* 对于个人或公司拥有的 iOS 和 Android 设备，请部署 [Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md)。 除了多重身份验证和无密码的登录功能外，Microsoft Authenticator 应用还可通过对最终用户进行更少身份验证的 [中转身份验证](../develop/brokered-auth.md) 来跨本机应用程序进行单一登录。

* 对于个人或公司拥有的 iOS 和 Android 设备，使用 [移动应用程序管理](https://docs.microsoft.com/mem/intune/apps/app-management.md) 来安全地访问具有较少身份验证请求的公司资源。 

* [使用适用于 Apple 设备的 Microsoft 企业 SSO 插件 (预览版) ](../develop/apple-sso-plugin.md)。 这会注册设备，并跨浏览器和本机 Azure AD 应用程序提供 SSO。 

## <a name="next-steps"></a>后续步骤
适用于管理员和架构师的复原资源
 
* [通过凭据管理构建复原能力](resilience-in-credentials.md)

* [使用持续存取评估 (CAE) 生成复原能力 ](resilience-with-continuous-access-evaluation.md)

* [在外部用户身份验证中构建复原能力](resilience-b2b-authentication.md)

* [在混合身份验证中构建复原能力](resilience-in-hybrid.md)

* [使用应用程序代理通过应用程序访问生成恢复能力](resilience-on-premises-access.md)


面向开发人员的复原资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)
