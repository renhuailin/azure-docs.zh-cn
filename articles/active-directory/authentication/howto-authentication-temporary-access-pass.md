---
title: 在 Azure AD 中配置临时访问密码，以注册无密码身份验证方法
description: 了解如何配置和允许用户使用临时访问密码来注册无密码身份验证方法
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/23/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8b0279e0f97f3440bdef04046c2cb6eb35b6573
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605784"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>在 Azure AD 中配置临时访问密码，以注册无密码身份验证方法（预览版）

利用无密码身份验证方法（例如 FIDO2，以及通过 Microsoft Authenticator 应用进行无密码手机登录），用户无需密码即可安全登录。 用户可以通过以下两种方式之一来启动无密码方法：

- 使用现有的 Azure AD 多重身份验证方法 
- 使用临时访问密码 (TAP) 

临时访问密码是由管理员颁发的一种有时间限制的密码，可满足强身份验证要求，并可用于载入其他身份验证方法（包括无密码方法）。 当用户丢失或忘记自己的强身份验证因素（如 FIDO2 安全密钥或 Microsoft Authenticator 应用），但需要登录以注册新的强身份验证方法时，也可以使用临时访问密码，更轻松地进行恢复。

本文介绍了如何通过 Azure 门户在 Azure AD 中启用和使用临时访问密码。 还可以使用 REST API 来执行这些操作。 

>[!NOTE]
>临时访问密码目前提供公共预览版。 某些功能可能不受支持，或者有一些功能受限。 

## <a name="enable-the-temporary-access-pass-policy"></a>启用临时访问密码策略

临时访问密码策略中定义了多项设置，例如租户中创建密码的生存期，或者可以使用临时访问密码进行登录的用户和组。 在任何人可以使用临时访问密码进行登录之前，需要先启用身份验证方法策略，并选择可以使用临时访问密码进行登录的用户和组。
尽管可以为任何用户创建临时访问密码，但只有策略中包含的用户才能使用该密码进行登录。

全局管理员和身份验证方法策略管理员角色持有者可以更新临时访问密码身份验证方法策略。
若要配置临时访问密码身份验证方法策略，请执行以下操作：

1. 以全局管理员身份登录到 Azure 门户，然后单击“Azure Active Directory” > “安全” > “身份验证方法” > “临时访问密码”。
1. 单击“是”以启用策略，选择要对其应用策略的用户以及任何“常规”设置。

   ![显示了如何启用临时访问密码身份验证方法策略的屏幕截图](./media/how-to-authentication-temporary-access-pass/policy.png)

   下表介绍了默认值以及允许值的范围。


   | 设置 | 默认值 | 允许的值 | 注释 |
   |---|---|---|---|
   | 最短生存期 | 1 小时 | 10 – 43200 分钟（30 天） | 临时访问密码保持有效的最短分钟数。 |
   | 最长生存期 | 24 小时 | 10 – 43200 分钟（30 天） | 临时访问密码保持有效的最长分钟数。 |
   | 默认生存期 | 1 小时 | 10 – 43200 分钟（30 天） | 默认值可以替代为各个密码，但必须是介于策略所配置的最短和最长生存期之间。 |
   | 一次性使用 | False | True/False | 当策略设置为“False”时，租户中的密码在其有效期（最长生存期）内可以使用一次或多次。 通过在临时访问密码策略中强制要求一次性使用，在租户中创建的所有密码都将创建为一次性使用。 |
   | Length | 8 | 8-48 个字符 | 定义密码的长度。 |

## <a name="create-a-temporary-access-pass"></a>创建临时访问密码

启用策略后，可以在 Azure AD 中为用户创建临时访问密码。 这些角色可以执行下列与临时访问密码有关的操作。

- 全局管理员可以创建、删除、查看任何用户（除了自己）的临时访问密码
- 特权身份验证管理员可以创建、删除、查看管理员和成员（除了自己）的临时访问密码
- 身份验证管理员可以创建、删除、查看成员（除了自己）的临时访问密码
- 全局管理员可以查看用户的临时访问密码详细信息（但不能读取代码本身）。

1. 以全局管理员、特权身份验证管理员或身份验证管理员身份登录 Azure 门户。 
1. 单击“Azure Active Directory”，浏览到“用户”并选择一个用户（例如“Chris Green”），然后选择“身份验证方法”。
1. 如果需要，请选择相应的选项以“尝试新的用户身份验证方法体验”。
1. 选择“添加身份验证方法”选项。
1. 在“选择方法”下，单击“临时访问密码(预览版)”。
1. 定义一个自定义激活时间或持续时间，然后单击“添加”。

   ![显示了如何创建临时访问密码的屏幕截图](./media/how-to-authentication-temporary-access-pass/create.png)

1. 添加后，将显示临时访问密码的详细信息。 记下临时访问密码的实际值。 将此值提供给用户。 单击“确定”后，将无法查看此值。
   
   ![显示了临时访问密码详细信息的屏幕截图](./media/how-to-authentication-temporary-access-pass/details.png)

以下命令显示如何使用 PowerShell 创建和获取临时访问密码：

```powershell
# Create a Temporary Access Pass for a user
$properties = @{}
$properties.isUsableOnce = $True
$properties.startDateTime = '2021-03-11 06:00:00'
$propertiesJSON = $properties | ConvertTo-Json

New-MgUserAuthenticationTemporaryAccessPassMethod -UserId user2@contoso.com -BodyParameter $propertiesJSON

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM TAPRocks!

# Get a user's Temporary Access Pass
Get-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM

```

## <a name="use-a-temporary-access-pass"></a>使用临时访问密码

临时访问密码的最常见用途是让用户在第一次登录期间注册身份验证详细信息，而无需完成额外的安全提示。 身份验证方法在 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 上注册。 用户还可以在此处更新现有的身份验证方法。

1. 打开 Web 浏览器，访问 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)。
1. 输入为其创建了临时访问密码的帐户所对应的 UPN，例如 *tapuser@contoso.com* 。
1. 如果用户已包含在临时访问密码策略中，则屏幕中将提示输入其临时访问密码。
1. 输入 Azure 门户中显示的临时访问密码。

   ![显示了如何输入临时访问密码的屏幕截图](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>对于联合域，优先使用临时访问密码而非联合身份验证。 拥有临时访问密码的用户将在 Azure AD 中完成身份验证，并且不会重定向到联合标识提供程序 (IdP)。

用户现已登录，可以更新或注册方法，例如 FIDO2 安全密钥。 如果是因丢失凭据或设备而要更新其身份验证方法，用户应确保删除原先的身份验证方法。
用户还可以使用其密码继续登录；“点击”操作不会替换用户的密码。

### <a name="passwordless-phone-sign-in"></a>无密码的手机登录

用户还可以使用自己的临时访问密码，直接从 Authenticator 应用中注册无密码手机登录。 有关详细信息，请参阅[将工作或学校帐户添加到 Microsoft Authenticator 应用](https://support.microsoft.com/account-billing/add-your-work-or-school-account-to-the-microsoft-authenticator-app-43a73ab5-b4e8-446d-9e54-2a4cb8e4e93c)。

![显示了如何使用工作或学校帐户输入临时访问密码的屏幕截图](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

### <a name="guest-access"></a>来宾访问

如果临时访问密码满足主租户身份验证要求，则来宾用户可以使用其主租户颁发的临时访问密码登录到资源租户。 如果资源租户需要 MFA，来宾用户需要执行 MFA 才能访问资源。

### <a name="expiration"></a>过期时间

过期或删除的临时访问密码不能用于交互式或非交互式身份验证。 临时访问密码过期或删除后，用户需要使用不同的身份验证方法重新进行身份验证。 

## <a name="delete-an-expired-temporary-access-pass"></a>删除过期的临时访问密码

在用户对应的“身份验证方法”下，“详细信息”列中显示了临时访问密码的过期时间。 可以使用以下步骤，删除已过期的临时访问密码：

1. 在 Azure AD 门户中，浏览到“用户”并选择一个用户（例如“Tap User”），然后选择“身份验证方法”。
1. 在列表中显示的“临时访问密码(预览版)”身份验证方法的右侧，选择“删除”。

你也可以使用 PowerShell：

```powershell
# Remove a user's Temporary Access Pass
Remove-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com -TemporaryAccessPassAuthenticationMethodId c5dbd20a-8b8f-4791-a23f-488fcbde3b38
```

## <a name="replace-a-temporary-access-pass"></a>替换临时访问密码 

- 每个用户只能有一个临时访问密码。 在临时访问密码的开始和结束时间内，可以使用密码。
- 如果用户需要新的临时访问密码，请执行以下操作：
  - 如果现有的临时访问密码尚在有效期内，则管理员需要删除现有的临时访问密码，然后为该用户创建新的密码。 
  - 如果现有的临时访问密码已过期，则新的临时访问密码将替代现有的临时访问密码。

若要详细了解有关载入和恢复的 NIST 标准，请参阅 [NIST 特别发布 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4)。

## <a name="limitations"></a>限制

请牢记这些限制：

- 使用一次性临时访问密码来注册无密码方法（如 FIDO2 或手机登录）时，用户必须在登录 10 分钟内使用一次性临时访问密码完成注册。 此限制不适用于可多次使用的临时访问密码。
- 临时访问密码为公共预览版，目前不可用于 Azure 美国政府版。
- 自助式密码重置 (SSPR) 注册策略或[标识保护多重身份验证注册策略](../identity-protection/howto-identity-protection-configure-mfa-policy.md)范围内的用户在使用临时访问密码登录后，需要注册身份验证方法。 这些策略范围内的用户将重定向到[合并注册的中断模式](concept-registration-mfa-sspr-combined.md#combined-registration-modes)。 此体验当前不支持 FIDO2 和手机登录注册。 
- 临时访问密码不能与网络策略服务器 (NPS) 扩展、Active Directory 联合身份验证服务 (AD FS) 适配器或 Windows 安装/全新体验 (OOBE) 及 Autopilot 一起使用，或用于部署 Windows Hello 企业版。 
- 如果在租户上启用了无缝 SSO，则用户将收到输入密码的提示。 用户可单击“改用临时访问密码”链接，使用临时访问密码进行登录。

  ![显示了改用临时访问密码的屏幕截图](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>疑难解答    

- 如果用户在登录过程中未收到临时访问密码，请检查以下各项：
  - 用户是否处于临时访问密码身份验证方法策略的范围内。
  - 用户是否拥有尚在有效期内的临时访问密码，如果是一次性使用，是否尚未使用。
- 如果在使用临时访问密码进行登录的过程中，出现了“由于用户凭据策略原因，临时访问密码登录已被阻止”提示，请检查以下各项：
  - 用户是否拥有可多次使用的临时访问密码，而身份验证方法策略要求的是一次性临时访问密码。
  - 一次性临时访问密码已被使用。
- 如果因用户凭据策略而导致临时访问密码登录受阻，请检查该用户是否位于 TAP 策略的范围内。

## <a name="next-steps"></a>后续步骤

- [在 Azure Active Directory 中规划无密码身份验证部署](howto-authentication-passwordless-deployment.md)