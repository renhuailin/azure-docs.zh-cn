---
title: 身份验证方法活动 - Azure Active Directory
description: 用户注册的用于登录和重置密码的身份验证方法概述。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/13/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a46b368954102dff2a09635114b5ef9a6a23b710
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687921"
---
# <a name="authentication-methods-activity"></a>身份验证方法活动 

使用新的“身份验证方法活动”仪表板，管理员可以监视整个组织中的身份验证方法注册和使用情况。 此报告功能可让组织了解正在注册哪些方法，以及这些方法的使用方式。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>权限和许可证

具有以下权限的内置角色和自定义角色可以访问“身份验证方法活动”边栏选项卡和 API：

- Microsoft.directory/auditLogs/allProperties/read
- Microsoft.directory/signInReports/allProperties/read

以下角色具有所需的权限：

- 报告读者
- 安全读取者
- 全局读取者
- 安全操作员
- 安全管理员
- 全局管理员角色

 需要 Azure AD Premium P1 或 P2 许可证才能访问使用情况和见解。 有关 Azure AD 多重身份验证和自助式密码重置 (SSPR) 许可信息，可查看 [Azure Active Directory 定价站点](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。

## <a name="how-it-works"></a>工作原理

若要访问身份验证方法使用情况和见解：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 单击“Azure Active Directory” > “安全性” > “身份验证方法” > “活动”。
1. 报告中有两个选项卡：“注册”和“使用情况”。

   ![身份验证方法活动概述](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>注册详细信息

你可以访问[“注册”选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)，该选项卡显示能够进行多重身份验证、无密码身份验证和自助式密码重置的用户数。 

单击以下任一选项以预筛选用户注册详细信息列表：

- “可以进行 Azure 多重身份验证的用户”显示了满足以下两个条件的用户的明细：
  - 已注册强身份验证方法 
  - 策略允许其使用该方法进行 MFA 
  
  此数字不反映已在 Azure AD 之外注册 MFA 的用户。 
- “可以使用无密码身份验证的用户”显示已在 Microsoft Authenticator 应用中使用 FIDO2、Windows Hello 企业版或无密码手机登录注册进行无密码登录的用户的明细。 
- “可以进行自助式密码重置的用户”显示可重置其密码的用户的明细。 同时满足以下两个条件的用户可重置其密码：
  - 已注册了足够的方法来满足其组织的自助式密码重置策略 
  - 获允重置其密码 

  ![可以注册的用户的屏幕截图](media/how-to-authentication-methods-usage-insights/users-capable.png)

“已注册的用户(按身份验证方法)”显示每种身份验证方法注册的用户数。 单击身份验证方法可查看针对该方法注册的用户。

![已注册用户的屏幕截图](media/how-to-authentication-methods-usage-insights/users-registered.png)

“最近注册(按身份验证方法)”显示按身份验证方法分类的成功注册数和失败注册数。 单击身份验证方法可查看该方法最近的注册事件。

![屏幕截图，显示最近的注册数](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>使用情况详细信息

“使用情况”报告显示了用于登录和重置密码的身份验证方法。

![“使用情况”页的屏幕截图](media/how-to-authentication-methods-usage-insights/usage-page.png)

“登录次数(按身份验证要求)”显示 Azure AD 中单因素身份验证与多重身份验证所需的成功的用户交互登录次数。 不包括第三方 MFA 提供商强制执行 MFA 的登录。

![“登录次数(按身份验证要求)”的屏幕截图](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

“登录次数(按身份验证方法)”显示所用身份验证方法的用户交互登录次数（成功和失败）。 它不包括使用令牌中的声明来满足身份验证要求的登录。

![登录次数（按方法）的屏幕截图](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

“密码重置和帐户解锁次数”显示一段时间内成功更改密码和重置密码（自助方式和通过管理员进行的方式）的次数。

![屏幕截图，显示了重置和解锁](media/how-to-authentication-methods-usage-insights/password-changes.png)

“密码重置次数(按身份验证方法)”按身份验证方法显示在密码重置流程中成功和失败的身份验证次数。

![屏幕截图，显示了按方法进行的重置](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>用户注册详细信息 

使用列表顶部的控件可以搜索用户，并根据显示的列筛选用户列表。

注册详细信息报告显示每个用户的以下信息：

- 用户主体名称
- 名称
- 支持 MFA（支持、不支持）
- 支持无密码（支持、不支持）
- 已注册 SSPR（已注册、未注册）
- 已启用 SSPR（已启用、未启用）
- 支持 SSPR（支持、不支持） 
- 注册方法（电子邮件、移动电话、备用移动电话、办公电话、Microsoft Authenticator 推送、软件一次性密码、FIDO2、安全密钥、安全问题）

  ![用户注册详细信息的屏幕截图](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>注册和重置事件 

“注册和重置事件”显示过去 24 小时、过去 7 天或过去 30 天内的注册和重置事件，包括：

- 日期
- 用户名
- 用户 
- 功能（注册、重置）
- 所用方法（应用通知、应用代码、电话呼叫、办公室电话、备用移动电话、短信、电子邮件、安全问题）
- 状态（成功、失败）
- 失败原因（说明）

  ![注册和重置事件的屏幕截图](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>限制

- 报告中的数据不会实时更新，可能会反映长达数小时的延迟。
- “临时访问密码”注册不会反映在报告的注册选项卡中，因为它们只在短时间内有效。
- 用户已配置好的 PhoneAppNotification 或 PhoneAppOTP 方法不会显示在仪表板中。 

## <a name="next-steps"></a>后续步骤

- [使用身份验证方法使用情况报告 API](/graph/api/resources/authenticationmethods-usage-insights-overview)
- [为你的组织选择身份验证方法](concept-authentication-methods.md)
- [合并注册体验](concept-registration-mfa-sspr-combined.md)
