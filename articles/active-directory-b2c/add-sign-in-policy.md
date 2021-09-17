---
title: 设置登录流
titleSuffix: Azure Active Directory B2C
description: 了解如何在 Azure Active Directory B2C 中设置登录流。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/07/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71784aa5e40cc7af96faa60d0779a779e99f3425
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777844"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置登录流

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>登录流概述

通过使用登录策略，用户可以： 

* 用户可以使用 Azure AD B2C 本地帐户进行登录
* 使用社交帐户进行注册或登录
* 密码重置
* 用户无法注册 Azure AD B2C 本地帐户。 若要创建帐户，管理员可以使用 [Azure 门户](manage-users-portal.md#create-a-consumer-user)或 [MS Graph API](microsoft-graph-operations.md)。

![配置文件编辑流](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>先决条件

[在 Azure Active Directory B2C 中注册 Web 应用程序](tutorial-register-applications.md)（如果尚未这样做）。

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>创建登录用户流

若要添加登录策略，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录 + 订阅”页上，在“目录名称”列表中找到 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“策略”下，依次选择“用户流”、“新建用户流”。
1. 在“创建用户流”页上，选择“登录”用户流 。
1. 在“选择版本”下，选择“建议”，然后选择“创建”  。 （[详细了解](user-flow-versions.md)用户流版本。）
1. 输入该用户流的 **名称**。 例如 *signupsignin1*。
1. 在“标识提供者”下，至少选择一个标识提供者：

   * 在“本地帐户”下，选择下列选项之一：“电子邮件登录”、“用户 ID 登录”、“手机登录”、“手机/电子邮件登录”、“用户 ID/电子邮件登录”或“无”。 [了解详细信息](sign-in-options.md)。
   * 在“社交标识提供者”下，选择已设置的任一外部社交标识提供者或企业标识提供者。 [了解详细信息](add-identity-provider.md)。
1. 在“多重身份验证”下，如果你希望要求用户使用另一种身份验证方法来验证其身份，请选择方法类型，以及何时要强制执行多重身份验证 (MFA)。 [了解详细信息](multi-factor-authentication.md)。
1. 在“条件访问”下，如果已为 Azure AD B2C 租户配置条件访问策略，并且想要为此用户流启用这些策略，请选择“强制实施条件访问策略”复选框 。 无需指定策略名称。 [了解详细信息](conditional-access-user-flow.md?pivots=b2c-user-flow)。
1. 在“应用程序声明”下，选择要在令牌中返回给应用程序的声明。 若要显示完整的值列表，请选择“显示更多”，选择值，然后选择“确定” 。
   > [!NOTE]
   > 还可以[创建自定义属性](user-flow-custom-attributes.md?pivots=b2c-user-flow)以便在 Azure AD B2C 租户中使用。
1. 单击“创建”以添加用户流。 名称中会自动附加前缀 B2C_1。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择已创建的用户流以打开其概览页，然后选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击“运行用户流”。
1. 如果没有注册链接，应该能够使用你所创建的账户（使用 MS Graph API）进行登录。 返回的令牌包含所选的声明。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>删除注册链接

**SelfAsserted-LocalAccountSignin-Email** 属于 [自断言](self-asserted-technical-profile.md)的技术配置文件，在注册或登录流中调用。 若要删除注册链接，请将 `setting.showSignupLink` 元数据设置为 `false`。 覆盖扩展文件中的 SelfAsserted-LocalAccountSignin-Email 技术配置文件。 

1. 打开策略的扩展文件， 例如，_`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`_。
1. 查找 `ClaimsProviders` 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明提供程序添加到 `ClaimsProviders` 元素：

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. 在 `<BuildingBlocks>` 元素中，添加以下 [ContentDefinition](contentdefinitions.md) 以引用版本 1.2.0 或更新版本的数据 URI：

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>更新并测试策略

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，确保使用包含 Azure AD 租户的目录。
1. 在“门户设置 | 目录 + 订阅”页上，在“目录名称”列表中找到 Azure AD 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
1. 选择“标识体验框架”。
1. 选择“上传自定义策略”，然后上传已更改的策略文件 *TrustFrameworkExtensions.xml*。
1. 选择已上传的登录策略，并单击“立即运行”按钮。
1. 如果没有注册链接，应该能够使用你所创建的账户（使用 MS Graph API）进行登录。

::: zone-end

## <a name="next-steps"></a>后续步骤

* 添加[使用社交标识提供者登录](add-identity-provider.md)。
* 创建[密码重置流](add-password-reset-policy.md)。
