---
title: SSPR 和 Azure AD 多重身份验证的合并注册 - Azure Active Directory
description: 了解 Azure Active Directory 的合并注册体验，让用户注册 Azure AD 多重身份验证和自助式密码重置
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52e50aa4097dbb405b0c37a96426440fdf002810
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763132"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Azure Active Directory 的合并安全信息注册概述

在进行合并注册之前，用户分别注册了 Azure AD 多重身份验证和自助式密码重置 (SSPR) 的身份验证方法。 让人们感到困惑的是，多重身份验证和 SSPR 使用的方法相似，但他们却不得不同时注册这两个功能。 现在，通过合并注册，用户只需注册一次，便可同时获得多重身份验证和 SSPR 带来的好处。 建议观看[如何在 Azure AD 中启用和配置 SSPR](https://www.youtube.com/watch?v=rA8TvhNcCvQ) 这段视频

> [!NOTE]
> 自 2020 年 8 月 15 日起，将自动为所有新的 Azure AD 租户启用合并注册。 

本文概述了什么是合并安全注册。 如需合并安全注册入门指导，请参阅以下文章：

> [!div class="nextstepaction"]
> [启用合并安全注册](howto-registration-mfa-sspr-combined.md)

![“我的帐户”显示用户的已注册安全信息](media/concept-registration-mfa-sspr-combined/combined-security-info-defaults-registered.png)

在启用新体验之前，请查看以管理员为中心的文档和以用户为中心的文档，以确保你了解此功能的作用和效果。 根据[用户文档](https://support.microsoft.com/account-billing/set-up-your-security-info-from-a-sign-in-prompt-28180870-c256-4ebf-8bd7-5335571bf9a8)方面的培训，让用户准备好适应新的体验，并帮助确保成功推出。

Azure AD 组合安全信息注册适用于 Azure 美国政府，但不适用于 Azure 德国或 Azure 中国世纪互联。

> [!IMPORTANT]
> 同时启用了原始预览和增强合并注册体验的用户将看到新的行为。 同时启用了这两种体验的用户只会看到“我的帐户”体验。 “我的帐户”与合并注册的外观一致，并为用户提供无缝体验。 用户可以通过转到 [https://myaccount.microsoft.com](https://myaccount.microsoft.com) 来查看“我的帐户”。
>
> 尝试访问“安全信息”选项时可能会遇到错误消息，如“抱歉，无法登录”。 确认在 Web 浏览器中没有任何配置或组策略对象阻止第三方 cookie。

“我的帐户”页面根据访问该页面的计算机的语言设置进行本地化。 Microsoft 会存储浏览器缓存中使用的最新语言，因此后续尝试访问页面时将继续以最后使用的语言呈现。 如果清除缓存，则页面将重新呈现。

如果要强制使用特定语言，你可以将 `?lng=<language>` 添加到 URL 末尾，其中 `<language>` 是你想呈现的语言的代码。

![设置 SSPR 或其他安全验证方法](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>合并注册中的可用方法

合并注册支持以下身份验证方法和操作：

| 方法 | 注册 | 更改 | 删除 |
| --- | --- | --- | --- |
| Microsoft Authenticator | 是（最多为 5） | 否 | 是 |
| 其他 Authenticator 应用 | 是（最多为 5） | 否 | 是 |
| 硬件令牌 | 否 | 否 | 是 |
| 电话 | 是 | 是 | 是 |
| 备用号码 | 是 | 是 | 是 |
| 办公电话 | 是 | 是 | 是 |
| 电子邮件 | 是 | 是 | 是 |
| 安全性问题 | 是 | 否 | 是 |
| 应用密码 | 是 | 否 | 是 |
| FIDO2 安全密钥<br />仅来自[安全信息](https://mysignins.microsoft.com/security-info)页面的托管模式| 是 | 是 | 是 |

> [!NOTE]
> 应用密码仅适用于已强制执行多重身份验证的用户。 对于通过条件访问策略启用多重身份验证的用户，不能使用应用密码。

用户可以将以下选项之一设置为默认多重身份验证方法：

- Microsoft Authenticator - 推送通知
- Authenticator 应用或硬件令牌 - 验证码
- 电话呼叫
- 短信

第三方验证器应用不提供推送通知。 随着我们不断向 Azure AD 添加更多身份验证方法，这些方法可用于合并注册。

## <a name="combined-registration-modes"></a>合并注册模式

合并注册分为两种模式：中断和管理。

- 中断模式是一种类似于向导的体验，当用户在登录期间注册或刷新其安全信息时，会向用户显示此模式。
- 管理模式是用户配置文件的一部分，使用户能够管理其安全信息。

对于这两种模式，以前注册了可用于多重身份验证的方法的用户需要先执行多重身份验证，然后才能访问其安全信息。 用户必须确认其信息，然后才能继续使用以前注册的方法。 



### <a name="interrupt-mode"></a>中断模式

如果同时为你的租户启用了多重身份验证和 SSPR 策略，则合并注册会同时遵守这两个策略。 这些策略控制在登录期间用户是否被中断以便注册，以及哪些方法可用于注册。 如果仅启用了 SSPR 策略，则用户将能够跳过注册中断并在稍后完成注册。

下面是可能提示用户注册或刷新其安全信息的示例应用场景：

- 通过标识保护强制执行多重身份验证注册：要求用户在登录期间进行注册。 他们注册多重身份验证方法和 SSPR 方法（如果为用户启用了 SSPR）。
- 通过每用户的多重身份验证强制执行多重身份验证注册：要求用户在登录期间进行注册。 他们注册多重身份验证方法和 SSPR 方法（如果为用户启用了 SSPR）。
- 通过条件访问或其他策略强制执行多重身份验证注册：要求用户在使用需要多重身份验证的资源时进行注册。 他们注册多重身份验证方法和 SSPR 方法（如果为用户启用了 SSPR）。
- 强制执行 SSPR 注册：要求用户在登录时进行注册。 他们只注册 SSPR 方法。
- 强制执行 SSPR 刷新：需要用户在管理员设置的时间间隔内检查其安全信息。用户会看到其信息，并可以确认当前信息或在需要时做出更改。

强制执行注册时，将向用户显示符合多重身份验证和 SSPR 策略所需的最低方法数量（从最安全到最不安全）。

请考虑以下示例场景：

- 已为用户启用 SSPR。 SSPR 策略需要两种方法来重置并已启用 Authenticator 应用、电子邮件和电话。
- 当用户选择注册时，需要两种方法：
   - 默认情况下会为用户显示 Authenticator 应用和电话。
   - 用户可以选择注册电子邮件，而不是 Authenticator 应用或电话。

以下流程图描述了在登录期间中断以进行注册时向用户显示的方法：

![合并安全信息流程图](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

如果同时启用了多重身份验证和 SSPR，建议强制执行多重身份验证注册。

如果 SSPR 策略要求用户定期检查其安全信息，则用户会在登录期间被中断，并会看到所有注册方法。 他们可以确认当前信息是否是最新的，也可以在需要时进行更改。 用户在访问此页面时，必须执行多重身份验证。

### <a name="manage-mode"></a>管理模式

用户可以通过转到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 或通过从“我的帐户”选择“安全信息”来访问管理模式。 在该页面上，用户可以添加方法，删除或更改现有方法，更改默认方法等。

## <a name="key-usage-scenarios"></a>主要使用场景

### <a name="set-up-security-info-during-sign-in"></a>在登录过程中设置安全信息

管理员已强制执行注册。

用户尚未设置全部所需安全信息，并转到了 Azure 门户。 输入用户名和密码后，系统会提示用户设置安全信息。 然后，用户按照向导中显示的步骤来设置所需的安全信息。 如果设置允许，用户可以选择设置默认显示的方法以外的方法。 完成向导后，用户将查看他们设置的方法及其用于多重身份验证的默认方法。 若要完成安装过程，用户需要确认信息并继续转到 Azure 门户。

### <a name="set-up-security-info-from-my-account"></a>从“我的帐户”中设置安全信息

管理员尚未强制执行注册。

尚未设置全部所需安全信息的用户转到 [https://myaccount.microsoft.com](https://myaccount.microsoft.com)。 用户选择左侧窗格中的“安全信息”。 在该页面上，用户可以选择添加方法，选择可用的任何方法，然后按照步骤设置该方法。 完成后，用户会看到在“安全信息”页面上设置的方法。

### <a name="delete-security-info-from-my-account"></a>从“我的帐户”中删除安全信息

之前至少设置了一个方法的用户导航到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)。 用户选择删除以前注册的方法之一。 完成后，用户将不会再在“安全信息”页面上看到该方法。

### <a name="change-the-default-method-from-my-account"></a>从“我的帐户”中更改默认方法

之前至少设置了一个可用于多重身份验证的方法的用户导航到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)。 用户将当前默认方法更改为其他默认方法。 完成后，用户会在“安全信息”页面上看到新的默认方法。

### <a name="switch-directory"></a>切换目录

外部标识（例如 B2B 用户）可能需要切换目录，以更改第三方租户的安全注册信息。 此外，当访问资源租户的用户更改其主租户的设置，但未在资源租户中看到相应更改时，他们可能会感到困惑。 

例如，某用户将 Microsoft Authenticator 应用推送通知设置为登录主租户时的主要身份验证，同时将短信/文本设置为另一种验证选择。 此用户还在资源租户上配置了短信/文本选项。 如果此用户取消将短信/文本作为其主租户的身份验证选项之一，则在访问资源租户并被要求回复短信/文本消息时，他们会感到困惑。 

若要在 Azure 门户中切换目录，请单击右上角的用户帐户名称，然后单击“切换目录”。

![外部用户可以切换目录。](media/concept-registration-mfa-sspr-combined/switch-directory.png)

## <a name="next-steps"></a>后续步骤

若要开始，请参阅[启用自助式密码重置](tutorial-enable-sspr.md)和[启用 Azure AD 多重身份验证](tutorial-enable-azure-mfa.md)教程。

了解如何[在租户中启用合并注册](howto-registration-mfa-sspr-combined.md)或[强制用户重新注册身份验证方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)。

你还可以查看 [Azure AD 多重身份验证和 SSPR 的可用方法](concept-authentication-methods.md)。