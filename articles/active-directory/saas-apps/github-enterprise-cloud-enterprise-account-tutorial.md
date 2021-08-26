---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 GitHub Enterprise Cloud - 企业帐户的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 GitHub Enterprise Cloud - 企业帐户之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/25/2021
ms.author: jeedes
ms.openlocfilehash: 4fdaad6d828ec83f40b6fb632d3d9738a7b72dc7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750401"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>教程：Azure Active Directory 单一登录 (SSO) 与 GitHub Enterprise Cloud - 企业帐户的集成

本教程介绍如何将 GitHub Enterprise Cloud - 企业帐户与 Azure Active Directory (Azure AD) 集成。 将 GitHub Enterprise Cloud - 企业帐户与 Azure AD 集成，可以实现以下目的：

* 在 Azure AD 中控制谁有权访问 GitHub 企业帐户及其中的任何组织。


## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个 [GitHub 企业帐户](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts)。
* 一个用作企业帐户所有者的 GitHub 用户帐户。 

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* GitHub Enterprise Cloud - 企业帐户支持 SP 和 IDP 发起的 SSO 。
* GitHub Enterprise Cloud - 企业帐户支持实时用户预配。

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>从库中添加 GitHub Enterprise Cloud - 企业帐户

若要配置 GitHub Enterprise Cloud - 企业帐户与 Azure AD 的集成，需要从库中将 GitHub Enterprise Cloud - 企业帐户添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“GitHub Enterprise Cloud - 企业帐户” 。
1. 从结果面板中选择“GitHub Enterprise Cloud - 企业帐户”，然后添加应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>为 GitHub Enterprise Cloud - 企业帐户配置和测试 Azure AD SSO

使用名为“B.Simon”的测试用户配置并测试 GitHub Enterprise Cloud - 企业帐户的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 GitHub Enterprise Cloud - 企业帐户中的相关用户之间建立链接关系。

若要配置并测试 GitHub Enterprise Cloud - 企业帐户的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[将 Azure AD 用户和测试用户帐户分配给 GitHub 应用](#assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app)** - 启用你的用户帐户和测试用户 `B.Simon` 以使用 Azure AD 单一登录。
1. **[为企业帐户及其组织启用和测试 SAML](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** - 在应用程序端配置单一登录设置。
    1. **[使用另一个企业帐户所有者或组织成员帐户测试 SSO](#test-sso-with-another-enterprise-account-owner-or-organization-member-account)** - 验证配置是否正常发挥作用。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中，在 GitHub Enterprise Cloud - 企业帐户应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

     在“登录 URL”文本框中，使用以下模式键入 URL：`https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > 将 `<ENTERPRISE-SLUG>` 替换为 GitHub 企业帐户的实际名称。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上   。

    ![证书下载链接](common/certificateBase64.png)

1. 在“设置 GitHub Enterprise Cloud - 企业帐户”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 `B.Simon` 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>将 Azure AD 用户和测试用户帐户分配给 GitHub 应用

在本部分，你将向 `B.Simon` 和你的用户帐户授予访问 GitHub Enterprise Cloud - 企业帐户的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“GitHub Enterprise Cloud - 企业帐户”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从用户列表中选择 B.Simon 和你的用户账户，然后单击屏幕底部的“选择”按钮  。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>为企业帐户及其组织启用和测试 SAML

若要在“GitHub Enterprise Cloud - 企业帐户”端配置单一登录，请按照[此 GitHub 文档](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account)中列出的步骤操作。 
1. 使用作为[企业帐户所有者](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner)的用户帐户登录 GitHub.com。 
1. 从 Azure 门户复制应用的 `Login URL` 字段中的值，并将其粘贴到 GitHub 企业帐户 SAML 设置的 `Sign on URL` 字段中。 
1. 从 Azure 门户复制应用的 `Azure AD Identifier` 字段中的值，并将其粘贴到 GitHub 企业帐户 SAML 设置的 `Issuer` 字段中。 
1. 复制上述步骤中从 Azure 门户下载的“证书(Base64)”文件的内容，并将其粘贴到 GitHub 企业帐户 SAML 设置的相应字段中。 
1. 单击 `Test SAML configuration`，并确认你能够成功地从 GitHub 企业帐户对 Azure AD 进行身份验证。
1. 测试成功后，保存设置。 
1. 在首次通过 SAML 从 GitHub 企业帐户进行身份验证后，将在 GitHub 企业帐户中创建一个链接的外部标识，它将已登录的 GitHub 用户帐户与 Azure AD 用户帐户相关联。  
 
为 GitHub 企业帐户启用 SAML SSO 后，默认情况下将为企业帐户拥有的所有组织启用 SAML SSO。 所有成员都需要使用 SAML SSO 进行身份验证，以获得对其所属组织的访问权限，而企业所有者在访问企业帐户时将需要使用 SAML SSO 进行身份验证。

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>使用另一个企业帐户所有者或组织成员帐户测试 SSO

为 GitHub 企业帐户设置 SAML 集成后（也适用于企业帐户中的 GitHub 组织），分配给 Azure AD 中的应用的其他企业帐户所有者应该能够导航到 GitHub 企业帐户 URL (`https://github.com/enterprises/<enterprise account>`)，通过 SAML 进行身份验证，并访问 GitHub 企业帐户下的策略和设置。 

企业帐户中组织的所有者应该能够[邀请用户加入其 GitHub 组织](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization)。 使用组织所有者帐户登录 GitHub.com，然后按照本文中的步骤邀请 `B.Simon` 加入组织。 如果还没有 GitHub 用户帐户，则需要为 `B.Simon` 创建一个。 

若要使用 `B.Simon` 测试用户帐户在企业帐户下测试 GitHub 组织访问权限：
1. 邀请 `B.Simon` 作为组织所有者加入企业帐户下的组织。 
1. 使用要关联到 `B.Simon` Azure AD 用户帐户的用户帐户登录 GitHub.com。
1. 使用 `B.Simon` 用户帐户登录到 Azure AD。
1. 转到 GitHub 组织。 系统应会提示用户通过 SAML 进行身份验证。 SAML 身份验证成功之后，`B.Simon` 应该能够访问组织资源。 

## <a name="next-steps"></a>后续步骤

配置 GitHub Enterprise Cloud - 企业帐户后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。
