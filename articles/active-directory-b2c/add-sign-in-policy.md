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
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c84966f7b4dc1740235fe6414da2ba832a1334fd
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119903"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置登录流

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>登录流概述

登录策略允许用户： 

* 用户可以使用 Azure AD B2C 本地帐户登录
* 使用社交帐户注册或登录
* 密码重置
* 用户无法注册 Azure AD B2C 本地帐户。 若要创建帐户，管理员可以使用 [Azure 门户](manage-users-portal.md#create-a-consumer-user)或 [MS 图形 API](microsoft-graph-operations.md)。

![配置文件编辑流](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>先决条件

[在 Azure Active Directory B2C 中注册 Web 应用程序](tutorial-register-applications.md)（如果尚未这样做）。

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>创建登录用户流

添加登录策略：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“策略”下，依次选择“用户流”、“新建用户流”。
1. 在 " **创建用户流** " 页上，选择 " **登录** 用户流"。
1. 在“选择版本”下，选择“建议”，然后选择“创建”  。 （[详细了解](user-flow-versions.md)用户流版本。）
1. 输入该用户流的 **名称**。 例如 *signupsignin1*。
1. 对于 **标识提供者**，请选择 " **电子邮件登录**"。
1. 对于 " **应用程序声明**"，请选择要发送到应用程序的声明和属性。 例如，选择 " **显示更多**"，然后选择 " **显示名称**"、" **名称**"、"  **姓氏**" 和 " **用户的对象 ID**" 的 "属性" 和 "声明"。 单击 **“确定”** 。
1. 单击“创建”以添加用户流。 名称中会自动附加前缀 B2C_1。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择已创建的用户流以打开其概览页，然后选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击“运行用户流”。
1. 你应该能够使用在不使用注册链接的情况下使用 MS 图形 API) 创建 (帐户进行登录。 返回的令牌包括你选择的声明。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>删除注册链接

**SelfAsserted-LocalAccountSignin-电子邮件** 技术配置文件是 [自断言](self-asserted-technical-profile.md)的，它是在注册或登录流中调用的。 若要删除注册链接，请将 `setting.showSignupLink` 元数据设置为 `false` 。 覆盖扩展文件中的 SelfAsserted-LocalAccountSignin 技术配置文件。 

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

1. 在 `<BuildingBlocks>` 元素中，添加以下 [ContentDefinition](contentdefinitions.md) 以引用版本1.2.0 或更新的数据 URI：

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

## <a name="update-and-test-your-policy"></a>更新和测试策略

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
1. 选择“标识体验框架”。
1. 选择 " **上传自定义策略**"，然后上传所更改的策略文件， *TrustFrameworkExtensions.xml*。
1. 选择上传的登录策略，然后单击 " **立即运行** " 按钮。
1. 你应该能够使用在不使用注册链接的情况下使用 MS 图形 API) 创建 (帐户进行登录。

::: zone-end

## <a name="next-steps"></a>后续步骤

* 添加 [使用社交标识提供者的登录](add-identity-provider.md)。
* 设置 [密码重置流](add-password-reset-policy.md)。
