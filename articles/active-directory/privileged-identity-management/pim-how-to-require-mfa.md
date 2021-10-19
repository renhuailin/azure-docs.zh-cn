---
title: MFA 或 2FA 和 Privileged Identity Management - Azure AD | Microsoft Docs
description: 了解 Azure AD Privileged Identity Management (PIM) 如何验证多重身份验证 (MFA)。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e263de51c234c0572584911b3bd8f7d4eb7c487c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667205"
---
# <a name="multifactor-authentication-and-privileged-identity-management"></a>多重身份验证和 Privileged Identity Management

我们建议要求所有管理员使用多重身份验证（MFA 或 2FA）。 多重身份验证降低了使用已泄露的密码进行攻击的风险。

可以要求用户在登录时完成多重身份验证质询。 还可以要求用户在 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 中激活角色时完成多重身份验证质询。 这样一来，即使用户在登录时未完成多重身份验证质询，Privileged Identity Management 也会提示他们完成此操作。

> [!IMPORTANT]
> 目前，Azure AD 多重身份验证仅适用于工作或学校帐户，不适用于 Microsoft 个人帐户（通常是用于登录 Skype、Xbox 或 Outlook.com 等 Microsoft 服务的个人帐户）。 因此，使用个人帐户的任何人都不是符合条件的管理员，因为他们无法使用多重身份验证激活其角色。 如果这些用户需要继续使用 Microsoft 帐户管理工作负荷，请立即将其提升到永久管理员。

## <a name="how-pim-validates-mfa"></a>PIM 如何验证 MFA

用户激活角色时，有两个选项可用于验证多重身份验证。

最简单的选项是对激活特权角色的用户使用 Azure AD 多重身份验证。 为此，请首先检查这些用户是否已获得许可（如有必要），以及是否已注册 Azure AD 多重身份验证。 若要详细了解如何部署 Azure AD 多重身份验证，请参阅[部署基于云的 Azure AD 多重身份验证](../authentication/howto-mfa-getstarted.md)。 建议（但不是必需）将 Azure AD 配置为在用户登录时针对这些用户强制执行多重身份验证。 这是因为多重身份验证检查将由 Privileged Identity Management 本身进行。

或者，如果用户在本地进行身份验证，可以让标识提供者负责进行多重身份验证。 例如，如果将 AD 联合身份验证服务配置为需要基于智能卡的身份验证才能访问 Azure AD，[将 Azure AD 多重身份验证与 AD FS 配合使用来保护云资源](../authentication/howto-mfa-adfs.md)包含有关配置 AD FS 以向 Azure AD 发送声明的说明。 用户尝试激活某个角色时，Privileged Identity Management 将在收到相应声明后，接受已经为该用户验证了多重身份验证。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
