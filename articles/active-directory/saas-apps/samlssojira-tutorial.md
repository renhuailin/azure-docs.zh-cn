---
title: 教程：Azure Active Directory 与 SAML SSO for Jira by Resolution GmbH 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAML SSO for Jira by resolution GmbH 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 79ec9389840582ea7841bfd0edc4bda3a6f16f01
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746564"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>教程：Azure Active Directory 与 SAML SSO for Jira by resolution GmbH 集成

本教程介绍如何将 SAML SSO for Jira by resolution GmbH 与 Azure Active Directory (Azure AD) 集成。 将 SAML SSO for Jira by resolution GmbH 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SAML SSO for Jira by resolution GmbH。
* 让用户通过其 Azure AD 帐户自动登录到 SAML SSO for Jira by resolution GmbH。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 SAML SSO for Jira by resolution GmbH 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SAML SSO for Jira by resolution GmbH 支持 **SP** 和 **IDP** 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>从库添加 SAML SSO for Jira by resolution GmbH

要配置 SAML SSO for Jira by resolution GmbH 的集成（集成到 Azure AD 中），需从库中将 SAML SSO for Jira by resolution GmbH 添加到托管 SaaS 应用的列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“SAML SSO for Jira by resolution GmbH” 。
1. 从结果面板中选择“SAML SSO for Jira by resolution GmbH”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>配置并测试 SAML SSO for Jira by resolution GmbH 的 Azure AD SSO

使用名为“B.Simon”的测试用户为 SAML SSO for Jira by resolution GmbH 配置和测试 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 SAML SSO for Jira by resolution GmbH 相关用户之间建立关联。

若要配置并测试 SAML SSO for Jira by resolution GmbH 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 SAML SSO for Jira by resolution GmbH SSO](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 SAML SSO for Jira by resolution GmbH 测试用户](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** - 在 SAML SSO for Jira by resolution GmbH 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“SAML SSO for Jira by resolution GmbH”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 若要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    c. 若要在 **SP** 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > 对于“标识符”、“回复 URL”和“登录 URL”，请将 \<server-base-url> 替换为 Jira 实例的基 URL。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。 如果遇到问题，请联系 [SAML SSO for Jira by resolution GmbH 客户支持团队](https://www.resolution.de/go/support)。

4. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，下载“联合元数据 XML”并将其保存在计算机上  。

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

在本部分中，你将通过授予 B.Simon 访问 SAML SSO for Jira by resolution GmbH 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“SAML SSO for Jira by resolution GmbH”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>配置 SAML SSO for Jira by resolution GmbH SSO 

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Jira 实例。

2. 将鼠标悬停在右侧的齿轮图标上，然后单击“管理应用”。
    
    ![屏幕截图显示了指向“齿轮”图标的箭头，其中从下拉列表中选择了“管理应用”。](./media/samlssojira-tutorial/add-on-1.png)

3. 如果已重定向到“管理员访问”页，请输入 **密码**，然后单击“确认”按钮。

    ![屏幕截图显示了“管理员访问权限”页。](./media/samlssojira-tutorial/add-on-2.png)

4. Jira 通常会将你重定向到 Atlassian 市场。 如果没有，请单击左面板中的“查找新应用”。 搜索 **SAML Single Sign On (SSO) for JIRA**，然后单击“安装”按钮安装 SAML 插件。

    ![屏幕截图显示了“Atlassian Marketplace for JIRA”页，其中有一箭头指向“SAML 单一登录(SSO) Jira，SAML/SSO”应用的“安装”按钮。](./media/samlssojira-tutorial/store.png)

5. 插件安装随即开始。 完成后，单击“关闭”按钮。

    ![显示“正在安装”对话框的屏幕截图。](./media/samlssojira-tutorial/store-2.png)

    ![屏幕截图显示选择了“关闭”操作的 选择了“关闭”按钮的对话框。](./media/samlssojira-tutorial/store-3.png)

6. 然后单击“管理”。

    ![屏幕截图显示了“SAML 单一登录(SSO) Jira，SAML/SSO”应用，其中选择了“管理”按钮。](./media/samlssojira-tutorial/store-4.png)
    
7. 然后，单击“配置”以配置刚刚安装的插件。

    ![屏幕截图显示了“管理应用”页，其中选择了“Jira 的 SAML 单一登录”应用的“配置”按钮。](./media/samlssojira-tutorial/store-5.png)

8. 在“SAML 单一登录插件配置”向导中，单击“添加新 IdP”，将 Azure AD 配置为新的标识提供者。 

    ![屏幕截图显示了“欢迎”页，其中选择了“新增 IdP”按钮。](./media/samlssojira-tutorial/add-on-4.png) 

9. 在“选择 SAML 标识提供者”页上执行以下步骤：

    ![屏幕截图显示了“选择 SAML 标识提供者”页，其中突出显示了“IdP 类型”和“名称”文本框，并选择了“下一步”按钮。](./media/samlssojira-tutorial/identity-provider.png)
 
    a. 设置“Azure AD”作为 IdP 类型。
    
    b. 添加标识提供者的 **名称**（例如 Azure AD）。
    
    c. （可选）添加标识提供者的 **说明**（例如 Azure AD）。
    
    d. 单击“下一步”。
    
10. 在“标识提供者配置”页上，单击“下一步” 。
 
    ![屏幕截图显示了“标识提供者配置”页。](./media/samlssojira-tutorial/configuration.png)

11. 在“导入 SAML IdP 元数据”页上，执行以下步骤：

    ![屏幕截图显示了“导入 SAML IdP 元数据”页，其中选择了“选择元数据 XML 文件”操作。](./media/samlssojira-tutorial/metadata.png)

    a. 单击“选择元数据 XML 文件”按钮，然后选择前面下载的“联合身份验证元数据 XML”文件。 

    b. 单击“导入”按钮。
     
    c. 等待片刻，让导入成功完成。  
     
    d. 单击“下一步”按钮。
    
12. 在“用户 ID 属性和转换”页上，单击“下一步”按钮。

    ![屏幕截图显示了“用户 ID 属性和转换”页，其中选择了“下一步”按钮。](./media/samlssojira-tutorial/transformation.png)
    
13. 在“用户创建和更新”页上，单击“保存”以保存设置，然后单击“下一步”。 
    
    ![显示选择了“保存并进行下一步”按钮的“用户创建和更新”页的屏幕截图。](./media/samlssojira-tutorial/update.png)
    
14. 在“测试设置”页上，单击“跳过测试并手动配置”以暂时跳过用户测试。 此测试将在下一部分执行，需要在 Azure 门户中进行某些设置。
    
    ![显示选择了“跳过测试并手动配置”按钮的“测试设置”页的屏幕截图。](./media/samlssojira-tutorial/test.png)
    
15. 单击“确定”跳过警告。
    
    ![屏幕截图显示了警告对话框，其中选择了“确定”按钮。](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>创建 SAML SSO for Jira by resolution GmbH 测试用户

要使 Azure AD 用户能够登录到 SAML SSO for Jira by resolution GmbH，必须将其预配到 SAML SSO for Jira by resolution GmbH 中。 对于本教程，必须手动完成预配。 但是，还有其他预配模型可供解决方案的 SAML SSO 插件使用，例如 **实时** 预配。 请参阅 [SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all) 提供的文档。 如果遇到相关问题，请联系[解决方案支持](https://www.resolution.de/go/support)。

**若要手动预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Jira 实例。

2. 将鼠标悬停在齿轮图标上，然后选择“用户管理”。

   ![屏幕截图显示了指向“齿轮”图标的箭头，并从下拉列表中选择了“用户管理”。](./media/samlssojira-tutorial/user-1.png)

3. 如果已重定向到“管理员访问”页，请输入 **密码**，然后单击“确认”按钮。

    ![屏幕截图显示了“管理员访问”页，其中突出显示了“密码”文本框。](./media/samlssojira-tutorial/user-2.png) 

4. 在“用户管理”选项卡部分，单击“创建用户”。

    ![屏幕截图显示了“用户管理”选项卡，其中选择了“创建用户”按钮。](./media/samlssojira-tutorial/user-3-new.png) 

5. 在“新建用户”对话框页上执行以下步骤。 必须创建与 Azure AD 中完全相同的用户：

    ![添加员工](./media/samlssojira-tutorial/user-4-new.png) 

    a. 在“电子邮件地址”文本框中，键入用户的电子邮件地址：<b>BrittaSimon@contoso.com</b>。

    b. 在“全名”文本框中，键入用户的全名： **Britta Simon**。

    c. 在“用户名”文本框中，键入用户的电子邮件地址：<b>BrittaSimon@contoso.com</b>。 

    d. 在“密码”文本框中，输入用户的密码。

    e. 单击“创建用户”以完成用户创建。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 SAML SSO for Jira by resolution GmbH 登录 URL，你可以从此处启动登录流。  

* 直接转到 SAML SSO for Jira by resolution GmbH 登录 URL，并从此处启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到你为其设置了 SSO 的 SAML SSO for Jira by resolution GmbH。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 SAML SSO for Jira by resolution GmbH 磁贴时，如果是在 SP 模式下配置的，则会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则会自动登录到为其设置了 SSO 的 SAML SSO for Jira by resolution GmbH。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="enable-sso-redirection-for-jira"></a>为 Jira 启用 SSO 重定向

如前面的部分中所述，目前可以通过两种方式来触发单一登录： 使用 **Azure 门户** 或使用 **Jira 实例的特殊链接**。 使用 resolution GmbH 提供的 SAML SSO 插件，还可以简单地通过 **访问指向 Jira 实例的任何 URL**，来触发单一登录。

从本质上讲，访问 Jira 的所有用户在激活该插件中的某个选项后，会重定向到单一登录页。

若要激活 SSO 重定向，请 **在 Jira 实例** 中执行以下操作：

1. 访问 Jira 中 SAML SSO 插件的配置页。
1. 单击左面板中的“重定向”。

   ![Jira SAML 单一登录插件配置页的部分屏幕截图，突出显示左侧导航栏中的重定向链接。](./media/samlssojira-tutorial/configure-1.png)

1. 勾选“启用 SSO 重定向”。

   ![Jira SAML 单一登录插件配置页的部分屏幕截图，突出显示“启用 SSO 重定向”复选框已被选中。](./media/samlssojira-tutorial/configure-2.png) 

1. 按右上角的“保存设置”按钮。

激活该选项后，如果已勾选“启用 nosso”选项，则仍可以通过导航到 `https://<server-base-url>/login.jsp?nosso` 来查看用户名/密码提示。 同样，将 \<server-base-url> 替换为基 URL。

## <a name="next-steps"></a>后续步骤

配置 SAML SSO for Jira by resolution GmbH 后，就可强制实施会话控制，以便实时防止组织的敏感数据遭到外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。