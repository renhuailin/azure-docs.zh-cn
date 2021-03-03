---
title: 身份验证方法活动-Azure Active Directory
description: 概述你的组织注册并用于登录和执行密码重置的身份验证方法。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6566ee7e744411fc3b7005938f95181e5c5ec94d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645127"
---
# <a name="authentication-methods-activity"></a>身份验证方法活动 

使用新的 "身份验证方法" 活动面板，管理员可以监视其组织内的身份验证方法注册和使用情况。 此报告功能可让组织了解正在注册哪些方法，以及这些方法的使用方式。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>权限和许可证

以下角色可以访问使用情况和见解：

- 报告读者
- 安全读取者
- 安全管理员
- 全局管理员角色

 需要 Azure AD Premium P1 或 P2 许可证才能访问使用情况和见解。 有关 Azure AD 多重身份验证和自助式密码重置 (SSPR) 许可信息，可查看 [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="how-it-works"></a>工作原理

若要访问身份验证方法使用情况和见解：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 单击 " **Azure Active Directory**  >  **安全**  >  **身份验证方法**"  >  **活动**。
1. 报告中有两个选项卡： **注册** 和 **使用**。

   ![身份验证方法活动概述](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>注册详细信息

你可以访问 " [**注册" 选项卡**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) ，以显示能够进行多重身份验证的用户数、passowordless 身份验证和自助密码重置。 

单击 " **支持 Azure 多重身份验证** 的用户"、可 **无密码身份验证** 的用户或 **可以进行自助密码重置** 的用户，或者单击 "insights" 以预筛选用户注册详细信息列表。

- **支持 Azure 多重身份验证的用户** 可以在 Azure AD 中显示能够进行 MFA 的用户细分。 如果用户都注册了强身份验证方法，并由策略启用以使用该方法来执行 MFA，则用户将被视为可以使用。 此数字不反映在 Azure AD 之外注册 MFA 的用户。 
- **能够进行无密码 authentication 的用户** 会显示无需密码即可登录的用户细目。 这包括为 FIDO2、Windows Hello 企业版和无密码手机登录 Microsoft Authenticator 应用注册的用户。 
- **能够进行自助密码重置的用户** 会显示能够进行自助服务密码重置的用户细目。 如果用户都注册了足够的方法来满足组织的 SSPR 策略，并启用了执行 SSPR，则会被视为 SSPR。 

  ![能够注册的用户的屏幕截图](media/how-to-authentication-methods-usage-insights/users-capable.png)

**通过身份验证方法注册的用户** 会显示为每个身份验证方法注册的用户数。 单击 "身份验证方法"，查看为该方法注册的用户。 

![已注册用户的屏幕截图](media/how-to-authentication-methods-usage-insights/users-registered.png)

"**最近通过身份验证的注册" 方法** 显示通过身份验证方法进行的身份验证方法注册失败和失败的次数。 单击身份验证方法可查看该方法的最近注册事件。

![最近注册的屏幕截图](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>使用情况详细信息

**使用情况** 报告显示了用户用来登录和重置其密码的身份验证方法。

!["使用情况" 页的屏幕截图](media/how-to-authentication-methods-usage-insights/usage-page.png)

"**通过身份验证的登录" 要求** 显示在 Azure AD 中执行单因素与多重身份验证所需的成功用户交互式登录数。 这不会反映第三方 MFA 提供程序强制实施 MFA 的登录。

![按身份验证要求登录的屏幕截图](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

"**通过身份验证登录" 方法** 显示使用身份验证方法)  (成功和失败的用户交互式登录数。 它不包括令牌中的声明满足身份验证要求的登录。

![按方法登录的屏幕截图](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**密码重置和帐户解锁的次数** 显示密码更改和密码重置的次数 (自助服务和管理员) 一段时间。

![重置和解锁的屏幕截图](media/how-to-authentication-methods-usage-insights/password-changes.png)

**身份验证方法重置密码** 通过身份验证方法显示密码重置流中的成功和失败的身份验证次数。

![重置方法的屏幕截图](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>用户注册详细信息 

使用列表顶部的控件可以搜索用户，并根据显示的列筛选用户列表。

注册详细信息报告显示每个用户的以下信息：

- 用户主体名称
- 名称
- 支持 MFA 的 (支持，不能) 
- 支持无密码的 (支持，不能) 
- SSPR 注册 (注册，未注册) 
- 启用了 SSPR 的 (，未启用) 
- 支持 SSPR 的 (支持，不能)  
- 注册方法 (电子邮件、移动电话、备用移动电话、办公电话、Microsoft Authenticator 推送、软件一次性密码、FIDO2、安全密钥、安全问题) 

  ![用户注册详细信息的屏幕截图](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>注册和重置事件 

**注册和重置事件** 显示最近24小时、过去7天或过去30天内的注册和重置事件，包括：

- Date
- 用户名
- 用户 
- 功能 (注册，重置) 
-  (应用通知、应用代码、电话呼叫、办公室电话、备用移动电话、短信、电子邮件、安全问题) 使用的方法
- 状态 (成功，失败) 
- 失败的原因 (说明) 

  !["使用情况" 页的屏幕截图](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>限制

临时访问 Pass (点击) 注册不会反映在报表的 "注册" 选项卡中，因为它们仅在短时间内有效。

## <a name="next-steps"></a>后续步骤

- [使用身份验证方法使用情况报告 API](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [为你的组织选择身份验证方法](concept-authentication-methods.md)
- [组合注册体验](concept-registration-mfa-sspr-combined.md)