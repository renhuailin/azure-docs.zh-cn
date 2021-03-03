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
ms.openlocfilehash: 74bfa4987f584bbd3490bc5f4f187dee5bc1bd87
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646276"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B 协作用户的条件访问

本文介绍了组织如何) B2B 来宾用户访问其资源的条件性访问 (CA 的策略。
>[!NOTE]
>对于来宾用户而言，此身份验证或授权流与该标识提供者的现有用户的身份验证或授权流不同 (IdP) 。

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>来自外部目录的 B2B 来宾用户的身份验证流

下图说明了流： ![ 图像显示外部目录中 B2B 来宾用户的身份验证流](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| 步骤 | 说明 |
|--------------|-----------------------|
| 1. | B2B 来宾用户请求对资源的访问权限。 资源会将用户重定向到其资源租户，即受信任的 IdP。|
| 2. | 资源租户将用户标识为外部用户，并将用户重定向到 B2B 来宾用户的 IdP。 用户在 IdP 中执行主要身份验证。
| 3. | B2B 来宾用户的 IdP 向用户颁发令牌。 用户被重定向回带有令牌的资源租户。 资源租户验证令牌，然后根据其 CA 策略评估用户。 例如，资源租户可能要求用户执行 (AD) 多重身份验证 Azure Active Directory。
| 4. | 满足所有资源租户 CA 策略后，资源租户会颁发自己的令牌，并将用户重定向到其资源。

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>具有一次性密码的 B2B 来宾用户的身份验证流

下图说明了流： ![ 图像显示具有一次性密码的 B2B 来宾用户的身份验证流](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| 步骤 | 说明 |
|--------------|-----------------------|
| 1. |用户请求访问另一个租户中的资源。 资源会将用户重定向到其资源租户，即受信任的 IdP。|
| 2. | 资源租户将用户标识为 [ (OTP) 用户的外部电子邮件一次性密码](./one-time-passcode.md) ，并向用户发送包含 otp 的电子邮件。|
| 3. | 用户检索 OTP 并提交代码。 资源租户根据其 CA 策略评估用户。
| 4. | 满足所有 CA 策略后，资源租户会颁发一个令牌，并将用户重定向到其资源。 |

>[!NOTE]
>如果用户来自外部资源租户，则不可能对 B2B 来宾用户的 IdP CA 策略进行评估。 目前，只有资源租户的 CA 策略适用于其来宾。

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>针对 B2B 用户 Azure AD 多重身份验证

组织可以为其 B2B 来宾用户强制实施多个 Azure AD 多重身份验证策略。 可以在租户、应用或个人用户级别强制实施这些策略，其方式与针对全职员工和组织成员启用这些策略的方式相同。
资源租户始终负责为用户 Azure AD 多重身份验证，即使来宾用户的组织具有多重身份验证功能。 下面是一个示例-

1. 名为 Contoso 的公司中名为 Fabrikam 邀请用户的公司中的管理员或信息工作者使用其应用程序 Woodgrove。

2. Fabrikam 中的 Woodgrove 应用配置为要求对访问 Azure AD 多重身份验证。

3. 当 Contoso 的 B2B 来宾用户尝试访问 Fabrikam 租户中的 Woodgrove 时，系统将要求他们完成 Azure AD 多重身份验证质询。

4. 然后，来宾用户可以通过 Fabrikam 设置其 Azure AD 多重身份验证，并选择相应的选项。

5. 此方案适用于任何标识（Azure AD 或个人 Microsoft 帐户 (MSA) 。 例如，如果 Contoso 中的用户使用社交 ID 进行身份验证。

6. Fabrikam 必须具有足够的高级 Azure AD 许可证，支持 Azure AD 多重身份验证。 然后从 Contoso 使用此许可证。 有关 B2B 许可的信息，请参阅 [计费模型 Azure AD 外部标识](./external-identities-pricing.md) 。

>[!NOTE]
>Azure AD 多重身份验证是在资源租户上完成的，以确保可预测性。

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>设置 B2B 用户的 Azure AD 多重身份验证

若要为 B2B 协作用户设置 Azure AD 多重身份验证，请观看此视频：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>B2B 用户 Azure AD 产品兑换的多重身份验证

若要详细了解 Azure AD 多重身份验证兑换体验，请观看此视频：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>针对 B2B 用户的 Azure AD 多重身份验证重置

现在，可以使用以下 PowerShell cmdlet 来对 B2B 来宾用户进行身份验证：

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

3. 为特定用户重置 Azure AD 多重身份验证方法，以要求 B2B 协作用户重新设置验证方法。 
   以下是示例：

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>B2B 用户的条件性访问

存在影响 B2B 来宾用户的 CA 策略的各种因素。

### <a name="device-based-conditional-access"></a>基于设备的条件访问

在 CA 中，有一个选项可以要求用户的 [设备符合或混合 Azure AD 加入](../conditional-access/concept-conditional-access-conditions.md#device-state-preview)。 如果资源租户可以管理其设备，则 B2B 来宾用户只能满足符合性要求。 一次不能由多个组织管理设备。 B2B 来宾用户不能满足混合 Azure AD 加入，因为他们没有本地 AD 帐户。 仅当来宾用户的设备不受管理时，他们可以在资源租户中注册或注册其设备，并使设备符合要求。 然后，用户可以满足授权控制。

>[!Note]
>建议不要将托管设备用于外部用户。

### <a name="mobile-application-management-policies"></a>移动应用程序管理策略

CA 授权控件，如 " **需要批准的客户端应用** " 和 "需要 **应用保护策略** " 需要在租户中注册设备。 这些控件只能应用于 [iOS 和 Android 设备](../conditional-access/concept-conditional-access-conditions.md#device-platforms)。 但是，如果用户的设备已被其他组织管理，则这些控件都不能应用于 B2B 来宾用户。 一次不能在多个租户中注册移动设备。 如果移动设备由另一组织管理，则该用户将被阻止。 仅当来宾用户的设备不受管理时，他们可以在资源租户中注册其设备。 然后，用户可以满足授权控制。  

>[!NOTE]
>不建议对外部用户要求使用应用保护策略。

### <a name="location-based-conditional-access"></a>基于位置的条件性访问

如果邀请的组织可以创建定义其合作伙伴组织的受信任 IP 地址范围，则可以强制实施基于位置的基于 IP 范围的 [策略](../conditional-access/concept-conditional-access-conditions.md#locations) 。

还可以根据 **地理位置** 强制实施策略。

### <a name="risk-based-conditional-access"></a>基于风险的条件访问

如果 B2B 来宾用户满足 grant 控制规定，则强制实施 [登录风险策略](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) 。 例如，组织可能需要 Azure AD 多重身份验证以实现中等或高的登录风险。 但是，如果用户之前未在资源租户中注册 Azure AD 多重身份验证，则该用户将被阻止。 这样做是为了防止恶意用户在其泄露合法用户的密码时注册自己的 Azure AD 多重身份验证凭据。

但是， [用户风险策略](../conditional-access/concept-conditional-access-conditions.md#user-risk) 无法在资源租户中解决。 例如，如果需要对高风险来宾用户进行密码更改，则会因为无法重置资源目录中的密码而被阻止。

### <a name="conditional-access-client-apps-condition"></a>条件访问客户端应用条件

[客户端应用条件](../conditional-access/concept-conditional-access-conditions.md#client-apps) 的行为与其他类型的用户相同。 例如，你可以阻止来宾用户使用旧的身份验证协议。

### <a name="conditional-access-session-controls"></a>条件访问会话控件

对于 B2B 来宾用户，[会话控件](../conditional-access/concept-conditional-access-session.md)的行为与其他类型的用户相同。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章 Azure AD B2B 协作：

- [什么是 Azure AD B2B 协作？](./what-is-b2b.md)
- [标识保护和 B2B 用户](../identity-protection/concept-identity-protection-b2b.md)
- [外部标识定价](https://azure.microsoft.com/pricing/details/active-directory/)
- [常见问题 (常见问题解答) ](./faq.md)