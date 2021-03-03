---
title: 身份验证方法活动-Azure Active Directory
description: 概述用户注册以登录和重置密码的身份验证方法。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/25/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe69967d84777091aec0bbbf1626b98f5018d0e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693020"
---
# <a name="authentication-methods-activity"></a>身份验证方法活动 

使用新的 "身份验证方法" 活动面板，管理员可以监视其组织内的身份验证方法注册和使用情况。 此报告功能为你的组织提供了一种方法，用于了解正在注册哪些方法及其使用方式。

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

单击下列任一选项，预筛选用户注册详细信息列表：

- **支持 Azure 多重身份验证的用户** 显示了两个用户的细目分类：
  - 已注册强身份验证方法 
  - 已通过策略启用以使用该方法进行 MFA 
  
  此数字不反映在 Azure AD 之外为 MFA 注册的用户。 
- **无密码 authentication 的用户** 可通过使用 FIDO2、Windows Hello 企业版或无密码手机登录到 Microsoft Authenticator 应用，显示注册为无密码登录的用户的细分。 
- **能够进行自助密码重置的用户** 会显示可重置其密码的用户的细分。 用户可以重置其密码（如果它们都是这样）：
  - 注册为满足其组织的自助密码重置策略的足够方法 
  - 已启用以重置其密码 

  ![可以注册的用户的屏幕截图](media/how-to-authentication-methods-usage-insights/users-capable.png)

**通过身份验证方法注册的用户** 会显示为每个身份验证方法注册的用户数。 单击 "身份验证方法" 以查看为该方法注册的用户。

![已注册用户的屏幕截图](media/how-to-authentication-methods-usage-insights/users-registered.png)

**最近注册的身份验证方法** 显示成功和失败的注册数量，按身份验证方法排序。 单击身份验证方法可查看该方法的最近注册事件。

![最近注册的屏幕截图](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>使用情况详细信息

" **使用情况** " 报告显示了用于登录和重置密码的身份验证方法。

!["使用情况" 页的屏幕截图](media/how-to-authentication-methods-usage-insights/usage-page.png)

"**通过身份验证的登录" 要求** 显示 Azure AD 中单因素与多重身份验证所需的成功用户交互式登录数。 不包含第三方 MFA 提供程序强制执行 MFA 的登录。

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
