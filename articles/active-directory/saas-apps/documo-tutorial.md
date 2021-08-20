---
title: 教程：Azure Active Directory 与 Documo 的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Documo 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/05/2021
ms.author: jeedes
ms.openlocfilehash: b73c760185e8b5d97ab03fbbee75fd399e0b0c16
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112466494"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-documo"></a>教程：Azure Active Directory 与 Documo 的单一登录 (SSO) 集成

本教程介绍如何将 Documo 与 Azure Active Directory (Azure AD) 集成。 将 Documo 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 Documo。
* 让用户使用其 Azure AD 帐户自动登录到 Documo。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Documo 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Documo 支持 SP 和 IDP 发起的 SSO。
* Documo 支持实时用户预配。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-documo-from-the-gallery"></a>从库中添加 Documo

要配置 Documo 与 Azure AD 的集成，需要从库中将 Documo 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Documo” 。
1. 从结果面板中选择“Documo”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-documo"></a>配置并测试 Documo 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Documo 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Documo 中的相关用户之间建立链接关系。

若要配置并测试 Documo 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Documo SSO](#configure-documo-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Documo 测试用户](#create-documo-test-user)** - 在 Documo 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Documo”应用程序集成页面上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成  。 如果 Documo 帐户具有自定义域，还必须有一个自定义 API 域，SSO 才能正常工作。 将默认值替换为自定义 API 域，例如 `https://mycustomapidomain.com` 和 `https://mycustomapidomain.com/assert`。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：  
    `https://app.documo.com/sso`

1. 单击“ **保存**”。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Documo”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Documo 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Documo”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-documo-sso"></a>配置 Documo SSO

1. 以管理员身份登录 Documo 网站。

1. 转到“帐户设置” -> “安全性” 。

    ![“安全性”页面的屏幕截图。](./media/documo-tutorial/security.png)

1. 在“安全性”选项卡中，单击页面底部的“配置 SSO”按钮。

    ![配置按钮的屏幕截图。](./media/documo-tutorial/configure-sso.png)

1. 在“设置 SAML”页面中执行以下步骤。

    ![配置页面的屏幕截图。](./media/documo-tutorial/setup-saml.png)

    a. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 。

    b. 在“SSO URL (重定向 URL)”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    c. 在文本框中输入“电子邮件域”值。

    d. 在“SAML 令牌中包含标识电子邮件的字段名称”文本框中输入该值。

    e. 在记事本中打开从 Azure 门户下载的“联合元数据 XML”。 找到 `<X509Certificate>` 标记，然后将内容粘贴到“签名者证书”文本框中。

    f. 单击“提交”  。

### <a name="create-documo-test-user"></a>创建 Documo 测试用户

在本部分，我们会在 Documo 中创建一个名为 Britta Simon 的用户。 Documo 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Documo 中尚不存在用户，则会在身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Documo 登录 URL，你可以在其中启动登录流。  

* 直接转到 Documo 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 Documo 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Documo 磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页面来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Documo。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 Documo 后，即可强制实施会话控制，从而防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。