---
title: 创建注册和登录流
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中创建注册和登录流。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0c1871921160b0b5862e7655a3826949406ac111
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777799"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中创建注册和登录流

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>注册和登录流

使用注册和登录策略，用户可以： 

* 使用本地帐户注册
* 使用本地帐户登录
* 使用社交帐户注册或登录
* 密码重置

![配置文件编辑流](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>先决条件

[在 Azure Active Directory B2C 中注册 Web 应用程序](tutorial-register-applications.md)（如果尚未这样做）。

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>创建注册和登录用户流

注册和登录用户流通过单一配置处理注册和登录体验。 根据上下文将应用程序用户引导至正确的路径。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上，在“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“策略”下，依次选择“用户流”、“新建用户流”。

    ![门户中的“用户流”页面，突出显示了“新建用户流”按钮](./media/add-sign-up-and-sign-in-policy/sign-up-sign-in-user-flow.png)

1. 在“创建用户流”页面上，选择“注册和登录”用户流 。

    ![选择突出显示了“注册和登录”流的用户流页面](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. 在“选择版本”下，选择“建议”，然后选择“创建”  。 （[详细了解](user-flow-versions.md)用户流版本。）

    ![在 Azure 门户中创建用户流页面（其中，属性突出显示）](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. 输入该用户流的 **名称**。 例如 *signupsignin1*。
1. 在“标识提供者”下，至少选择一个标识提供者：

   * 在“本地帐户”下，选择以下项之一：“电子邮件注册”、“用户 ID 注册”、“手机注册”、“手机/电子邮件注册”或“无”     。 [了解详细信息](sign-in-options.md)。
   * 在“社交标识提供者”下，选择已设置的任一外部社交标识提供者或企业标识提供者。 [了解详细信息](add-identity-provider.md)。
1. 在“多重身份验证”下，如果你希望要求用户使用另一种身份验证方法来验证其身份，请选择方法类型，以及何时要强制执行多重身份验证 (MFA)。 [了解详细信息](multi-factor-authentication.md)。
1. 在“条件访问”下，如果已为 Azure AD B2C 租户配置条件访问策略，并且想要为此用户流启用这些策略，请选择“强制实施条件访问策略”复选框 。 无需指定策略名称。 [了解详细信息](conditional-access-user-flow.md?pivots=b2c-user-flow)。
1. 在“用户属性和令牌声明”下，选择在注册期间要从用户收集的属性，以及要在令牌中返回的声明。 若要显示完整的值列表，请选择“显示更多”，选择值，然后选择“确定” 。

   > [!NOTE]
   > 还可以[创建自定义属性](user-flow-custom-attributes.md?pivots=b2c-user-flow)以便在 Azure AD B2C 租户中使用。

    ![属性和声明选择页，有三个声明处于选中状态](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. 选择“创建”以添加用户流。 名称中会自动附加前缀 B2C_1。
1. 按照注册或登录策略中的步骤[处理“忘记了密码?”的流](add-password-reset-policy.md?pivots=b2c-user-flow.md#self-service-password-reset-recommended) 。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择已创建的用户流以打开其概览页，然后选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击“运行用户流”，然后选择“立即注册”。

    ![门户中的“运行用户流”页面，突出显示了“运行用户流”按钮](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.png)

1. 输入有效的电子邮件地址，单击“发送验证码”，输入收到的验证码，然后选择“验证代码”。
1. 输入新密码并确认。
1. 选择所在的国家和地区，输入要显示的名称，输入邮政编码，然后单击“创建”。 令牌将返回到 `https://jwt.ms` 并显示出来。
1. 现在可以再次运行用户流，你应该可以使用创建的帐户登录。 返回的令牌包含所选国家/地区、名称和邮政编码的声明。

> [!NOTE]
> “运行用户流”体验当前与使用授权代码流的 SPA 回复 URL 类型不兼容。 若要将“运行用户流”体验与这些类型的应用结合使用，请注册类型为“Web”的回复 URL，并启用隐式流，如[此处](tutorial-register-spa.md)所述。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>创建注册或登录策略

自定义策略是上传到 Azure AD B2C 租户的一组 XML 文件，用于定义用户旅程。 我们提供了初学者包，其中有多个预构建策略，包括注册和登录、密码重置以及配置文件编辑策略。 有关详细信息，请参阅 [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)。

::: zone-end

## <a name="next-steps"></a>后续步骤

* 添加[使用社交标识提供者登录](add-identity-provider.md)。
* 创建[密码重置流](add-password-reset-policy.md)。
