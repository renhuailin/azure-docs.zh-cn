---
title: 教程：Azure Active Directory 与 Help Scout 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Help Scout 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2021
ms.author: jeedes
ms.openlocfilehash: 69d40711d74145ae911994f96132b504a034e498
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124826281"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>教程：Azure Active Directory 与 Help Scout 集成

本教程介绍如何将 Help Scout 与 Azure Active Directory (Azure AD) 集成。 将 Help Scout 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Help Scout。
* 让用户使用其 Azure AD 帐户自动登录到 Help Scout。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

要配置 Azure AD 与 Help Scout 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 Help Scout 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Help Scout 支持 SP 和 IDP 发起的 SSO。
* Help Scout 支持实时用户预配。

## <a name="add-help-scout-from-the-gallery"></a>从库中添加 Help Scout

要配置 Help Scout 与 Azure AD 的集成，需要从库中将 Help Scout 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Help Scout”   。
1. 从结果面板中选择“Help Scout”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-help-scout"></a>配置并测试 Help Scout 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Help Scout 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Help Scout 相关用户之间建立关联。

若要配置并测试 Help Scout 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Help Scout SSO](#configure-help-scout-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Help Scout 测试用户](#create-help-scout-test-user)** - 在 Help Scout 中创建 B.Simo 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Help Scout”应用程序集成页上，找到“管理”部分，并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    a. “标识符”是 Help Scout 的“受众 URI (服务提供程序实体 ID)”，以 `urn:` 开头

    b. “回复 URL”是 Help Scout 的“发回 URL (断言使用者服务 URL)”，以 `https://` 开头 

    > [!NOTE]
    > 这些 URL 中的值仅用于演示。 需要使用实际回复 URL 和标识符更新这些值。 可从“身份验证”部分下面的“单一登录”选项卡获取这些值，本教程稍后将会介绍。 

1. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://secure.helpscout.net/members/login/`

1. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Help Scout”部分中，根据要求复制相应 URL  。

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

在本部分，你将通过授予 B.Simon 访问 Help Scout 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Help Scout”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-help-scout-sso"></a>配置 Help Scout SSO

1. 若要在 Help Scout 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 Help Scout”，此时系统会将你定向到 Help Scout 应用程序  。 请在此处提供管理员凭据以登录到 Help Scout。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 Scout，请打开新的 Web 浏览器窗口，以管理员身份登录 Help Scout 公司站点，并执行以下步骤：

1. 在顶部菜单中单击“管理”，并从下拉菜单中选择“公司”。  

    ![显示选择了“公司”的“管理”菜单的屏幕截图。](./media/helpscout-tutorial/settings.png)

1. 从左侧导航窗格中选择“身份验证”。 

    ![显示选择了“身份验证”的屏幕截图。](./media/helpscout-tutorial/authentication.png)

1. 随后会转到“SAML 设置”部分，请执行以下步骤：

    ![显示可在其中输入指定信息的“单一登录”选项卡的屏幕截图。](./media/helpscout-tutorial/configuration.png)

    a. 复制“发回 URL (断言使用者服务 URL)”值，并将其粘贴到 Azure 门户的“基本 SAML 配置”部分的“回复 URL”文本框中。   

    b. 复制“受众 URL (服务提供商实体 ID)”值，并将其粘贴到 Azure 门户的“基本 SAML 配置”部分的“标识符”文本框中。   

1. 将“启用 SAML”切换为打开状态，并执行以下步骤： 

    ![显示可在其中启用 SAML 并添加其他信息的“单一登录”选项卡的屏幕截图。](./media/helpscout-tutorial/information.png)

    a. 在“单一登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    b. 单击“上传证书”，上传已从 Azure 门户下载的 **证书 (Base64)**。

    c. 在“电子邮件域”文本框中输入组织的电子邮件域，例如 `contoso.com`。  可使用逗号分隔多个域。 每当 Help Scout 用户或管理员在 [Help Scout 登录页](https://secure.helpscout.net/members/login/)上输入该特定域时，都会路由到“标识提供者”，以使用其凭据进行身份验证。

    d. 最后，可以希望用户只能通过此方法登录到 Help Scout，可将“强制 SAML 登录”切换为打开状态。  如果希望用户仍旧使用其 Help Scout 凭据登录，可将此选项切换为关闭状态。 即使启用此选项，帐户所有者也始终能够使用其帐户密码登录到 Help Scout。

    e. 单击“保存”  。

### <a name="create-help-scout-test-user"></a>创建 Help Scout 测试用户

在本部分中，我们将在 Help Scout 中创建一个名为 B.Simon 的用户。 Help Scout 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 Help Scout 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Help Scout 登录 URL，你可以从那里启动登录流。  

* 直接转到 Help Scout 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 Help Scout。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Help Scout 磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Help Scout。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Help Scout 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。