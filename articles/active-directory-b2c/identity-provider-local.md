---
title: 设置 Azure AD B2C 本地帐户标识提供者
titleSuffix: Azure AD B2C
description: 定义用户可用于在 Azure Active Directory B2C 租户中注册或登录的标识类型（电子邮件、用户名和电话号码）。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f5f72fa4dc126074177b96a484c10ade06724f83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574436"
---
# <a name="set-up-the-local-account-identity-provider"></a>设置本地帐户标识提供者

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

本文介绍如何为 Azure AD B2C 本地帐户确定登录方法。 本地帐户是指当用户注册应用程序或管理员创建帐户时在 Azure AD B2C 目录中创建的帐户。 用户名和密码存储在本地，Azure AD B2C 用作本地帐户的标识提供者。

本地帐户可以使用多种登录方法：

- 电子邮件：用户可以使用其电子邮件地址和密码进行登录和注册。 在默认情况下，本地帐户标识提供者设置中会启用电子邮件注册。
- 用户名：用户可以使用用户名和密码进行登录和注册。
- 电话(或“无密码身份验证)：用户可以使用电话号码作为其主要登录标识符来注册并登录到应用。 他们不需要创建密码。 一次性密码将通过短信发送给用户。
- 电话或电子邮件：用户可以通过输入电话号码或电子邮件地址来注册或登录。 根据用户输入，Azure AD B2C 会将用户转到注册或登录页中的相应流。
- 电话恢复：如果你已启用电话注册或登录，则手机恢复允许用户提供一个电子邮件地址，该地址可用于在没有手机的情况下恢复帐户。

若要了解有关这些方法的详细信息，请参阅[登录选项](sign-in-options.md)。 

若要配置社交或企业标识设置（其中用户的标识由 Facebook 或 Google 等联合标识提供者进行管理），请参阅[添加标识提供者](add-identity-provider.md)。

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>配置本地帐户标识提供者设置


可以通过在 Azure AD B2C 标识提供者的列表中配置本地帐户提供者，来选择要在租户中使用的本地帐户登录方法（电子邮件、用户名或电话号码）。 然后，在设置用户流时，可以选择已在租户范围内启用的本地帐户登录方法之一。 你只能为用户流选择一个本地帐户登录方法，但可以为每个用户流选择不同的选项。

若要在租户级别设置本地帐户登录选项： 

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在“Azure 服务”下，选择“Azure AD B2C”。 或者，使用搜索框查找并选择“Azure AD B2C”。
1. 在“管理”下，选择“标识提供者” 。
1. 在标识提供者列表中，选择“本地帐户”。
1. 在“配置本地 IDP”页中，选择一个或多个要为 Azure AD B2C 租户中的用户流启用的标识类型。 选择此处的选项只是使其可在租户范围内使用；创建或修改用户流时，可以从此处启用的选项中进行选择。

   - 电话：提示用户输入电话号码，该电话号码在注册时会经过验证并成为其用户 ID。
   - 用户名：使用户可以创建自己唯一的用户 ID。 系统会从用户那里收集电子邮件地址并进行验证。
   - 电子邮件：系统会提示用户输入电子邮件地址，此地址将在注册时经过验证并成为用户 ID。
1. 选择“保存”。

## <a name="configure-your-user-flow"></a>配置用户流

1. 在 Azure 门户的左侧菜单中，选择“Azure AD B2C”。
1. 在“策略”下，选择“用户流(策略)”。 
1. 选择要为其配置注册和登录体验的用户流。
1. 选择“标识提供者”
1. 在“本地帐户”下，选择以下项之一：“电子邮件注册”、“用户 ID 注册”、“手机注册”、“手机/电子邮件注册”或“无”     。

### <a name="enable-the-recovery-email-prompt"></a>启用恢复电子邮件提示

如果选择“手机注册”、“手机/电子邮件注册”选项，请启用恢复电子邮件提示 。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在 Azure AD B2C 中的“策略”下，选择“用户流” 。
1. 从列表中选择用户。
1. 在“设置”下，选择“属性”   。
1. 在“启用电话号码注册和登录的恢复电子邮件地址提示(预览)”旁边，选择：
   - “打开”，以在注册和登录过程中都显示恢复电子邮件提示。
   - “关闭”，以隐藏恢复电子邮件提示。
1. 选择“保存”。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>获取新手包

自定义策略是上传到 Azure AD B2C 租户的一组 XML 文件，用于定义用户旅程。 我们提供了包含多个预建策略的新手包。 下载相关新手包： 

- [电子邮件登录](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [用户名登录](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [手机登录](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 选择 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) 信赖方策略。 
- [手机或电子邮件登录](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 选择 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) 信赖方策略。

下载新手包后。

1. 在每个文件中，将字符串 `yourtenant` 替换为 Azure AD B2C 租户的名称。 例如，如果 B2C 租户的名称为 contosob2c，则 `yourtenant.onmicrosoft.com` 的所有实例都将变为 `contosob2c.onmicrosoft.com`。

1. 完成 [Azure Active Directory B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)的[将应用程序 ID 添加到自定义策略](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-application-ids-to-the-custom-policy)部分中的步骤。 例如，请使用你在完成先决条件时注册的两个应用程序的应用程序（客户端）ID（IdentityExperienceFramework 和 ProxyIdentityExperienceFramework）更新 `/phone-number-passwordless/``Phone_Email_Base.xml`  。
1. 上传策略文件

::: zone-end

## <a name="next-steps"></a>后续步骤

- [添加外部标识提供者](add-identity-provider.md)
- [创建用户流](tutorial-create-user-flows.md)
