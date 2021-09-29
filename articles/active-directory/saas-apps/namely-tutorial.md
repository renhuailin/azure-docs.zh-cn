---
title: 教程：Azure Active Directory 与 Namely 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Namely 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/23/2021
ms.author: jeedes
ms.openlocfilehash: 837ca45cf94b6882cca65e7aad7c6c3e74a9d3dc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124741805"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>教程：Azure Active Directory 与 Namely 的集成

本教程介绍如何将 Namely 与 Azure Active Directory (Azure AD) 集成。 将 Namely 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Namely。
* 让用户使用其 Azure AD 帐户自动登录到 Namely。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Namely 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Namely 支持 SP 发起的 SSO。

## <a name="add-namely-from-the-gallery"></a>从库中添加 Namely

要配置Namely 与 Azure AD 的集成，需要从库中将 Namely 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Namely” 。
1. 从结果面板中选择“Namely”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-namely"></a>配置并测试 Namely 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Namely 的 Azure AD SSO。 若要使 SSO 正常运行，需要在 Azure AD 用户与 Namely 中的相关用户之间建立链接关系。

若要配置并测试 Namely 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Namely SSO](#configure-namely-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Namely 测试用户](#create-namely-test-user) - 在 Namely 中创建 B.Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Namely”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.namely.com` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Namely 客户端支持团队](https://www.namely.com/contact/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Namely”部分，根据要求复制相应 URL  。

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

本部分将通过授予 B. Simon 访问 Namely 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Namely”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-namely-sso"></a>配置 Namely SSO

1. 在另一个浏览器窗口中，以管理员身份登录到 Namely 公司站点。

2. 在顶部工具栏中，单击“公司”。 
   
    ![屏幕截图显示选择了“公司价值”。](./media/namely-tutorial/company.png) 

3. 单击“设置”选项卡。 
   
    ![屏幕截图显示选择了“公司设置”选项卡。](./media/namely-tutorial/settings.png) 

4. 单击“SAML”。 
   
    ![屏幕截图显示选择了“SAML”。](./media/namely-tutorial/general.png) 

5. 在“SAML 设置”  页上，执行以下步骤：
   
    ![屏幕截图显示了“SAML 设置”，你可以在其中输入所述值。](./media/namely-tutorial/settings-page.png)
 
    a. 单击“启用 SAML”。  

    b. 在“标识提供者 SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。
    
    c. 在记事本中打开下载的证书，复制其内容，然后将其粘贴到“标识提供程序证书”文本框中。 
     
    d. 单击“ **保存**”。

### <a name="create-namely-test-user"></a>创建 Namely 测试用户

本部分的目的是在 Namely 中创建名为 Britta Simon 的用户。

**若要在 Namely 中创建名为 Britta Simon 的用户，请执行以下步骤：**

1. 以管理员身份登录到 Namely 公司站点。

2. 在顶部工具栏中，单击“人员”。 
   
    ![屏幕截图显示选择了“人员价值”。](./media/namely-tutorial/people.png) 

3. 单击“目录”选项卡。 
   
    ![屏幕截图显示选择了“人员目录”选项卡。](./media/namely-tutorial/directory.png) 

4. 单击“添加新人员”。 

    ![屏幕截图显示了“新增人员”选项。](./media/namely-tutorial/add-person.png)

5. 在“添加新人员”对话框中，执行以下步骤： 

    a. 在“名字”文本框中，键入“Britta”。  

    b. 在“姓氏”文本框中，键入“Simon”。  

    c. 在“电子邮件”文本框中，键入 Britta Simon 的电子邮件地址   。

    d. 单击“ **保存**”。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Namely 登录 URL，可以从这里启动登录流。 

* 直接转到 Namely 登录 URL，并从这里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的“Namely”磁贴时，系统会重定向到 Namely 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Namely 后，你可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。