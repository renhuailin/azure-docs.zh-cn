---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 AskYourTeam 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 AskYourTeam 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 67907689f68d034ef3d098d6843c79a619a0ed58
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748107"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>教程：Azure Active Directory 单一登录 (SSO) 与 AskYourTeam 的集成

本教程介绍如何将 AskYourTeam 与 Azure Active Directory (Azure AD) 集成。 将 AskYourTeam 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 AskYourTeam。
* 让用户使用其 Azure AD 帐户自动登录到 AskYourTeam。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 AskYourTeam 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* AskYourTeam 支持 **SP 和 IDP** 发起的 SSO

## <a name="adding-askyourteam-from-the-gallery"></a>从库中添加 AskYourTeam

若要配置 AskYourTeam 与 Azure AD 的集成，需要从库中将 AskYourTeam 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **AskYourTeam**。 
1. 在结果面板中选择“AskYourTeam”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-askyourteam"></a>配置并测试 AskYourTeam 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 AskYourTeam 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 AskYourTeam 中的相关用户之间建立链接关系。

若要配置并测试 AskYourTeam 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 AskYourTeam SSO](#configure-askyourteam-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 AskYourTeam 测试用户](#create-askyourteam-test-user)** - 在 AskYourTeam 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“AskYourTeam”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback` 

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > 这些不是实际值。 使用本教程稍后将会解释的实际“回复 URL”和“登录 URL”值来更新这些值。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 AskYourTeam”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 AskYourTeam 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“AskYourTeam”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-askyourteam-sso"></a>配置 AskYourTeam SSO

1. 若要在 AskYourTeam 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录”浏览器扩展 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 AskYourTeam”会将你定向到 Zoom 应用程序。 在此处，提供管理员凭据以登录到 AskYourTeam。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

3. 如果要手动设置 AskYourTeam，请在另一个 Web 浏览器窗口中，以管理员身份登录到 AskYourTeam 公司站点。

1. 单击“我的组织”。

    ![显示“我的组织”链接的屏幕截图。](./media/askyourteam-tutorial/user1.png)

1. 单击“集成”。 

    ![显示“集成”链接的屏幕截图。](./media/askyourteam-tutorial/configure1.png)

1. 单击“编辑设置”。 

    ![显示带有“编辑设置”按钮的“单一登录”消息的屏幕截图。](./media/askyourteam-tutorial/configure2.png)

1. 在“编辑单一登录集成”页上执行以下步骤：  

    ![显示“编辑单一登录集成”部分的屏幕截图，可以在其中输入此步骤的值。](./media/askyourteam-tutorial/configure3.png)

    a. 在“SAML 单一登录服务 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。  

    b. 在“SAML 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。  

    c. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。  

    d. 在记事本中打开从 Azure 门户下载的“证书(Base64)”，将内容粘贴到“SAML 签名证书 - Base64”文本框中。  

    > [!NOTE]
    > 或者，也可以单击“选择文件”选项上传“联合元数据 XML”文件。  

    e. 复制“回复 URL (断言使用者服务 URL)”值，并将此值粘贴到 Azure 门户的“基本 SAML 配置”部分的“回复 URL”文本框中。   

    f. 复制“登录 URL”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“登录 URL”文本框中。   

    g. 单击“ **保存**”。

### <a name="create-askyourteam-test-user"></a>创建 AskYourTeam 测试用户

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 AskYourTeam 网站。

1. 单击“我的组织”。

    ![显示“我的组织”链接的屏幕截图，可在其中开始该任务。](./media/askyourteam-tutorial/user1.png)

1. 单击“用户”并选择“新建用户”。  

    ![显示具有“新建用户”选项的“用户”链接的屏幕截图。](./media/askyourteam-tutorial/user2.png)

1. 在“新建用户”部分执行以下步骤： 

    ![显示“新建用户”部分的屏幕截图，可在其中输入用户信息。](./media/askyourteam-tutorial/user3.png)

    1. 在“名字”文本框中输入用户的名字。 

    1. 在“姓氏”文本框中输入用户的姓氏。 

    1. 在“电子邮件”文本框中输入用户的电子邮件地址，例如 B.Simon@contoso.com。 

    1. 根据组织要求选择用户的“角色”。 

    1. 单击“ **保存**”。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 AskYourTeam 登录 URL，可在其中启动登录流。

* 直接转到 AskYourTeam 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 AskYourTeam

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 AskYourTeam 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 AskYourTeam。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 AskYourTeam 后，可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。