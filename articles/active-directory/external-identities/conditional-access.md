---
title: B2B 协作用户的条件访问 - Azure AD
description: 了解如何对 Azure Active Directory B2B 用户强制实施多重身份验证策略。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c6611b7437bdaf873caaaf4722e30fb644b5f13
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110617236"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B 协作用户的条件访问

本文介绍组织如何为 B2B 来宾用户界定条件访问 (CA) 策略的范围以访问其资源。
>[!NOTE]
>与该标识提供者 (IdP) 的现有用户的身份验证或授权流相比，来宾用户的此身份验证或授权流稍有不同。

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>来自外部目录的 B2B 来宾用户的身份验证流

下图说明了流：![图像显示来自外部目录的 B2B 来宾用户的身份验证流](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| 步骤 | 说明 |
|--------------|-----------------------|
| 1. | B2B 来宾用户请求对资源的访问。 资源将用户重定向到其资源租户，即受信任的 IdP。|
| 2. | 资源租户将用户标识为外部用户，并将用户重定向到 B2B 来宾用户的 IdP。 用户在 IdP 中执行主要身份验证。
| 3. | B2B 来宾用户的 IdP 向用户颁发令牌。 用户被重定向回具有令牌的资源租户。 资源租户验证令牌，然后根据其 CA 策略评估用户。 例如，资源租户可能要求用户执行 Azure Active Directory (AD) 多重身份验证。
| 4. | 满足所有资源租户 CA 策略后，资源租户将颁发自己的令牌，并将用户重定向到其资源。

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>适用于 B2B 来宾用户的一次性密码身份验证流

下图说明了该流：![图像显示适用于 B2B 来宾用户的一次性密码身份验证流](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| 步骤 | 说明 |
|--------------|-----------------------|
| 1. |用户请求访问另一个租户中的资源。 资源将用户重定向到其资源租户，即受信任的 IdP。|
| 2. | 资源租户将用户标识为[外部电子邮件一次性密码 (OTP) 用户](./one-time-passcode.md)，并向用户发送包含 OTP 的电子邮件。|
| 3. | 用户检索 OTP 并提交代码。 资源租户根据其 CA 策略评估用户。
| 4. | 满足所有 CA 策略后，资源租户会颁发一个令牌，并将用户重定向到其资源。 |

>[!NOTE]
>如果用户来自外部资源租户，则不可能同时评估 B2B 来宾用户的 IdP CA 策略。 目前，只有资源租户的 CA 策略适用于其来宾。

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>针对 B2B 用户的 Azure AD 多重身份验证

组织可以对其 B2B 来宾用户执行多个 Azure AD 多重身份验证策略。 可以在租户、应用或个人用户级别强制实施这些策略，这与针对全职员工和组织成员启用这些策略的方式相同。
资源租户始终负责用户的 Azure AD 多重身份验证，即使来宾用户的组织具有多重身份验证功能也是如此。 下面举例进行说明：

1. 一家名为 Fabrikam 的公司中的管理员或信息工作者邀请来自另一家名为 Contoso 的公司的用户使用他们的应用程序 Woodgrove。

2. Fabrikam 的 Woodgrove 应用程序被配置为需要 Azure AD 多重身份验证访问。

3. 当来自 Contoso 的 B2B 来宾用户尝试访问 Fabrikam 租户中的 Woodgrove 时，系统要求他们完成 Azure AD 多重身份验证质询。

4. 然后，来宾用户可以使用 Fabrikam 设置 Azure AD 多重身份验证，并选择选项。

5. 此方案适用于任何标识（Azure AD 或 Microsoft 个人帐户 (MSA)）。 例如，Contoso 中的用户可以使用社交 ID 进行身份验证。

6. Fabrikam 必须提供足够的高级 Azure AD 许可证来支持 Azure AD 多重身份验证。 然后来自 Contoso 的用户才能使用 Fabrikam 提供的此许可证。 有关 B2B 许可的信息，请参阅 [Azure AD 外部标识的计费模型](./external-identities-pricing.md)。

>[!NOTE]
>Azure AD 多重身份验证在资源租户上完成，以确保可预测性。 当来宾用户登录时，他们会看到资源租户的登录页面显示在后台，而其主租户登录页面和公司徽标则显示在前台。

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>针对 B2B 用户设置 Azure AD 多重身份验证

若要为 B2B 协作用户设置 Azure AD 多重身份验证，请观看以下视频：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>针对兑换产品时要求的 B2B 用户 Azure AD 多重身份验证

若要详细了解兑换产品时的 Azure AD 多重身份验证体验，请观看以下视频：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>针对 B2B 用户的 Azure AD 多重身份验证重置

现在，可以使用以下 PowerShell cmdlet 来证明 B2B 来宾用户：

1. 连接到 Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. 使用身份验证方法获取所有用户

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   以下是示例：

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. 重置特定用户的 Azure AD 多重身份验证方法，以要求该 B2B 协作用户再次设置身份验证方法。 
   以下是示例：

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>针对 B2B 用户的条件访问

有各种影响 B2B 来宾用户的 CA 策略的因素。

### <a name="device-based-conditional-access"></a>基于设备的条件访问

在 CA 中，有一个要求用户的[设备合规或已加入混合 Azure AD](../conditional-access/concept-conditional-access-conditions.md#device-state-preview) 的选项。 B2B 来宾用户只有在资源租户能够管理其设备时才能满足合规性。 设备不能一次由多个组织管理。 B2B 来宾用户无法满足混合 Azure AD 加入，因为他们没有本地 AD 帐户。 仅当来宾用户的设备不受管理时，来宾用户才能在资源租户中注册其设备，然后使设备合规。 然后，用户可以满足授权控制要求。

>[!Note]
>不建议对外部用户要求使用受管理设备。

### <a name="mobile-application-management-policies"></a>移动应用程序管理策略

CA 授权控制（如需要已批准的客户端应用和需要应用保护策略）需要在租户中注册设备 。 这些控制仅应用于 [iOS 和 Android 设备](../conditional-access/concept-conditional-access-conditions.md#device-platforms)。 但是，如果用户的设备已经由其他组织管理，则这两种控制都不能应用于 B2B 来宾用户。 移动设备不能一次在多个租户中注册。 如果移动设备由其他组织管理，则用户将被阻止。 仅当来宾用户的设备不受管理时，来宾用户才能在资源租户中注册其设备。 然后，用户可以满足授权控制要求。  

>[!NOTE]
>不建议对外部用户要求应用保护策略。

### <a name="location-based-conditional-access"></a>基于位置的条件访问

如果邀请组织能够创建定义其合作伙伴组织的受信任 IP 地址范围，则可以执行基于 IP 范围的[基于位置的策略](../conditional-access/concept-conditional-access-conditions.md#locations)。

还可以基于地理位置执行策略。

### <a name="risk-based-conditional-access"></a>基于风险的条件访问

如果 B2B 来宾用户满足授权控制要求，则执行[登录风险策略](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)。 例如，组织可能需要 Azure AD 多重身份验证，以承担中等或高登录风险。 但是，如果某个用户以前没有在资源租户中注册过 Azure AD 多重身份验证，则该用户将被阻止。 这样做是为了防止恶意用户在泄露合法用户密码时注册其自己的 Azure AD 多重身份验证凭据。

但无法在资源租户中解决[用户风险策略](../conditional-access/concept-conditional-access-conditions.md#user-risk)。 例如，如果你需要为高风险来宾用户更改密码，则由于无法重置资源目录中的密码，他们将被阻止。

### <a name="conditional-access-client-apps-condition"></a>条件访问客户端应用条件

针对 B2B 来宾用户的[客户端应用条件](../conditional-access/concept-conditional-access-conditions.md#client-apps)的行为与针对其他类型用户的客户端应用条件的行为是一样的。 例如，你可以阻止来宾用户使用旧版身份验证协议。

### <a name="conditional-access-session-controls"></a>条件访问会话控制

针对 B2B 来宾用户的[会话控制](../conditional-access/concept-conditional-access-session.md)的行为与针对其他类型用户的会话控制的行为是一样的。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](./what-is-b2b.md)
- [标识保护和 B2B 用户](../identity-protection/concept-identity-protection-b2b.md)
- [外部标识定价](https://azure.microsoft.com/pricing/details/active-directory/)
- [常见问题 (FAQ)](./faq.yml)
