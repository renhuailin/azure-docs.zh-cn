---
title: 创建用户流 - Azure Active Directory B2C
description: 了解如何在 Azure 门户中创建用户流，以便在 Azure Active Directory B2C 中启用应用程序的注册、登录和用户配置文件编辑。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bd436b972dfb1549232831b1f07c3726ff459dd
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556504"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中创建用户流

可以在 Azure Active Directory B2C (Azure AD B2C) 租户中创建不同类型的[用户流](user-flow-overview.md)，并根据需要在应用程序中使用它们。 可以跨应用程序重复使用用户流。

> [!IMPORTANT]
> 我们更改了引用用户流版本的方式。 之前，我们提供 V1（生产就绪）版本，还提供了 V1.1 和 V2（预览版）版本。 而现在，我们将用户流整合到了“建议”（下一代预览版）和“标准”（正式发布版）版本中 。 所有 V1.1 和 V2 旧预览版用户流将在 2021 年 8 月 1 日之前逐渐被弃用。 有关详细信息，请参阅 [Azure AD B2C 中的用户流版本](user-flow-versions.md)。

## <a name="before-you-begin"></a>在开始之前

- 注册要用来测试新用户流的应用程序。 有关示例，请参阅[教程：在 Azure AD B2C 中注册 Web 应用程序](tutorial-register-applications.md)。
- 如果想让用户能够通过 Azure AD、Amazon、Facebook、GitHub、LinkedIn、Microsoft 或 Twitter 等提供者进行登录，请添加外部标识提供者。 请参阅[将标识提供者添加到 Azure AD B2C 中的应用程序](add-identity-provider.md)。
- 配置本地帐户标识提供者，以指定希望在租户中支持的本地帐户的标识类型（电子邮件、用户名、电话号码）。 然后，在创建单个用户流时，可以从这些支持的标识类型中进行选择。 用户完成用户流后，将在 Azure AD B2C 目录中创建本地帐户，并且你的本地帐户标识提供者将对用户的信息进行身份验证。 通过以下步骤配置租户的本地帐户标识提供者：

   1. 登录 [Azure 门户](https://portal.azure.com/)。 
   2. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录。
   3. 在 Azure 门户顶部的搜索栏中，搜索并选择“Azure AD B2C”。
   4. 在“管理”下，选择“标识提供者” 。
   5. 在标识提供者列表中，选择“本地帐户”。
   6. 在“配置本地 IDP”页上，选择要支持的所有标识类型。 选择此处的选项只是使其可供以后创建的用户流使用：
      - 电话号码（预览）：使用户可以输入电话号码，该电话号码在注册时会经过验证并成为其用户 ID。
      - 电子邮件（默认）：使用户可以输入电子邮件地址，该地址在注册时会经过验证并成为其用户 ID。
      - **用户名**：使用户可以创建自己唯一的用户 ID。 系统会从用户收集电子邮件地址并进行验证。
    7. 选择“保存”。

## <a name="create-a-user-flow"></a>创建用户流

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。

    ![Azure 门户 中的 B2C 租户、目录和订阅窗格](./media/create-user-flow/directory-subscription-pane.png)

3. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
4. 在“策略”下，依次选择“用户流”、“新建用户流”。

    ![门户中的“用户流”页面，突出显示了“新建用户流”按钮](./media/create-user-flow/signup-signin-user-flow.png)

5. 在“创建用户流”页上，选择要创建的用户流类型（有关概述，请参阅 [Azure AD B2C 中的用户流](user-flow-overview.md)）。

    ![选择突出显示了“注册和登录”流的用户流页面](./media/create-user-flow/select-user-flow-type.png)

6. 在“选择版本”下，选择“建议”，然后选择“创建”  。 （[详细了解](user-flow-versions.md)用户流版本。）

    ![在 Azure 门户中创建用户流页面（其中，属性突出显示）](./media/create-user-flow/select-version.png)

7. 输入用户流的名称（例如，signupsignin1、profileediting1、passwordreset1）  。
8. 在“标识提供者”下，根据所创建的用户流类型选择以下选项：

   - 本地帐户。 如果想使用户可以在 Azure AD B2C 租户中创建本地帐户，请选择希望他们使用的标识符的类型（例如，电子邮件、用户 ID 或电话号码）。 仅列出在[本地帐户标识提供者](#before-you-begin)设置中配置的标识类型。

   - 社交标识提供者。 如果要使用户可以通过[已添加的社交标识提供者](add-identity-provider.md)（如 Azure AD、Amazon、Facebook、GitHub、LinkedIn、Microsoft 或 Twitter）进行登录，请从列表中选择提供者。

9. 对于“用户属性和声明”，请选择在注册期间要从用户收集并发送的声明和属性。 选择“显示更多”。 选择属性和声明，然后选择“确定”。

    ![属性和声明选择页，有三个声明处于选中状态](./media/create-user-flow/signup-signin-attributes.png)

10. 选择“创建”以添加用户流。 名称中会自动附加前缀 B2C_1。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择“策略” > “用户流”，然后选择所创建的用户流 。 在用户流概述页上，选择“运行用户流”。
1. 对于“应用程序”，选择在步骤 1 中注册的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”。
2. 根据要测试的用户流类型，可以使用有效的电子邮件地址并遵循注册流程进行注册，也可以使用之前创建的帐户进行登录。

    ![门户中的“运行用户流”页面，突出显示了“运行用户流”按钮](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. 遵循用户流提示。 完成用户流后，令牌将返回到 `https://jwt.ms` 并显示出来。

> [!NOTE]
> “运行用户流”体验当前与使用授权代码流的 SPA 回复 URL 类型不兼容。 若要将“运行用户流”体验与这些类型的应用结合使用，请注册类型为“Web”的回复 URL，并启用隐式流，如[此处](tutorial-register-spa.md)所述。

## <a name="next-steps"></a>后续步骤

- [向 Azure AD B2C 用户流添加条件访问](conditional-access-user-flow.md)
- [在 Azure AD B2C 用户流中自定义用户界面](customize-ui-with-html.md)
