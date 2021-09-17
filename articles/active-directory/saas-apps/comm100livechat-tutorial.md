---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Comm100 Live Chat 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Comm100 Live Chat 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: jeedes
ms.openlocfilehash: 6527666bcc3f70e1386a857a921b83301f36645c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769483"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Comm100 Live Chat 集成

本教程介绍如何将 Comm100 Live Chat 与 Azure Active Directory (Azure AD) 集成。 将 Comm100 Live Chat 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Comm100 Live Chat。
* 让用户使用其 Azure AD 帐户自动登录到 Comm100 Live Chat。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Comm100 Live Chat 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Comm100 Live Chat 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-comm100-live-chat-from-the-gallery"></a>从库中添加 Comm100 Live Chat

要配置 Comm100 Live Chat 与 Azure AD 的集成，需要从库中将 Comm100 Live Chat 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Comm100 Live Chat”   。
1. 从结果面板中选择“Comm100 Live Chat”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-comm100-live-chat"></a>配置并测试 Comm100 Live Chat 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Comm100 Live Chat 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Comm100 Live Chat 相关用户之间建立链接关系。

若要配置并测试 Comm100 Live Chat 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
   1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
   1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Comm100 Live Chat SSO](#configure-comm100-live-chat-sso)** - 在应用程序端配置单一登录设置。
   1. **[创建 Comm100 Live Chat 测试用户](#create-comm100-live-chat-test-user)** - 在 Comm100 Live Chat 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Comm100 Live Chat”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > 登录 URL 值不是实际值。 需使用实际登录 URL 更新“登录 URL”值（本教程稍后将会介绍）。

1. Comm100 Live Chat 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/edit-attribute.png)

1. 除了上述属性，Comm100 Live Chat 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 |  源属性|
    | ---------------| --------------- |
    |   电子邮件    | user.mail |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Comm100 Live Chat”  部分，根据要求复制相应的 URL。

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

在本部分，我们通过授予 B.Simon 访问 Comm100 Live Chat 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Comm100 Live Chat”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-comm100-live-chat-sso"></a>配置 Comm100 Live Chat SSO

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Comm100 Live Chat。

1. 单击页面右上角的“我的帐户”  。

   ![Comm100 Live Chat - 我的帐户。](./media/comm100livechat-tutorial/account.png)

1. 在菜单左侧单击“安全性”，然后单击“代理单一登录”。  

   ![显示左侧帐户菜单的屏幕截图，其中突出显示了“安全性”和“代理单一登录”。](./media/comm100livechat-tutorial/security.png)

1. 在“代理单一登录”  页上，执行以下步骤：

   ![Comm100 Live Chat - 安全性。](./media/comm100livechat-tutorial/certificate.png)

   a. 复制第一个突出显示的链接，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“登录 URL”文本框中   。

   b. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

   c. 在“远程注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

   d. 单击“选择文件”，将从 Azure 门户下载的 base-64 编码证书上传到“证书”中。  

   e. 单击 **“保存更改”** 。

### <a name="create-comm100-live-chat-test-user"></a>创建 Comm100 Live Chat 测试用户

为了使 Azure AD 用户能够登录 Comm100 Live Chat，必须将其预配到 Comm100 Live Chat 中。 在 Comm100 Live Chat 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 Comm100 Live Chat。

2. 单击页面右上角的“我的帐户”  。

    ![Comm100 Live Chat - 我的帐户。](./media/comm100livechat-tutorial/account.png)

3. 从菜单左侧单击“代理”，然后单击“新建代理”。  

    ![Comm100 Live Chat - 代理。](./media/comm100livechat-tutorial/agent.png)

4. 在“新建代理”页上执行以下步骤： 

    ![Comm100 Live Chat - 新建代理。](./media/comm100livechat-tutorial/new-agent.png)

    a. a. 在“电子邮件”文本框中，输入用户的电子邮件，例如 B.simon\@contoso.com   。

    b. 在“名字”文本框中，输入用户的名字，例如 B   。

    c. 在“姓氏”文本框中，输入用户的姓氏，例如 **simon**。 

    d. 在“显示名称”文本框中，输入用户的显示名称，例如 B.Simon   。

    e. 在“密码”文本框中键入密码。 

    f. 单击“ **保存**”。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Comm100 Live Chat 登录 URL，你可以在那里启动登录流。 

* 直接转到 Comm100 Live Chat 登录 URL，从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Comm100 Live Chat 磁贴时，会重定向到 Comm100 Live Chat 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Comm100 Live Chat 后，即可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。