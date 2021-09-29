---
title: 教程：Azure Active Directory 与 SAML SSO for Confluence by resolution GmbH 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAML SSO for Confluence by resolution GmbH 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 8835f32b9a4960587eeb8a15545b3a2062661230
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124765279"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>教程：Azure Active Directory 与 SAML SSO for Confluence by resolution GmbH 集成

本教程介绍如何将 SAML SSO for Confluence by resolution GmbH 与 Azure Active Directory (Azure AD) 集成。 将 SAML SSO for Confluence by resolution GmbH 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 SAML SSO for Confluence by resolution GmbH。
* 让用户能够使用其 Azure AD 帐户自动登录到 SAML SSO for Confluence by resolution GmbH。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 SAML SSO for Confluence by resolution GmbH 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SAML SSO for Confluence by resolution GmbH 支持 **SP 和 IDP** 发起的 SSO

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>从库中添加 SAML SSO for Confluence by resolution GmbH

要配置 SAML SSO for Confluence by resolution GmbH 的集成（集成到 Azure AD 中），需从库中将 SAML SSO for Confluence by resolution GmbH 添加到托管 SaaS 应用的列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“SAML SSO for Confluence by resolution GmbH” 。
1. 从结果面板中选择“SAML SSO for Confluence by resolution GmbH”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>配置并测试 SAML SSO for Confluence by resolution GmbH 的 Azure AD SSO

使用名为“B.Simon”的测试用户为 SAML SSO for Confluence by resolution GmbH 配置和测试 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 SAML SSO for Confluence by resolution GmbH 相关用户之间建立关联。

若要为 SAML SSO for Confluence by resolution GmbH 配置和测试 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. [配置 SAML SSO for Confluence by resolution GmbH SSO](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso) - 在应用程序端配置单一登录设置。
    1. **[创建 SAML SSO for Confluence by resolution GmbH 测试用户](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** - 在 SAML SSO for Confluence by resolution GmbH 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“SAML SSO for Confluence by resolution GmbH”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 若要获取这些值，请联系 [SAML SSO for Confluence by resolution GmbH 客户支持团队](https://www.resolution.de/go/support)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)


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

在本部分中，你将通过授予 B.Simon 访问 SAML SSO for Confluence by resolution GmbH 的权限，允许该用户使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“SAML SSO for Confluence by resolution GmbH”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>配置 SAML SSO for Confluence by resolution GmbH SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 **SAML SSO for Confluence by resolution GmbH 管理门户**。

2. 将鼠标悬停在小齿轮上，并单击“外接程序”  。
    
    ![显示已选择齿轮图标并从下拉列表中选择了“加载项”的屏幕截图。](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. 系统会你将重定向到“管理员访问权限”页。 输入密码  ，并单击“确认”按钮。

    ![显示选择了“确认”按钮的“管理员访问”页的屏幕截图。](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. 在“ATLASSIAN MARKETPLACE”  选项卡下，单击“查找新外接程序”  。 

    ![显示选择了“查找新加载项”的“Attlassian 市场”选项卡的屏幕截图。](./media/saml-sso-confluence-tutorial/add-on.png)

5. 搜索“SAML Single Sign On (SSO) for Confluence”，并单击“安装”按钮安装新的 SAML 插件。  

    ![显示“查找新加载项”页的屏幕截图，其中搜索框中显示了“Confluence 的 SAML 单一登录 (SSO)”并选择了“安装”按钮。](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. 插件安装随即开始。 单击“关闭”  。

    ![显示“正在安装”对话框的屏幕截图。](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![屏幕截图显示选择了“关闭”操作的 “已安装并准备就绪!”对话框。](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  单击“管理”。

    ![显示选择了“管理”按钮的“Confluence 的 SAML 单一登录 (SSO)”应用页的屏幕截图。](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. 单击“配置”配置新的插件。

    ![显示“管理”页的屏幕截图，其中“配置”按钮处于选中状态。](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. 也可在“用户和安全”  选项卡下找到此新插件。

    ![显示“用户和安全”选项卡的屏幕截图，其中选择了“SAML SingleSignOn”。](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. 在“SAML 单一登录插件配置”页上单击“添加新 IdP”按钮，配置标识提供者的设置。

    ![显示选择了“添加新 IdP”按钮的“SAML SingleSignOn 插件配置”页的屏幕截图。](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. 在“选择 SAML 标识提供者”页上，执行以下步骤：

    ![显示“选择 SAML 标识提供者”页的屏幕截图，其中突出显示了“IdP 类型”、“名称”和“描述”文本框。](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. 设置“Azure AD”作为 IdP 类型。
    
    b. 添加标识提供者（例如 Azure AD）的“名称”。
    
    c. 添加标识提供者（例如 Azure AD）的“说明”。
    
    d. 单击“下一步”。
    
12. 在“标识提供者配置”页上，单击“下一步”按钮。

    ![显示选择了“下一步”按钮的“标识提供者配置”页的屏幕截图。](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. 在“导入 SAML IdP 元数据”页上，执行以下步骤：

    ![显示“导入 SAML IdP 元数据”页的屏幕截图，其中选择了“导入”、“加载文件”和“下一步”按钮。](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. 单击“加载文件”按钮，然后选择在步骤 5 中下载的元数据 XML 文件。

    b. 单击“导入”按钮。
    
    c. 在导入成功之前，请等待片刻。
    
    d. 单击“下一步”按钮。
    
14. 在“用户 ID 属性和转换”页上，单击“下一步”按钮。

    ![显示选择了“下一步”按钮的“用户 ID 属性和转换”页的屏幕截图。](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. 在“用户创建和更新”页上，单击“保存并下一步”保存设置。   
    
    ![显示选择了“保存并进行下一步”按钮的“用户创建和更新”页的屏幕截图。](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. 在“测试设置”页上，单击“跳过测试并手动配置”暂时跳过用户测试。 此测试将在下一部分中执行，并需要在 Azure 门户中进行某些设置。 
    
    ![显示选择了“跳过测试并手动配置”按钮的“测试设置”页的屏幕截图。](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. 在出现的显示“跳过测试意味着...”对话框中，单击“确定”。
    
    ![配置单一登录](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>创建 SAML SSO for Confluence by resolution GmbH 测试用户

要使 Azure AD 用户能够登录 SAML SSO for Confluence by resolution GmbH，必须将其预配到 SAML SSO for Confluence by resolution GmbH 中。  
在 SAML SSO for Confluence by resolution GmbH 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 SAML SSO for Confluence by resolution GmbH 公司站点。

2. 将鼠标悬停在小齿轮上，并单击“用户管理”  。

    ![显示已选择齿轮图标并从菜单中选择了“用户管理”的屏幕截图。](./media/saml-sso-confluence-tutorial/user-1.png) 

3. 在“用户”部分，单击“添加用户”选项卡。在“添加用户”对话框页上，执行以下步骤：

    ![添加员工](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. 在“用户名”文本框中，键入用户（例如 Britta Simon）的电子邮件地址。

    b. 在“全名”文本框中，键入用户（例如 Britta Simon）的全名。 

    c. 在“电子邮件”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    d. 在“密码”文本框中，键入 Britta Simon 的密码。

    e. 单击“确认密码”，重新输入该密码。
    
    f. 单击“添加”按钮。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 SAML SSO for Confluence by resolution GmbH 登录 URL，可以从那里启动登录流。  

* 直接转到 SAML SSO for Confluence by resolution GmbH 登录 URL，从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”，你应该会自动登录到为其设置了 SSO 的 SAML SSO for Confluence by resolution GmbH 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 当你在“我的应用”中单击 SAML SSO for Confluence by resolution GmbH 磁贴时，如果这是在 SP 模式下配置的，系统会将你重定向到应用程序登录页来启动登录流；如果这是在 IDP 模式下配置的，系统会让你自动登录到为其设置了 SSO 的 SAML SSO for Confluence by resolution GmbH。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

在配置 SAML SSO for Confluence by resolution GmbH 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。