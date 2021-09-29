---
title: 教程：Azure Active Directory 与 Mimecast Admin Console 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Mimecast Admin Console 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 79b583f51349378a40512e9e79763ec3ae3a192f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124795329"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-admin-console"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Mimecast Admin Console 集成

本教程介绍如何将 Mimecast Admin Console 与 Azure Active Directory (Azure AD) 集成。 将 Mimecast Admin Console 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Mimecast Admin Console。
* 让用户使用其 Azure AD 帐户自动登录到 Mimecast Admin Console。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Mimecast Admin Console 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Mimecast Admin Console 支持 SP 和 IDP 发起的 SSO

## <a name="add-mimecast-admin-console-from-the-gallery"></a>从库中添加 Mimecast Admin Console

要配置 Mimecast Admin Console 与 Azure AD 的集成，需要从库中将 Mimecast Admin Console 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 Mimecast Admin Console。 
1. 在结果面板中选择“Mimecast Admin Console”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-mimecast-admin-console"></a>配置并测试 Mimecast Admin Console 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Mimecast Admin Console 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Mimecast Admin Console 中的相关用户之间建立链接关系。

若要配置并测试 Mimecast Admin Console 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Mimecast Admin Console SSO](#configure-mimecast-admin-console-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Mimecast Admin Console 测试用户](#create-mimecast-admin-console-test-user) - 在 Mimecast Admin Console 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 Mimecast Admin Console 应用程序集成页面上，找到“管理”部分，然后选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤：

    a. 在“标识符”文本框中，使用以下模式键入 URL：

    | 区域  |  值 | 
    | --------------- | --------------- |
    | 欧洲          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | United States   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | 南非    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | 澳大利亚       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | 海外        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > 你将在“帐户” > “设置” > “帐户代码”下找到 Mimecast Admin Console 中的 `accountcode` 值。 向标识符追加 `accountcode`。

    b. 在“回复 URL”文本框中，键入 URL： 

    | 区域  |  值 | 
    | --------------- | --------------- | 
    | 欧洲          | `https://eu-api.mimecast.com/login/saml`|
    | United States   | `https://us-api.mimecast.com/login/saml`|
    | 南非    | `https://za-api.mimecast.com/login/saml`|
    | 澳大利亚       | `https://au-api.mimecast.com/login/saml`|
    | 海外        | `https://jer-api.mimecast.com/login/saml`|

1. 如果要在“SP”发起的模式下配置应用程序：

    在“登录 URL”文本框中，键入 URL： 

    | 区域  |  值 | 
    | --------------- | --------------- | 
    | 欧洲          | `https://login-eu.mimecast.com/administration/app/#/administration-dashboard`|
    | United States   | `https://login-us.mimecast.com/administration/app/#/administration-dashboard`|
    | 南非    | `https://login-za.mimecast.com/administration/app/#/administration-dashboard`|
    | 澳大利亚       | `https://login-au.mimecast.com/administration/app/#/administration-dashboard`|
    | 海外        | `https://login-jer.mimecast.com/administration/app/#/administration-dashboard`|

1. 单击“ **保存**”。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分，将通过授予 B.Simon 访问 Mimecast Admin Console 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Mimecast Admin Console”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-mimecast-admin-console-sso"></a>配置 Mimecast Admin Console SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Mimecast Administration Console。

1. 导航到“管理” > “服务” > “应用程序”。

    ![屏幕截图显示选择了“应用程序”的“Mimecast”窗口。](./media/mimecast-admin-console-tutorial/services.png)

1. 单击“身份验证配置文件”选项卡。
    
    ![屏幕截图显示选择了“身份验证配置文件”的“应用程序”选项卡。](./media/mimecast-admin-console-tutorial/authentication-profiles.png)

1. 单击“新建身份验证配置文件”选项卡。

    ![屏幕截图显示选择了“新建身份验证配置文件”。](./media/mimecast-admin-console-tutorial/new-authenticatio-profile.png)

1. 在“说明”文本框中提供有效说明，并选中“为管理控制台强制执行 SAML 身份验证”复选框。

    ![屏幕截图显示了“为管理控制台强制执行 SAML 身份验证”的位置。](./media/mimecast-admin-console-tutorial/selecting-admin-consle.png)

1. 在“管理控制台的 SAML 配置”页面上，执行以下步骤：

    ![屏幕截图显示了“管理控制台的 SAML 配置”页面，你可以在其中输入所述值。](./media/mimecast-admin-console-tutorial/sso-settings.png)

    a. 对于“提供程序”，从下拉菜单中选择“Azure Active Directory” 。

    b. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”值。 

    c. 单击“导入”。 导入元数据 URL 之后，字段将自动填充，无需对这些字段执行任何操作。

    d. 确保取消选中“使用受密码保护上下文”和“使用集成身份验证上下文”复选框。

    e. 单击“ **保存**”。

### <a name="create-mimecast-admin-console-test-user"></a>创建 Mimecast Admin Console 测试用户

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Mimecast Administration Console。

1. 导航到“管理” > “目录” > “内部目录”。

    ![屏幕截图显示选择了“内部目录”的“Mimecast”窗口。](./media/mimecast-admin-console-tutorial/internal-directories.png)

1. 如果下面提及你的域，请选择该域，否则请通过单击“新域”来创建新域。

    ![屏幕截图显示选择了域。](./media/mimecast-admin-console-tutorial/domain-name.png)

1. 单击“新地址”选项卡。

    ![屏幕截图显示选择了“新建地址”。](./media/mimecast-admin-console-tutorial/new-address.png)

1. 在以下页面提供所需用户信息：

    ![屏幕截图显示了可以在其中输入所述值的页。](./media/mimecast-admin-console-tutorial/user-information.png)

    a. 在“电子邮件地址”文本框中，输入用户的电子邮件地址，例如 `B.Simon@yourdomainname.com`。

    b. 在“全局名称”文本框中，输入用户的全名 。

    c. 在“密码”和“确认密码”文本框中，输入用户的密码。

    d. 选中“登录时强制更改”复选框。

    e. 单击“ **保存**”。

    f. 若要将角色分配给用户，请单击“角色编辑”，并根据组织要求向用户分配所需角色。

    ![屏幕截图显示了“地址设置”，你可以在其中选择“角色编辑”。](./media/mimecast-admin-console-tutorial/assign-role.png)

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Mimecast Admin Console 登录 URL，可从其中启动登录流。  

* 直接转到 Mimecast Admin Console 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Mimecast Admin Console 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Mimecast Admin Console 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Mimecast Admin Console。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Mimecast Admin Console 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。