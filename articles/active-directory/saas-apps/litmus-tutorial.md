---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Litmus 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Litmus 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 33795de94f5fae35eb6ebd122c26ad7e7d9efb0e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832809"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Litmus 的集成

本教程介绍如何将 Litmus 与 Azure Active Directory (Azure AD) 集成。 将 Litmus 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Litmus。
* 让用户使用其 Azure AD 帐户自动登录到 Litmus。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Litmus 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Litmus 支持 SP 和 IDP  发起的 SSO

## <a name="adding-litmus-from-the-gallery"></a>从库中添加 Litmus

若要配置 Litmus 与 Azure AD 的集成，需要从库中将 Litmus 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Litmus”   。
1. 从结果面板中选择“Litmus”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-litmus"></a>配置并测试 Litmus 的 Azure AD SSO

使用名为 B.Simon  的测试用户配置并测试 Litmus 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Litmus 相关用户之间建立关联。

若要配置并测试 Litmus 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Litmus SSO](#configure-litmus-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Litmus 测试用户](#create-litmus-test-user)** - 在 Litmus 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Litmus”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成  。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://litmus.com/sessions/new`

1. 单击“ **保存**”。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(原始)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificateraw.png)

1. 在“设置 Litmus”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 B.Simon 访问 Litmus 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Litmus”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-litmus-sso"></a>配置 Litmus SSO

1. 若要在 Litmus 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录”浏览器扩展 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Litmus”，此时会将你定向到 Litmus 应用程序。 在此处，提供管理员凭据以登录到 Litmus。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Litmus，请在另一个 Web 浏览器窗口中，以管理员身份登录到 Litmus 公司站点。

1. 在左侧导航面板中单击“安全性”。 

    ![屏幕截图显示选择了“安全”项。](./media/litmus-tutorial/security-img.png)

1. 在“配置 SAML 身份验证”  部分中，执行以下步骤：

    ![屏幕截图显示了“配置 SAML 身份验证”部分，你可以在其中输入所述值。](./media/litmus-tutorial/configure1.png)

    a. 打开“启用 SAML”  开关。

    b. 对于提供者，选择“通用”  。

    c. 输入标识提供者的名称  。 例如： `Azure AD`

1. 执行以下步骤：

    ![屏幕截图显示了你可以在其中输入所述值的部分。](./media/litmus-tutorial/configure3.png)

    a. 在“SAML 2.0 终结点(HTTP)”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。  

    b. 在记事本中打开从 Azure 门户下载的证书文件，将内容粘贴到“X.509 证书”文本框中。  

    c. 单击“保存 SAML 设置”  。

### <a name="create-litmus-test-user"></a>创建 Litmus 测试用户

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Litmus 应用程序。

1. 在左侧导航面板中单击“帐户”。 

    ![屏幕截图显示选择了“帐户”项。](./media/litmus-tutorial/accounts-img.png)

1. 单击“添加新用户”选项卡。 

    ![屏幕截图显示选择了“添加新用户”项。](./media/litmus-tutorial/add-new-user.png)

1. 在“添加用户”  部分中，执行以下步骤：

    ![屏幕截图显示了“添加新用户”部分，你可以在其中输入所述值。](./media/litmus-tutorial/user-profile.png)

    a. 在“电子邮件”文本框中，输入用户的电子邮件地址，如 B.Simon\@contoso.com 

    b. 在“名字”文本框中，输入用户的名字，例如 B。  

    c. 在“姓氏”文本框中，输入用户的姓氏，例如 Simon。  

    d. 单击“创建用户”  。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Litmus 登录 URL，可以从那里启动登录流。

* 直接转到 Litmus 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应该会自动登录到为其设置了 SSO 的 Litmus

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Litmus 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Litmus。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Litmus 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。