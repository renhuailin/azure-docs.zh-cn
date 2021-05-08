---
title: 设置本地帐户标识提供者
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 租户中设置用户流时，请定义可用于本地帐户身份验证的标识类型（电子邮件、用户名和电话号码）。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dd21c1dca0dd54331780ba98f9c53d5b99d6b4e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100557226"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>为用户流设置电话注册和登录（预览版）

> [!NOTE]
> 用户流的手机注册和登录以及恢复电子邮件功能处于公共预览版阶段。

除了电子邮件和用户名之外，你还可以将电话号码注册和登录添加到你的本地帐户标识提供者，从而使电话号码能够在租户范围内成为注册选项。 在为本地帐户启用电话注册和登录后，可将电话注册添加到用户流。

在用户流中设置电话注册和登录涉及以下步骤：

- 在本地帐户标识提供者中[配置租户范围内的电话注册和登录](#configure-phone-sign-up-and-sign-in-tenant-wide)，以接受电话号码作为用户的标识。 

- [将电话注册添加到用户流](#add-phone-sign-up-to-a-user-flow)，以使用户能够使用他们的电话号码来注册你的应用程序。

- [启用恢复电子邮件提示（预览版）](#enable-the-recovery-email-prompt-preview)，以使用户能够指定在没有电话的情况下可用于恢复其帐户的电子邮件。

在为用户流配置电话注册时，默认情况下会禁用多重身份验证 (MFA)。 你可以在具有电话注册的用户流中启用 MFA，但是，由于使用电话号码作为主标识符，因此，对于第二个身份验证因素，只提供电子邮件一次性密码这一个选项。

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>配置租户范围内的电话注册和登录

在默认情况下，本地帐户标识提供者设置中会启用电子邮件注册。 可以通过选择或取消选择电子邮件注册、用户名或电话号码来更改将在租户中支持的标识类型。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 请确保使用包含 Azure AD B2C 租户的目录，方法是：选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD 租户的目录。

3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。

4. 在“管理”下，选择“标识提供者” 。

5. 在标识提供者列表中，选择“本地帐户”。

   ![标识提供者选择本地帐户](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. 在“配置本地 IDP”页中，确保已选择“电话”作为允许的身份类型（使用者可用于在你的 Azure AD B2C 租户中创建其本地帐户）之一 。

   ![选择允许的标识类型](media/phone-authentication-user-flows/configure-local-idp.png)

1. 选择“保存”。

## <a name="add-phone-sign-up-to-a-user-flow"></a>将电话注册添加到用户流

在为本地帐户添加了电话注册作为标识选项后，可将其添加到用户流，只要这些用户流是最新的建议用户流版本即可。 以下示例演示如何将电话注册添加到新用户流。 不过，你也可以将电话注册添加到现有的推荐版本用户流（选择“用户流” > “用户流名称” > “标识提供者” > “本地帐户电话注册”） 。 

以下示例演示如何将电话注册添加到新用户流。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。

    ![Azure 门户 中的 B2C 租户、目录和订阅窗格](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
4. 在“策略”下，依次选择“用户流”、“新建用户流”。

    ![门户中的“用户流”页面，突出显示了“新建用户流”按钮](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. 在“创建用户流”页面上，选择“注册和登录”用户流 。

    ![选择突出显示了“注册和登录”流的用户流页面](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. 在“选择版本”下，选择“建议”，然后选择“创建”  。 （[详细了解](user-flow-versions.md)用户流版本。）

    ![创建用户流按钮](./media/phone-authentication-user-flows/select-version.png)

7. 输入该用户流的 **名称**。 例如 *signupsignin1*。
8. 在“标识提供者”部分中“本地帐户”下，选择“电话注册”  。

   ![已选择用户流电话注册选项](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. 在“社交标识提供者”下面，选择要允许此用户流使用的任何其他标识提供者。

   > [!NOTE]
   > 在默认情况下，对于注册用户流，多重身份验证 (MFA) 处于禁用状态。 你可以为电话注册用户流启用 MFA，但是，由于使用电话号码作为主标识符，因此，对于第二个身份验证因素，只提供电子邮件一次性密码这一个选项。

1. 在“用户属性和令牌声明”部分中，请选择在注册期间要从用户收集和发送的声明和属性。 例如，选择“显示更多”，然后选择“国家/地区”、“显示名称”和“邮政编码”的属性和声明。 选择“确定”。

1. 选择“创建”以添加用户流。 名称中会自动附加前缀 B2C_1。

## <a name="enable-the-recovery-email-prompt-preview"></a>启用恢复电子邮件提示（预览版）

在为用户流启用电话注册和登录时，最好启用恢复电子邮件功能。 使用此功能，用户可以提供电子邮件地址，该地址可用于在没有电话的情况下恢复用户帐户。 此电子邮件地址只用于帐户恢复。 它不能用于登录。

- 在恢复电子邮件提示处于打开状态时，系统会要求首次注册的用户验证备份电子邮件。 对于以前未提供恢复电子邮件的用户，系统会在他们下次登录的过程中要求他们验证备份电子邮件。

- 在恢复电子邮件处于关闭状态时，注册或登录的用户不会看到恢复电子邮件提示。
 
可以在用户流属性中启用恢复电子邮件提示。

> [!NOTE]
> 在开始之前，请确保已按上文所述[将电话注册添加到了用户流](#add-phone-sign-up-to-a-user-flow)。

### <a name="to-enable-the-recovery-email-prompt"></a>启用恢复电子邮件提示

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
3. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
4. 在 Azure AD B2C 中的“策略”下，选择“用户流” 。
5. 从列表中选择用户。
6. 在“设置”下，选择“属性”   。
7. 在“启用电话号码注册和登录的恢复电子邮件地址提示(预览)”旁边，选择：

   - “打开”，以在注册和登录过程中都显示恢复电子邮件提示。
   - “关闭”，以隐藏恢复电子邮件提示。

    ![启用了“启用恢复电子邮件”的用户流属性](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. 选择“保存”。

### <a name="to-test-the-recovery-email-prompt"></a>测试恢复电子邮件提示

在用户流中启用了电话注册和登录以及恢复电子邮件提示后，可使用“运行用户流”来测试用户体验。

1. 选择“策略” > “用户流”，然后选择所创建的用户流 。 在用户流概述页上，选择“运行用户流”。

2. 对于“应用程序”，选择在步骤 1 中注册的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。

3. 选择“运行用户流”，并验证以下行为：

   - 系统会要求首次注册的用户提供恢复电子邮件。 
   - 对于已注册但尚未提供恢复电子邮件的用户，系统会要求他们在登录时提供恢复电子邮件。

4. 输入电子邮件地址，然后选择“发送验证码”。 验证是否已将代码发送到你提供的电子邮件收件箱。 检索该验证码，并将其输入到“验证码”框中。 然后选择“验证验证码”。

## <a name="next-steps"></a>后续步骤

- [添加外部标识提供者](add-identity-provider.md)
- [创建用户流](tutorial-create-user-flows.md)