---
title: 教程：Azure Active Directory 与 OpsGenie 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 OpsGenie 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: e35ed7aaabbb81c274c4d92ad7e83dcd9f3bac77
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124738148"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>教程：Azure Active Directory 单一登录 (SSO) 与 OpsGenie 的集成

本教程介绍如何将 OpsGenie 与 Azure Active Directory (Azure AD) 集成。 将 OpsGenie 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 OpsGenie。
* 让用户使用其 Azure AD 帐户自动登录到 OpsGenie。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 OpsGenie 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* OpsGenie 支持 IDP 发起的 SSO 

## <a name="adding-opsgenie-from-the-gallery"></a>从库中添加 OpsGenie

要通过配置将 OpsGenie 集成到 Azure AD 中，需从库将 OpsGenie 添加到托管式 SaaS 应用的列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 OpsGenie。  
1. 在结果面板中选择“OpsGenie”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-opsgenie"></a>配置并测试 OpsGenie 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 OpsGenie 的 Azure AD SSO。  若要使 SSO 有效，需要在 Azure AD 用户与 OpsGenie 相关用户之间建立关联。

若要配置并测试 OpsGenie 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 OpsGenie SSO](#configure-opsgenie-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 OpsGenie 测试用户](#create-opsgenie-test-user)** - 在 OpsGenie 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“OpsGenie”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > 这些不是实际值。 使用实际的标识符和回复 URL（在本教程的后面部分介绍）更新这些值。

5. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

1. 在“设置 OpsGenie”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 OpsGenie 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“OpsGenie”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-opsgenie-sso"></a>配置 OpsGenie SSO

1. 若要在 OpsGenie 中自动执行配置，需要通过单击“安装扩展”，安装“我的应用安全登录”浏览器扩展 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“安装 OpsGenie”会将你定向到 OpsGenie 应用程序。 在此处提供管理员凭据以登录到 OpsGenie。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 OpsGenie，请在另一个 Web 浏览器窗口中，以管理员身份登录到 OpsGenie 公司站点。

2. 单击“设置”，并单击“单一登录”选项卡。  
   
    ![OpsGenie 单一登录](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. 若要启用 SSO，请选择“启用”。 
   
    ![屏幕截图显示选择了“已启用”复选框。](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. 在“提供程序”部分，单击“Azure Active Directory”选项卡。  
   
    ![屏幕截图显示“提供程序”部分，其中选择了“Azure Active Directory”选项卡。](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. 在“Azure Active Directory”对话框页上，执行以下步骤：
   
    ![屏幕截图显示了“单一登录”部分，其中包含“启用单一登录”开关、“SAML 2.0 终结点”和“元数据 URL”。](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. 复制“应用 ID URI”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符(实体 ID)”文本框中。   

    a. 复制“回复 URL”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中。   

    a. 在“SAML 2.0 终结点”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。
    
    b. 在“元数据 URL:”文本框中，粘贴从 Azure 门户复制的 **应用联合元数据 URL** 值。 
    
    c. 若要启用 SSO，请将“启用单一登录”开关切换为打开。 

    d. 单击“应用 SSO 设置”。 

### <a name="create-opsgenie-test-user"></a>创建 OpsGenie 测试用户

本部分的目的是在 OpsGenie 中创建名为 B.Simon 的用户。 

1. 在 Web 浏览器窗口中，以管理员身份登录到 OpsGenie 租户。

2. 单击左侧面板中的“用户”，导航到“用户”列表。 
   
    ![OpsGenie 设置](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. 单击“添加用户”  。

4. 在“添加用户”对话框中，执行以下步骤： 
   
    ![屏幕截图显示了“添加用户”对话框，其中突出显示了“电子邮件”和“全名”文本框，并已选择“保存”按钮。](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. 在“电子邮件”文本框中，键入 B.Simon 在 Azure Active Directory 中使用的电子邮件地址。 
   
    b. 在“全名”文本框中，键入 B.Simon。  
   
    c. 单击“ **保存**”。 

> [!NOTE]
> B.Simon 会收到一封电子邮件，其中包含个人资料设置说明。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 OpsGenie

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 OpsGenie 磁贴时，你应当会自动登录到为其设置了 SSO 的 OpsGenie。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

* 配置 OpsGenie 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。