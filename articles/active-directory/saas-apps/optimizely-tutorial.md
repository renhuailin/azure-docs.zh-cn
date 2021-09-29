---
title: 教程：Azure Active Directory 与 Optimizely 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Optimizely 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/24/2021
ms.author: jeedes
ms.openlocfilehash: eafd4002726e6ee25a8ad37e4fc2713b38d09af3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124808625"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>教程：Azure Active Directory 与 Optimizely 的集成

本教程介绍如何将 Optimizely 与 Azure Active Directory (Azure AD) 相集成。 将 Optimizely 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Optimizely。
* 让用户使用其 Azure AD 帐户自动登录到 Optimizely。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Optimizely 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Optimizely 支持 SP 发起的 SSO。

## <a name="add-optimizely-from-the-gallery"></a>从库中添加 Optimizely

要通过配置将 Optimizely 集成到 Azure AD 中，需从库将 Optimizely 添加到托管式 SaaS 应用的列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 Optimizely 。
1. 在结果面板中选择“Optimizely”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-optimizely"></a>配置并测试 Optimizely 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Optimizely 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Optimizely 中的相关用户之间建立链接关系。

若要配置并测试 Optimizely 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Optimizely SSO](#configure-optimizely-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Optimizely 测试用户](#create-optimizely-test-user) - 在 Optimizely 中创建 B.Simon 的对应用户，并将其链接到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Optimizely”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://app.optimizely.net/<INSTANCE_NAME>` 

    b. 在“标识符(实体 ID)”文本框中，键入使用以下模式的值：`urn:auth0:optimizely:contoso`

    > [!NOTE]
    > 这些不是实际值。 本教程稍后将介绍如何使用实际的登录 URL 和标识符来更新该值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. Optimizely 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标打开“用户属性”对话框。  

    ![屏幕截图显示“用户属性”对话框，已选择其右上角的“编辑”图标。](common/edit-attribute.png)

6. 除了上述属性，Optimizely 应用程序还要求在 SAML 响应中传递回更多的属性。 在“用户属性”对话框的“用户声明”部分执行以下步骤，以便添加 SAML 令牌属性，如下表所示：

    | 名称 | 源属性 |
    | ---------------| --------------- |
    | 电子邮件 | user.mail |
    
    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![屏幕截图显示“用户声明”对话框，其中突出显示了“添加新声明”和“保存”操作。](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“保存”  。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Optimizely”部分中，根据要求复制相应的 URL  。

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

在本部分，你将通过授予 B.Simon 访问 Optimizely 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Optimizely”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-optimizely-sso"></a>配置 Optimizely SSO

1. 若要在“Optimizely”端配置单一登录，请联系 Optimizely 帐户经理，并提供已下载的“证书 (Base64)”和复制的相应 URL   。

2. 在电子邮件回复中，Optimizely 会提供登录 URL（SP 发起的 SSO）和标识符（服务提供商实体 ID）值。

    a. 复制 Optimizely 提供的“SP 发起的 SSO URL”，并粘贴到 Azure 门户中“基本 SAML 配置”部分的“登录 URL”文本框中    。

    b. 复制 Optimizely 提供的“服务提供商实体 ID”，并粘贴到 Azure 门户中“基本 SAML 配置”部分的“登录 URL”文本框中    。

3. 在其他浏览器窗口中，登录 Optimizely 应用程序。

4. 单击右上角的帐户名称，并单击“帐户设置”。 

    ![屏幕截图显示，在右上角选择了帐户名称，并从菜单中选择了“帐户设置”。](./media/optimizely-tutorial/settings.png)

5. 在“帐户”选项卡中，选中“概述”部分“单一登录”下的“启用 SSO”框。  
  
    ![Azure AD 单一登录](./media/optimizely-tutorial/account.png)

6. 单击“保存” 。

### <a name="create-optimizely-test-user"></a>创建 Optimizely 测试用户

本部分需在 Optimizely 中创建名为“Britta Simon”的用户。

1. 在主页上选择“协作者”选项卡  。

2. 单击“新建协作者”  ，向项目添加新协作者。
   
    ![屏幕截图显示 Optimizely 主页，其中选择了“协作者”选项卡和“新建协作者”按钮。](./media/optimizely-tutorial/collaborator.png)

3. 填写电子邮件地址，为其分配角色。 单击“邀请”。 

    ![创建 Azure AD 测试用户](./media/optimizely-tutorial/invite-collaborator.png)

4. 他们将收到电子邮件邀请。 使用电子邮件地址，需登录到 Optimizely。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Optimizely 登录 URL，可从中启动登录流。 

* 直接转到 Optimizely 登录 URL，并从中启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Optimizely”磁贴时，会重定向到 Optimizely 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Optimizely 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。