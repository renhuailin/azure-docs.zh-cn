---
title: Azure 安全中心的 MFA 安全建议
description: 了解如何使用 Azure 安全中心为 Azure 订阅强制执行多重身份验证
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631891"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>管理订阅上的多重身份验证 (MFA) 实施

如果只使用密码来对用户进行身份验证，则意味着打开了一条攻击途径。 用户经常使用弱密码或对多个服务重用密码。 启用 [MFA](https://www.microsoft.com/security/business/identity/mfa) 后，帐户将更安全，并且用户仍然可以通过单一登录 (SSO) 向几乎所有应用程序验证身份。

可以通过多种方式基于你的组织拥有的许可证为 Azure Active Directory (AD) 用户启用 MFA。 本页提供 Azure 安全中心上下文中每个方式的详细信息。


## <a name="mfa-and-security-center"></a>MFA 和安全中心 

安全中心高度重视 MFA。 为安全功能评分作出最大贡献的安全控制措施是“启用 MFA”。 

“启用 MFA”控制措施中的建议确保你满足订阅用户的建议做法：

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应在对订阅拥有写入权限的帐户上启用 MFA

可以通过三种方法来启用 MFA，并符合安全中心的两个建议：安全默认值、每用户分配、条件访问 (CA) 策略。 以下介绍了每个选项。

### <a name="free-option---security-defaults"></a>免费选项 - 安全默认值
如果你使用的是免费版本的 Azure AD，请使用[安全默认值](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)在租户上启用多重身份验证。

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>适用于 Microsoft 365 商业版、E3 或 E5 客户的 MFA
拥有 Microsoft 365 的客户可以使用每用户分配。 在此情景中，针对所有用户的所有登录事件启用或禁用 Azure AD MFA。 不能仅为一部分用户或在特定情况下启用多重身份验证，管理通过 Office 365 门户进行。

### <a name="mfa-for-azure-ad-premium-customers"></a>适用于 Azure AD Premium 客户的 MFA
若要获得更好的用户体验，请升级到 Azure AD Premium P1 或 P2 以使用条件访问 (CA) 策略选项。 若要配置 CA 策略，需要 [Azure Active Directory (AD) 租户权限](../active-directory/roles/permissions-reference.md)。

CA 策略必须：
- 强制执行 MFA
- 包含 Microsoft Azure 管理应用 ID (797f4846-ba00-4fd7-ba43-dac1f8f63013) 或所有应用
- 不排除 Microsoft Azure 管理应用 ID

在特定的情况下或者发生适合业务要求的事件时，Azure AD Premium P1 客户可以使用 Azure AD CA 提示用户执行多重身份验证。 包含此功能的其他许可证：企业移动性 + 安全性 E3、Microsoft 365 F1 和 Microsoft 365 E3。

Azure AD Premium P2 提供极强大的安全功能和经过改善的用户体验。 此许可证向 Azure AD Premium P1 功能添加[基于风险的条件访问](../active-directory/conditional-access/howto-conditional-access-policy-risk.md)。 基于风险的 CA 适应用户的模式，并最大程度减少多重身份验证提示。 包含此功能的其他许可证：企业移动性 + 安全性 E5 或 Microsoft 365 E5。

在 [Azure 条件访问文档](../active-directory/conditional-access/overview.md)中了解详细信息。

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>标识未启用多重身份验证 (MFA) 的帐户

可以从安全中心“建议详细信息”页面或使用 Azure Resource Graph 查看未启用 MFA 的用户帐户列表。

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>查看 Azure 门户中未启用 MFA 的帐户
在“建议详细信息”页中，从“运行不正常的资源”列表中选择订阅，或选择“执行操作”，这时将显示该列表 。

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>使用 Azure Resource Graph 查看未启用 MFA 的帐户
若要查看未启用 MFA 的帐户，请使用以下 Azure Resource Graph 查询。 该查询返回建议“应对拥有订阅所有者权限的帐户启用 MFA”的所有运行不正常的资源 - 帐户。 

1. 打开“Azure Resource Graph 资源管理器”。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="启动 Azure Resource Graph Explorer 建议页面" :::

1. 输入以下查询并选择“运行查询”。

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData` 属性显示未强制启用 MFA 的帐户的帐户对象 ID 列表。 

    > [!NOTE]
    > 这些帐户显示为对象 ID 而不是帐户名称，以保护帐户持有者的隐私。

> [!TIP]
> 或者，可以使用安全中心的 REST API 方法[评估 - 获取](/rest/api/securitycenter/assessments/get)。


## <a name="faq---mfa-in-security-center"></a>常见问题解答 - 安全中心中的 MFA

- [我们已在使用 CA 策略强制实施 MFA，为什么还会收到安全中心建议？](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [我们已在使用第三方 MFA 工具强制实施 MFA，为什么还会收到安全中心建议？](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [为什么安全中心将没有订阅权限的用户帐户显示为“需要 MFA”？](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [我们正在通过 PIM 强制实施 MFA，为什么 PIM 帐户会显示为不合规？](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [是否可以免除或关闭某些帐户？](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [安全中心的标识和访问保护是否有任何限制？](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>我们已经在使用 CA 策略来强制执行 MFA。 为什么我们仍会获得安全中心建议？
若要调查为何仍在生成建议，请验证 MFA CA 策略中的以下配置选项：

- 已将帐户包含在 MFA CA 策略的“用户”部分（或“组”部分中的一个组）中 
- MFA CA 策略的“应用”部分包含 Azure 管理应用 ID (797f4846-ba00-4fd7-ba43-dac1f8f63013) 或所有应用
- MFA CA 策略的“应用”部分未排除 Azure 管理应用 ID

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>我们正在使用第三方 MFA 工具强制执行 MFA。 为什么我们仍会获得安全中心建议？
安全中心的 MFA 建议不支持第三方 MFA 工具（例如 DUO）。

如果这些建议与你的组织无关，请考虑将它们标记为“已缓解”，如[从安全功能评分中免除资源和建议](exempt-resource.md)中所述。 还可以[禁用建议](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)。

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>为什么安全中心将没有订阅权限的用户帐户显示为“需要 MFA”？
安全中心的 MFA 建议指的是 [Azure RBAC 角色](../role-based-access-control/role-definitions-list.md)和 [Azure 经典订阅管理员](../role-based-access-control/classic-administrators.md)角色。 验证是否所有帐户都没有此类角色。

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>我们正在通过 PIM 强制执行 MFA。 为什么 PIM 帐户显示为不合规？
安全中心的 MFA 建议目前不支持 PIM 帐户。 可以将这些帐户添加到 CA 策略的“用户/组”部分。

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>是否可以免除或关闭某些帐户？
目前不支持免除某些不使用 MFA 的帐户的功能。  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>安全中心的标识和访问保护是否有任何限制？
安全中心的标识和访问保护存在一些限制：

- 标识建议不适用于拥有超过 600 个帐户的订阅。 在这种情况下，这些建议将在“不可用的评估”下列出。
- 标识建议不适用于云解决方案提供商 (CSP) 合作伙伴的管理代理。
- 标识建议不标识使用 Privileged Identity Management (PIM) 系统管理的帐户。 如果使用的是 PIM 工具，则可能会在“管理访问和权限”控件中看到不准确的结果。


## <a name="next-steps"></a>后续步骤
若要详细了解适用于其他 Azure 资源类型的建议，请参阅以下文章：

- [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)