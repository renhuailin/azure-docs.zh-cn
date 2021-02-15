---
title: 在 Azure AD 中配置临时访问 Pass 以注册无密码 authentication 方法
description: '了解如何配置和允许用户通过使用临时访问 Pass (来注册无密码 authentication 方法) '
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: justinha
author: inbarckms
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d45119fa86ab47e6a625c628d8cb9763db83bd
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520883"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>在 Azure AD 中配置临时访问传递 (预览) 注册无密码身份验证方法

无密码身份验证方法（如 FIDO2 和无密码）通过 Microsoft Authenticator 应用登录，使用户无需密码即可安全登录。 用户可以通过以下两种方式之一来启动无密码方法：

- 使用现有 Azure AD 多重身份验证方法 
- 使用临时访问 Pass (点击)  

点击是由管理人员颁发的、可满足强身份验证要求并可用于载入其他身份验证方法（包括无密码）的时间限制的密码。 当用户丢失或忘记其强身份验证因素（如 FIDO2 安全密钥或 Microsoft Authenticator 应用），但需要登录来注册新的强身份验证方法时，点击还可以更轻松地进行恢复。


本文说明如何使用 Azure 门户在 Azure AD 中启用和使用点击。 你还可以使用 REST Api 执行这些操作。 

>[!NOTE]
>暂时访问 Pass 目前为公共预览版。 某些功能可能不受支持或功能有限。 

## <a name="enable-the-tap-policy"></a>启用点击策略

点击策略定义设置，如在租户中创建的传递的生存期，或者可以使用点击登录的用户和组。 在任何人都可以通过点击登录之前，你需要启用身份验证方法策略，并使用点击来选择可以登录的用户和组。
尽管可以为任何用户创建点击点，但只有包含在策略中的人员才能使用它进行登录。

全局管理员和身份验证方法策略管理员角色持有者可以更新分流身份验证方法策略。
若要配置点击身份验证方法策略：

1. 以全局管理员身份登录到 Azure 门户，然后单击 **Azure Active Directory**  >  **安全**  >  **身份验证方法** 的  >  **临时访问权限**。
1. 单击 **"是"** 以启用策略，选择应用策略的用户以及任何 **常规** 设置。

   ![如何启用点击身份验证方法策略的屏幕截图](./media/how-to-authentication-temporary-access-pass/policy.png)

   下表介绍了默认值和允许值的范围。


   | 设置          | 默认值 | 允许的值               | 注释                                                                                                                                                                                                                                                                 |   |
   |------------------|----------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    最小生存期 | 1 小时         | 10–43200分钟 (30 天)  | 点击有效的最小分钟数。                                                                                                                                                                                                                         |   |
   | 最长生存期 | 24 小时       | 10–43200分钟 (30 天)  | 点击有效的最大分钟数。                                                                                                                                                                                                                         |   |
   | 默认生存期 | 1 小时         | 10–43200分钟 (30 天)  | 默认值可以在策略所配置的最小和最大生存期内由各个传递覆盖                                                                                                                                                |   |
   | 一次性使用     | 错误          | True/False                 | 如果将策略设置为 "false"，则可以在租户有效期内使用一次或多次（最长 (生存期) ）。 通过在点击策略中强制使用一次性，在租户中创建的所有传递都将创建为一次性使用。 |   |
   | 长度           | 8              | 8-48 个字符              | 定义密码的长度。                                                                                                                                                                                                                                      |   |

## <a name="create-a-tap-in-the-azure-ad-portal"></a>在 Azure AD 门户中创建点击

启用点击策略后，可以在 Azure AD 中为用户创建点击。 这些角色可以执行以下与点击相关的操作。

- 全局管理员可以创建、删除、查看任何用户 (除了自身) 
- 特权身份验证管理员可以创建、删除、查看管理员和成员 (除了自身) 
- 身份验证管理员可以创建、删除、查看成员 (（其本身除外）) 
- 全局管理员可以在用户 (上查看点击详细信息，而无需) 读取代码本身。

若要创建点击：

1. 以 "全局管理员"、"特权身份验证管理员" 或 "身份验证管理员" 身份登录到门户。 
1. 单击 " **Azure Active Directory**"，浏览到 "用户"，选择一个用户（如 *Chris 绿色*），然后选择 " **身份验证方法**"。
1. 如果需要，请选择相应的选项以 **尝试新的用户身份验证方法**。
1. 选择用于 **添加身份验证方法** 的选项。
1. 在 " **选择方法**" 下，单击 " **临时访问传递 (预览")**。
1. 定义自定义激活时间或持续时间，并单击 " **添加**"。

   ![如何创建点击的屏幕截图](./media/how-to-authentication-temporary-access-pass/create.png)

1. 添加后，将显示点击的详细信息。 记下实际的点击值。 你向用户提供此值。 单击 **"确定"** 后将无法查看此值。
   
   ![点击详细信息的屏幕截图](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-tap"></a>使用分流

点击最常见的用途是让用户在第一次登录期间注册身份验证详细信息，而无需完成额外的安全提示。 身份验证方法在上注册 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 。 用户还可以在此处更新现有的身份验证方法。

1. 在 web 浏览器中打开 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 。
1. 输入为点击创建的帐户的 UPN，如 *tapuser@contoso.com* 。
1. 如果用户已包含在点击策略中，则他们将看到一个屏幕来输入其点击。
1. 输入 Azure 门户中显示的点击。

   ![如何进入点击的屏幕截图](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>对于联合域，点击优先于联合。 具有点击的用户将在 Azure AD 中完成身份验证，并且不会重定向到联合标识提供程序 (IdP) 。

用户现已登录，可以更新或注册方法，如 FIDO2 安全密钥。 因丢失凭据或设备而更新其身份验证方法的用户应确保它们删除旧的身份验证方法。

用户还可以使用其点击注册直接从验证器应用进行无密码手机登录。 有关详细信息，请参阅 [将工作或学校帐户添加到 Microsoft Authenticator 应用](../user-help/user-help-auth-app-add-work-school-account.md)。

![如何使用工作或学校帐户进入点击的屏幕截图](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-tap"></a>删除点击

无法使用过期的点击。 在用户的 **身份验证方法** 下，" **详细信息** " 列会显示点击已过期的时间。 您可以使用以下步骤删除这些过期的点击：

1. 在 Azure AD 门户中，浏览到 " **用户**"，选择一个用户（如 *点击用户*），然后选择 " **身份验证方法**"。
1. 在 " **临时访问 Pass" (预览 ")** 身份验证方法" 列表中，选择 " **删除**"。

## <a name="replace-a-tap"></a>替换分流 

- 用户只能点击一次。 密码可以在点击的开始和结束时间使用。
- 如果用户需要新点击：
  - 如果现有的点击是有效的，则管理员需要删除现有的点击，并为用户创建新的 pass。 删除有效的点击会吊销用户会话。 
  - 如果现有点击已过期，则新的点击将覆盖现有的点击，并且不会撤销用户会话。

有关用于载入和恢复的 NIST 标准的详细信息，请参阅 [Nist 特殊发布 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4)。

## <a name="limitations"></a>限制

请记住以下限制：

- 当使用一次点击来注册无密码方法（如 FIDO2 或手机登录）时，用户必须在10分钟内完成注册，只需一次点击即可完成。 此限制不适用于可多次使用的点击。
- 来宾用户无法通过点击登录。
- 在登录后，将需要在自助服务密码重置范围内的用户 (SSPR) 注册策略注册一个 SSPR 方法。 如果用户只是要使用 FIDO2 密钥，则从 SSPR 策略中排除这些密钥，或者禁用 SSPR 注册策略。 
- 点击不能与网络策略服务器 (NPS) 扩展和 Active Directory 联合身份验证服务 (AD FS) 适配器一起使用。
- 如果在租户上启用了无缝 SSO，则会提示用户输入密码。 " **使用临时访问 Pass** " 链接将可供用户使用点击来登录。

![改用点击的屏幕截图](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>疑难解答    

- 如果在登录过程中未向用户提供点击，请检查以下各项：
  - 用户处于点击身份验证方法策略的范围内。
  - 用户具有有效的点击点，并且如果它是一次性使用，则尚未使用。
- 如果在通过点击登录期间出现 " **用户凭据策略已阻止" 的临时访问权限登录** ，请检查以下各项：
  - 用户可以使用多点点击，同时身份验证方法策略需要单击一次。
  - 已使用一次点击。

## <a name="next-steps"></a>后续步骤

- [在 Azure Active Directory 中规划无密码 authentication 部署](howto-authentication-passwordless-deployment.md)

