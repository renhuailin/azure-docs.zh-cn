---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Evernote 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Evernote 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2021
ms.author: jeedes
ms.openlocfilehash: 69785f135ff79c089d5d38e2309ec9c111588341
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779134"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Evernote 的集成

本教程介绍如何将 Evernote 与 Azure Active Directory (Azure AD) 集成。 将 Evernote 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Evernote。
* 让用户使用其 Azure AD 帐户自动登录到 Evernote。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Evernote 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Evernote 支持 SP 和 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-evernote-from-the-gallery"></a>从库中添加 Evernote

要配置 Evernote 与 Azure AD 的集成，需要将库中的 Evernote 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Evernote”   。
1. 从结果面板中选择“Evernote”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-evernote"></a>配置并测试 Evernote 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 Evernote 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Evernote 相关用户之间建立链接关系。

若要配置并测试 Evernote 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Evernote SSO](#configure-evernote-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Evernote 测试用户](#create-evernote-test-user)** - 在 Evernote 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Evernote”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤 ： 

    在“标识符”文本框中键入 URL：`https://www.evernote.com/saml2`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：  `https://www.evernote.com/Login.action`

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

7. 若要修改“签名”  选项，请单击“编辑”  按钮，打开“SAML 签名证书”  对话框。

    ![显示已选择“编辑”按钮的“SAML 签名证书”对话框的屏幕截图。](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/assertion.png)

    a. 选择“为 SAML 响应和断言签名”选项作为“签名选项”   。

    b. 单击“保存” 

1. 在“设置 Evernote”部分中，根据要求复制相应的 URL  。

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

在本部分中，将通过授予 B.Simon 访问 Evernote 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Evernote”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-evernote-sso"></a>配置 Evernote SSO

1. 若要在 Evernote 中自动执行配置，需要通过单击“安装扩展”  来安装 **我的应用安全登录浏览器扩展**。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将该扩展添加到浏览器后，单击“设置 Evernote”定向到 Evernote 应用程序。  在此处提供管理员凭据以登录到 Evernote。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Evernote，请打开新的 Web 浏览器窗口，以管理员身份登录到 Evernote 公司站点，并执行以下步骤：

4. 转到“管理控制台” 

    ![Admin-Console](./media/evernote-tutorial/admin.png)

5. 在“管理控制台”中，转到“安全性”并选择“单一登录”   

    ![SSO-Setting](./media/evernote-tutorial/security.png)

6. 配置以下值：

    ![Certificate-Setting](./media/evernote-tutorial/certificate.png)
    
    a.  启用 SSO：  SSO 默认已启用（单击“禁用单一登录”可以取消 SSO 要求） 

    b. 在“SAML HTTP 请求 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. 在记事本中打开从 Azure AD 下载的证书，并复制包括“BEGIN CERTIFICATE”和“END CERTIFICATE”在内的内容并将其粘贴到“X.509 证书”文本框中  。 

    d. 单击“保存更改” 

### <a name="create-evernote-test-user"></a>创建 Evernote 测试用户

为了使 Azure AD 用户能够登录到 Evernote，必须将其预配到 Evernote 中。  
对于 Evernote，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Evernote 公司站点。

2. 单击“管理控制台”。 

    ![Admin-Console](./media/evernote-tutorial/admin.png)

3. 在“管理控制台”中，转到“添加用户”。  

    ![显示选中了“添加用户”的“用户”菜单的屏幕截图。](./media/evernote-tutorial/create-user.png)

4. 在“电子邮件”文本框中的“添加团队成员”内，键入用户帐户的电子邮件地址，并单击“邀请”。   

    ![Add-testUser](./media/evernote-tutorial/add-user.png)
    
5. 发送邀请后，Azure Active Directory 帐户持有人将收到邀请电子邮件。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这将重定向到 Evernote 登录 URL，从那里启动登录流。  

* 直接转到 Evernote 登录 URL，从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你会自动登录到为其设置了 SSO 的 Evernote。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 单击“我的应用”中的 Evernote 磁贴时，如果该应用程序是在 SP 模式下配置的，系统会将你重定向到应用程序登录页来启动登录流；如果它是在 IDP 模式下配置的，你会自动登录到为其设置了 SSO 的 Evernote。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Evernote 后，你可以强制实施会话控制，实时防止组织的敏感数据遭到外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。