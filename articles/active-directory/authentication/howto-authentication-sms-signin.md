---
title: Azure Active Directory 的基于短信的用户登录
description: 了解如何配置并允许用户使用短信登录到 Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/18/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: anjusingh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 284a1c9d8b79a0d4e9c479afea9713a09c8c0fa4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429608"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>为用户配置和启用通过 Azure Active Directory 进行基于短信的身份验证 

为了简化和保护登录到应用程序和服务的过程，Azure Active Directory (Azure AD) 提供多种身份验证选项。 利用基于短信的身份验证，用户无需提供甚至无需知道自己的用户名和密码即可登录。 在标识管理员创建了用户帐户后，用户可以在登录提示中输入他们的电话号码。 他们会通过短信收到身份验证代码，然后可以提供这个代码来完成登录。 这种身份验证方法简化了对应用程序和服务的访问，尤其是对一线工作人员而言。

本文介绍如何在 Azure AD 中为选定的用户或组启用基于短信的身份验证。 有关支持使用基于短信的登录的应用列表，请参阅[应用对基于短信的身份验证的支持](how-to-authentication-sms-supported-apps.md)。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一个与订阅关联的 Azure Active Directory 租户。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 需要在 Azure AD 租户中拥有“全局管理员”特权才能启用基于短信的身份验证。
* 短信身份验证方法策略中启用的每个用户都必须获得许可，即使他们不使用该方法也是如此。 每个启用的用户都必须具有以下 Azure AD、EMS、Microsoft 365 许可证之一：
    * [Microsoft 365 F1 或 F3][m365-firstline-workers-licensing]
    * [Azure Active Directory Premium P1 或 P2][azure-ad-pricing]
    * [企业移动性 + 安全性 (EMS) E3 或 E5][ems-licensing] 或 [Microsoft 365 E3 或 E5][m365-licensing]
    * [Office 365 F3][o365-f3]

## <a name="known-issues"></a>已知问题

以下是一些已知问题：

* 基于短信的身份验证目前与 Azure AD 多重身份验证不兼容。
* 基于短信的身份验证与本机 Office 应用程序不兼容，Teams 除外。
* 不建议对 B2B 帐户使用基于短信的身份验证。
* 联合用户不会在主租户中进行身份验证。 他们只会在云中进行身份验证。
* 如果用户的默认登录方法是给你的电话号码发短信或打电话，则在多重身份验证过程中会自动发送短信代码或语音呼叫。 截至 2021 年 6 月，某些应用程序会要求用户首先选择“发短信”或“打电话”。 此选项可防止为不同应用发送过多的安全码。 如果默认登录方法是 Microsoft Authenticator 应用（[强烈推荐](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)）,则将自动发送应用通知。

## <a name="enable-the-sms-based-authentication-method"></a>启用基于短信的身份验证方法

若要在组织中启用和使用基于短信的身份验证，需要执行三个主要步骤：

* 启用身份验证方法策略。
* 选择可以使用基于短信的身份验证方法的用户或组。
* 为每个用户帐户分配一个电话号码。
    * 可以在 Azure 门户中（如本文所示）以及“我的员工”或“我的帐户”中分配此电话号码 。

首先，让我们为 Azure AD 租户启用基于短信的身份验证。

1. 以全局管理员的身份登录到 [Azure 门户][azure-portal]。
1. 搜索并选择“Azure Active Directory”。
1. 从“Azure Active Directory”窗口左侧的导航菜单中，选择“安全性”>“身份验证方法”>“身份验证方法策略”。

    [![在 Azure 门户中浏览到“身份验证方法策略”窗口并将其选中。](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. 从可用身份验证方法列表中选择“短信”。
1. 将“启用”设置为“是”。

    ![在身份验证方法策略窗口中启用短信身份验证](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    可以选择为“所有用户”启用基于短信的身份验证，或者选择用户和组。  在下一部分，你将为测试用户启用基于短信的身份验证。

## <a name="assign-the-authentication-method-to-users-and-groups"></a>向用户和组分配身份验证方法

在 Azure AD 租户中启用基于短信的身份验证后，现在请选择要被允许使用此身份验证方法的某些用户或组。

1. 在短信身份验证策略窗口中，将“目标”设置为“选择用户”。
1. 选择“添加用户或组”，然后选择某个测试用户或组，例如“Contoso 用户”或“Contoso 短信用户”。 

    [![在 Azure 门户中选择要启用基于短信的身份验证的用户或组。](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. 选择用户或组后，选择“选择”，然后保存更新的身份验证方法策略。 

短信身份验证方法策略中启用的每个用户都必须获得许可，即使他们不使用该方法也是如此。 确保为在身份验证方法策略中启用的用户提供适当的许可证，尤其是为大型用户组启用此功能时。

## <a name="set-a-phone-number-for-user-accounts"></a>为用户帐户设置电话号码

现在已为用户启用了基于短信的身份验证，但必须将其电话号码与 Azure AD 中的用户个人资料相关联，然后用户才能登录。 可以让用户在“我的帐户”中[自己设置此电话号码](../user-help/sms-sign-in-explainer.md)，也可以由你使用 Azure 门户来分配电话号码。 电话号码可由全局管理员、身份验证管理员或特权身份验证管理员进行设置。

设置用于短信登录的电话号码后，还可将该电话号码用于 [Azure AD 多重身份验证][tutorial-azure-mfa]和[自助式密码重置][tutorial-sspr]。

1. 搜索并选择“Azure Active Directory”。
1. 在“Azure Active Directory”窗口左侧的导航菜单中，选择“用户”。
1. 选择在上一部分中为其启用了基于短信的身份验证的用户（例如“Contoso 用户”），然后选择“身份验证方法”。
1. 选择“+ 添加身份验证方法”，然后在“选择方法”下拉菜单中选择“电话号码”。

    输入用户的电话号码，包括国家/地区代码，例如 +1 xxxxxxxxx。 Azure 门户将验证电话号码的格式是否正确。

    然后，根据需要从“电话类型”下拉菜单中选择“移动电话”、“备用移动电话”，或“其他”   。

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="在 Azure 门户中为用户设置一个用于基于短信的身份验证的电话号码":::

    该电话号码在租户中必须是唯一的。 如果尝试为多个用户使用同一个电话号码，将显示错误消息。

1. 若要将电话号码应用于用户的帐户，请选择“添加”。

成功预配后，会出现一个勾选标记，表示已启用短信登录。

## <a name="test-sms-based-sign-in"></a>测试基于短信的登录

若要测试现已启用基于短信的登录的用户帐户，请完成以下步骤：

1. 在新的 InPrivate 或 Incognito Web 浏览器窗口中打开 [https://www.office.com][office]
1. 在右上角选择“登录”。
1. 在登录提示下，输入在上一部分中设置的与用户关联的电话号码，然后选择“下一步”。

    ![在登录提示下为测试用户输入电话号码](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. 一条短信将发送到提供的电话号码。 若要完成登录过程，请在登录提示下输入短信中提供的 6 位数代码。

    ![输入通过短信发送到用户电话号码的确认代码](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. 现在，用户无需提供用户名或密码就已登录。


## <a name="troubleshoot-sms-based-sign-in"></a>排查基于短信的登录的问题

如果在启用和使用基于短信的登录时遇到问题，可以使用以下方案和故障排除步骤。 有关支持使用基于短信的登录的应用列表，请参阅[应用对基于短信的身份验证的支持](how-to-authentication-sms-supported-apps.md)。


### <a name="phone-number-already-set-for-a-user-account"></a>已经为用户帐户设置了电话号码

如果用户已经注册 Azure AD 多重身份验证和/或自助式密码重置 (SSPR)，则他们已拥有与其帐户关联的电话号码。 此电话号码不会自然而然就可用于基于短信的登录。

对于已经为其帐户设置了电话号码的用户，其“我的个人资料”页中会显示一个“启用短信登录”按钮。 选择此按钮，即可将该帐户与基于短信的登录和以前的 Azure AD 多重身份验证或 SSPR 注册配合使用。

若要详细了解最终用户体验，请参阅[电话号码的短信登录用户体验](../user-help/sms-sign-in-explainer.md)。

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>尝试对用户帐户设置电话号码时出错

如果在 Azure 门户中尝试为用户帐户设置电话号码时收到错误，请查看以下故障排除步骤：

1. 确保你能够使用基于短信的登录。
1. 确认用户帐户已在“短信”身份验证方法策略中启用。
1. 确保使用已在 Azure 门户中经过验证的正确格式设置电话号码（例如 +1 4251234567）。
1. 确保电话号码未在租户中的其他地方使用。
1. 检查帐户中是否设置了语音号码。 如果设置了语音号码，请删除并尝试重新输入电话号码。

## <a name="next-steps"></a>后续步骤

- 有关支持使用基于短信的登录的应用列表，请参阅[应用对基于短信的身份验证的支持](how-to-authentication-sms-supported-apps.md)。
- 有关不使用密码登录 Azure AD 的其他方法（例如 Microsoft Authenticator 应用或 FIDO2 安全密钥），请参阅 [Azure AD 无密码身份验证选项][concepts-passwordless]。
- 你还可以使用 Microsoft Graph REST API 版本来[启用][rest-enable]或[禁用][rest-disable]基于短信的登录。


<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
[azure-ad-pricing]: https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing
