---
title: 在 Azure Active Directory 中使用设备状态构建复原能力
description: 架构师和 IT 管理员指南：使用设备状态构建复原能力
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
ms.openlocfilehash: 6d476be7a417cfc31cca76d3409074aaaa281a56
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724604"
---
# <a name="build-resilience-with-device-states"></a>使用设备状态构建复原能力

通过在 Azure AD 中启用[设备状态](../devices/overview.md)，管理员可以创建根据设备状态控制应用程序访问权限的[条件访问](../conditional-access/overview.md)策略。 设备的额外好处是，它满足了访问资源时的强身份验证要求，因而减少了额外的 MFA 身份验证请求并提高了复原能力。 

下面的流程图显示了在 Azure AD 中加入启用设备状态的设备的不同方法。 你可以在你的组织中使用多种方法。

![选择设备状态的流程图](./media/resilience-with-device-states/admin-resilience-devices.png)

当你使用[设备状态](../devices/overview.md)时，用户在多数情况下都可通过[主刷新令牌](../devices/concept-primary-refresh-token.md) (PRT) 单一登录到资源。 PRT 包含有关用户和设备的声明，并且可用于获取从设备访问应用程序所用的身份验证令牌。 PRT 的有效期为 14 天，只要用户经常使用该设备，就会持续续订，为用户提供可复原的体验。 PRT 也可以通过多种方式获取多重身份验证声明。 有关详细信息，请参阅 [PRT 何时获取 MFA 声明](../devices/concept-primary-refresh-token.md)。

## <a name="how-do-device-states-help"></a>设备状态有何作用？

使用 PRT 请求访问某个应用程序时，Azure AD 会信任 PRT 的设备、会话和 MFA 声明。 当管理员创建需要基于设备的控制或多重身份验证控制的策略时，可以通过设备状态来满足策略要求，而无需尝试多重身份验证。 用户在同一设备上不会另外看到多重身份验证提示。 这会增强抵御 Azure MFA 服务或其依赖方（例如本地电信提供商）服务中断的复原能力。

## <a name="how-do-i-implement-device-states"></a>如何实现实现设备状态？

* 为公司拥有的 Windows 设备启用[已建立混合 Azure AD 联接](../devices/hybrid-azuread-join-plan.md)和 [Azure AD 联接](../devices/azureadjoin-plan.md)，并在可能的情况下要求设备建立联接。 如果无法实现，则要求设备进行注册。

  如果组织中有设备使用较早版本的 Windows，请将这些设备升级为使用 Windows 10。

* 将用户浏览器访问的功能标准化，以将 [Microsoft Edge](/deployedge/microsoft-edge-security-identity) 或 Google Chrome 与 [支持](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)的 [扩展](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) 结合使用，从而使使用 PRT 的 WEB 应用程序实现无缝 SSO。

* 对于个人或公司拥有的 iOS 和 Android 设备，请部署 [Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md)。 除了多重身份验证和无密码的登录功能外，Microsoft Authenticator 应用还可通过对最终用户进行更少身份验证的 [中转身份验证](../develop/msal-android-single-sign-on.md) 来跨本机应用程序进行单一登录。

* 对于个人或公司拥有的 iOS 和 Android 设备，请使用[移动应用管理](/mem/intune/apps/app-management)来安全访问公司资源，该方法可以减少身份验证请求次数。 

* [使用适用于 Apple 设备的 Microsoft 企业 SSO 插件（预览版）](../develop/apple-sso-plugin.md)。 该插件可以注册设备，并提供各浏览器和本机 Azure AD 应用程序的 SSO。 

## <a name="next-steps"></a>后续步骤
面向管理员和架构师的复原能力资源
 
* [利用凭据管理构建复原能力](resilience-in-credentials.md)

* [使用连续访问评估 (CAE) 构建复原能力](resilience-with-continuous-access-evaluation.md)

* [在外部用户身份验证中构建复原能力](resilience-b2b-authentication.md)

* [在混合身份验证中构建复原能力](resilience-in-hybrid.md)

* [使用应用程序代理通过应用程序访问生成恢复能力](resilience-on-premises-access.md)


适用于开发人员的复原能力资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)