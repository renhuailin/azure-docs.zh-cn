---
title: Azure Active Directory B2C 中的多重身份验证 | Microsoft Docs
description: 如何在由 Azure Active Directory B2C 保护的面向用户的应用程序中启用多重身份验证。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b7b7f1c5fb0a7991707a26b4a7f54fb3ffaf7bab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033514"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中启用多重身份验证

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) 直接集成了 [Azure AD 多重身份验证](../active-directory/authentication/concept-mfa-howitworks.md)，因此你可以为应用程序中的注册和登录体验增加另一层安全性。 无需编写一行代码便可启用多重身份验证。 如果已经创建了注册和登录用户流，则仍然可以启用多重身份验证。

此功能有助于应用程序处理以下情况：

- 不需要多重身份验证即可访问一个应用程序，但需要多重身份验证才能访问另一个应用程序。 例如，客户可以使用社交或本地帐户登录汽车保险应用程序，但是必须在访问在同一目录中注册的家庭保险应用程序之前验证电话号码。
- 通常不需要多重身份验证即可访问一个应用程序，但需要它才能访问其中的敏感部分。 例如，客户可以使用社交或本地帐户登录银行应用程序并查询帐户余额，但必须在尝试进行电子转账前验证电话号码。

## <a name="set-multi-factor-authentication"></a>设置多重身份验证

::: zone pivot="b2c-user-flow"

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 使用顶部菜单中的“目录 + 订阅”筛选器来选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“用户流”。
1. 选择要为其启用 MFA 的用户流。 例如，*B2C_1_signinsignup*。
1. 选择“属性”。
1. 在“多重身份验证”部分中，选择所需的“MFA 方法”，然后在“MFA 强制执行”下选择“始终启用”或“条件(推荐)”    。
   > [!NOTE]
   >
   > - 如果选择“条件(推荐)”，则还需要[用户流添加条件访问](conditional-access-user-flow.md)，并指定要应用策略的应用。
   > - 在默认情况下，对于注册用户流，多重身份验证 (MFA) 处于禁用状态。 你可以在具有电话注册的用户流中启用 MFA，但是，由于使用电话号码作为主标识符，因此，对于第二个身份验证因素，只提供电子邮件一次性密码这一个选项。

1. 选择“保存”。 现在已为此用户流启用 MFA。

可以使用“运行用户流”  来验证体验。 确认以下场景：

在多重身份验证步骤发生之前，在租户中创建了一个客户帐户。 在执行此步骤期间，会要求客户提供一个电话号码并对其进行验证。 如果验证成功，则会将电话号码附加到帐户供以后使用。 即使客户取消或退出登录，也可能会要求客户在下次登录时再次验证电话号码（启用了多重身份验证时）。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要启用多重身份验证，请从 GitHub 获取自定义策略新手包，然后使用 Azure AD B2C 租户名称更新 SocialAndLocalAccountsWithMFA 新手包中的 XML 文件。 SocialAndLocalAccountsWithMFA 启用社交、本地和多重身份验证选项。 有关详细信息，请参阅 [Active Directory B2C 中的自定义策略入门](custom-policy-get-started.md)。 

::: zone-end
