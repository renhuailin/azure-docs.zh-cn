---
title: 教程：Azure Active Directory 与 Egnyte 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Egnyte 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 9e82c980bb92832c3abd092ed799cf55b6f47438
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124835689"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Egnyte 的集成

本教程介绍如何将 Egnyte 与 Azure Active Directory (Azure AD) 集成。 将 Egnyte 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Egnyte。
* 让用户使用其 Azure AD 帐户自动登录到 Egnyte。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 Egnyte 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Egnyte 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-egnyte-from-the-gallery"></a>从库中添加 Egnyte

若要配置 Egnyte 与 Azure AD 的集成，需要从库中将 Egnyte 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Egnyte”。
1. 从结果面板中选择“Egnyte”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-egnyte"></a>配置并测试 Egnyte 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Form.com 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Form.com 中的相关用户之间建立链接关系。

若要配置并测试 Form.com 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Egnyte SSO](#configure-egnyte-sso)** - 在应用程序端配置单一登录设置。
    1. [创建 Egnyte 测试用户](#create-egnyte-test-user) - 在 Egnyte 中创建 B.Simon 的对应用户，将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Egnyte”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<companyname>.egnyte.com`。

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > 这些不是实际值。 使用实际的登录 URL 和回复 URL 更新该值。 请联系 [Egnyte 客户端支持团队](https://www.egnyte.com/corp/contact_egnyte.html)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

5. 在“设置 Egnyte”部分中，根据需求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>创建 Azure AD 测试用户 

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Egnyte 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Egnyte”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-egnyte-sso&quot;></a>配置 Egnyte SSO

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 Egnyte 公司站点。

2. 单击“设置”。
   
    ![设置 1](./media/egnyte-tutorial/settings-tab.png &quot;设置")

3. 在菜单中，单击“设置”。

    ![菜单 1](./media/egnyte-tutorial/menu-tab.png "菜单")

4. 单击“配置”选项卡，然后单击“安全”。

    ![安全性](./media/egnyte-tutorial/configuration.png "安全性")

5. 在“单一登录身份验证”部分中，执行以下步骤：

    ![单一登录身份验证](./media/egnyte-tutorial/authentication.png "单一登录身份验证")   
    
    a. 对于“单一登录身份验证”下，选择 **“SAML 2.0”**。
   
    b. 对于 **“标识提供程序”**，选择 **“AzureAD”**。
   
    c. 将从 Azure 门户复制的“登录 URL”粘贴到“标识提供者登录 URL”文本框。
   
    d. 将从 Azure 门户复制的“Azure AD 标识符”粘贴到“标识提供者实体 ID”文本框。
      
    e. 在记事本中打开从 Azure 门户下载的 base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“标识提供者证书”文本框。
   
    f. 对于 **“默认用户映射”**，选择 **“电子邮件地址”**。
   
    g. 对于 **“使用特定于域的颁发者值”**，选择 **“已禁用”**。
   
    h. 单击“ **保存**”。

### <a name="create-egnyte-test-user"></a>创建 Egnyte 测试用户

若要使 Azure AD 用户能够登录到 Egnyte，必须将这些用户预配到 Egnyte 中。 对于 Egnyte，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Egnyte 公司站点。

2. 转到“设置”\>“用户和组”。

3. 单击 **“添加新用户”**，然后选择你要添加的用户的类型。
   
    ![用户](./media/egnyte-tutorial/add-user.png "用户")

4. 在“新建 Power User”部分中，执行以下步骤：
    
    ![新建标准用户](./media/egnyte-tutorial/new-user.png "新建标准用户")   

    a. 在“电子邮件”文本框中，输入用户的电子邮件，例如 Brittasimon\@contoso.com。

    b. 在“用户名”文本框中输入用户的用户名，例如“Brittasimon”。

    c. 选择“单一登录”作为“身份验证类型”。
   
    d. 单击“ **保存**”。
    
    >[!NOTE]
    >Azure Active Directory 帐户持有者将收到一封通知电子邮件。
    >

>[!NOTE]
>可以使用 Egnyte 提供的任何其他 Egnyte 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。
>

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Egnyte 登录 URL，并从此处启动登录流。 

* 直接转到 Egnyte 登录 URL，并从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的“Egnyte”磁贴时，系统会将你重定向到 Egnyte 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Egnyte 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。