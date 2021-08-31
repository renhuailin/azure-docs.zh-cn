---
title: Azure AD 多重身份验证概述
description: 了解 Azure AD 多重身份验证如何帮助保护对数据和应用程序的访问，同时满足用户对简单登录过程的需求。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 133ec8c46bb227f2f43cb9243ca5db4a4e9ac32b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737349"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>工作原理：Azure AD 多重身份验证

多重身份验证是一种过程。在该过程中，系统会在用户登录时提示其输入其他形式的标识，例如在其手机上输入代码或提供指纹扫描。

如果只使用密码对用户进行身份验证，则会留下不安全的矢量，容易受到攻击。 如果密码弱或者已在其他位置公开，那么如何确定是该用户在使用用户名和密码登录，还是攻击者在登录？ 需要另一种形式的身份验证时，会提高安全性，因为攻击者并不容易获取或复制进行多重身份验证所需的额外内容。

![不同形式的多重身份验证的概念图](./media/concept-mfa-howitworks/methods.png)

至少需要采用下列身份验证方法中的两种，才能使 Azure AD 多重身份验证发挥作用：

* 你知道的某样东西，通常为密码。
* 你有的某样东西，例如无法轻易复制的可信设备，如电话或硬件密钥。
* 自身的特征 - 生物识别，如指纹或面部扫描。

Azure AD 多重身份验证还可以进一步保护密码重置。 当用户自行注册 Azure AD 多重身份验证时，还可以一步注册自助密码重置。 管理员可以选择辅助身份验证的形式，并可根据配置决策为 MFA 配置质询。 

应用程序或服务不需要进行任何更改即可使用 Azure AD 多重身份验证。 验证提示是 Azure AD 登录事件的一部分，它会在需要时自动请求并处理 MFA 质询。

![登录屏幕上使用的身份验证方法](media/concept-authentication-methods/overview-login.png)

## <a name="available-verification-methods"></a>可用的验证方法

当用户登录到应用程序或服务并收到 MFA 提示时，他们可以从其注册的附加验证形式中选择一种。 用户可以访问“[的个人资料](https://myprofile.microsoft.com) 以编辑或添加验证方法。

以下其他形式的验证可以与 Azure AD 多重身份验证一起使用：

* Microsoft Authenticator 应用
* OATH 硬件令牌（预览版）
* OATH 软件令牌
* SMS
* 语音呼叫

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>如何启用和使用 Azure AD 多重身份验证

所有 Azure AD 租户都可以使用[安全默认设置](../fundamentals/concept-fundamentals-security-defaults.md)来为所有用户快速启用 Microsoft Authenticator。 可以为用户和组启用 Azure AD 多重身份验证，以在登录事件期间提示其进行其他验证。 

如需更精细的控制，可使用[条件性访问](../conditional-access/overview.md)策略来定义需要 MFA 的事件或应用程序。 通过使用这些策略，可在用户使用企业网络或已注册的设备时，允许其执行常规登录，但在其远程访问或使用个人设备时向其提示其他验证。

![有关条件访问如何保护登录过程的概览图](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>后续步骤

若要了解有关许可的信息，请参阅 [Azure AD 多重身份验证的功能和许可证](concept-mfa-licensing.md)。

若要了解有关不同身份验证和验证方法的详细信息，请参阅 [Azure Active Directory 中的身份验证方法](concept-authentication-methods.md)。

在以下教程中，为了展示运行中的 MFA，为一组测试用户启用了 Azure AD 多重身份验证：

> [!div class="nextstepaction"]
> [启用 Azure AD 多重身份验证](./tutorial-enable-azure-mfa.md)