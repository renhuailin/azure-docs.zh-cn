---
title: 教程：Azure Active Directory 与 Wdesk 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Wdesk 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: 4d5c472e144fff28c6cc02c99cbfe4e34ff5aafa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731581"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Wdesk 集成

本教程介绍如何将 Wdesk 与 Azure Active Directory (Azure AD) 集成。 将 Wdesk 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Wdesk。
* 允许用户使用其 Azure AD 帐户自动登录到 Wdesk。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Wdesk 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Wdesk 支持 **SP** 和 **IDP** 发起的 SSO。

## <a name="add-wdesk-from-the-gallery"></a>从库中添加 Wdesk

若要配置 Wdesk 与 Azure AD 的集成，需要从库中将 Wdesk 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Wdesk”   。
1. 从结果面板中选择“Wdesk”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>配置并测试 Wdesk 的 Azure AD SSO

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Wdesk 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Wdesk 相关用户之间建立链接关系。

若要配置并测试 Wdesk 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Wdesk SSO](#configure-wdesk-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Wdesk 测试用户](#create-wdesk-test-user)** - 在 Wdesk 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在Azure 门户中的“Wdesk”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 配置 SSO 时，你会从 WDesk 门户获得这些值。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Wdesk”部分，根据要求复制相应的 URL。 

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

本部分将通过授予 B.Simon 访问 Wdesk 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Wdesk”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-wdesk-sso"></a>配置 Wdesk SSO

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Wdesk。

1. 在左下角，单击“管理员”，然后选择“管理员帐户”   ：
 
    ![显示从“管理员”菜单选择了“帐户管理员”的屏幕截图。](./media/wdesk-tutorial/account.png)

1. 在 Wdesk 管理员中，导航到“安全性”、“SAML”、“SAML 设置”    >   ：

    ![显示从“SAML”选项卡选择了“SAML 设置”的屏幕截图。](./media/wdesk-tutorial/settings.png)

1. 在“SAML 用户 ID 设置”下，勾选“SAML 用户 ID 为 Wdesk 用户名”。  

    ![显示“SAML 用户 ID 设置”的屏幕截图，可在其中选择“SAML 用户 ID 为 Wdesk 用户名”。](./media/wdesk-tutorial/wdesk-username.png)

4. 在“常规设置”下，选中“启用 SAML 单一登录”   ：

    ![显示“编辑 SAML 设置”的屏幕截图，可在其中选择“启用 SAML 单一登录”。](./media/wdesk-tutorial/user-settings.png)

5. 在“服务提供程序详细信息”下，执行以下步骤  ：

    ![显示“服务提供程序详细信息”的屏幕截图，你可以在其中输入所述值。](./media/wdesk-tutorial/service-provider.png)

    1. 复制登录 URL 并将其粘贴到 Azure 门户的“登录 URL”文本框   。

    1. 复制元数据 URL 并将其粘贴到 Azure 门户的“标识符”文本框   。

    1. 复制使用者 URL 并将其粘贴到 Azure 门户的“回复 URL”文本框   。

    1. 单击 Azure 门户的“保存”以保存更改  。      

1. 单击“配置 IdP 设置”以打开“编辑 IdP 设置”对话框   。 单击“选择文件”以查找从 Azure 门户保存的 Metadata.xml 文件，然后将其上传   。
    
    ![显示“编辑 IdP 设置”的屏幕截图，可在其中上传元数据。](./media/wdesk-tutorial/metadata.png)
  
1. 单击“保存更改”。 

    ![显示“保存更改”按钮的屏幕截图。](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>创建 Wdesk 测试用户

要使 Azure AD 用户能够登录到 Wdesk，必须将其预配到 Wdesk 中。 在“Wdesk”中，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 Wdesk。

2. 导航到“管理员” > “管理员帐户”   。

     ![显示从“管理员”菜单选择了“帐户管理员”的屏幕截图。](./media/wdesk-tutorial/account.png)

3. 单击“人员”下的“成员”   。

4. 现在请单击“添加成员”以打开“添加成员”对话框   。 
   
    ![显示可在其中选择“添加成员”的“成员”选项卡的屏幕截图。](./media/wdesk-tutorial/create-user-1.png)  

5. 在“用户”文本框中输入用户的用户名（例如 b.simon@contoso.com），然后单击“继续”按钮。  

    ![显示可在其中输入用户的“添加成员”对话框的屏幕截图。](./media/wdesk-tutorial/create-user-3.png)

6.  输入详细信息，如下所示：
  
    ![显示可在其中为用户添加“基本信息”的“添加成员”对话框的屏幕截图。](./media/wdesk-tutorial/create-user-4.png)
 
    a. 在“电子邮件”文本框中输入用户的电子邮件，例如 b.simon@contoso.com。 

    b. 在“名字”文本框中，输入用户的名字，例如 B   。

    c. 在“姓氏”文本框中，输入用户的名字，如 Simon   。

7. 单击“保存成员”按钮  。  

    ![显示包含“保存成员”按钮的“发送欢迎电子邮件”的屏幕截图。](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Wdesk 登录 URL，可以从那里启动登录流。  

* 直接转到 Wdesk 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Wdesk。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Wdesk 磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Wdesk。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Wdesk 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。