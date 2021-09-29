---
title: 教程：Azure Active Directory 与 Zoho 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zoho 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 9468f957f2679856a3f6d75a1d00a876ab66e37b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764702"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>教程：Azure Active Directory 与 Zoho 的集成

本教程介绍如何将 Zoho 与 Azure Active Directory (Azure AD) 进行集成。 将 Zoho 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 Zoho。
* 让用户能够使用其 Azure AD 帐户自动登录到 Zoho。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Zoho One 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 Zoho 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Zoho 支持 SP 发起的 SSO 

## <a name="add-zoho-from-the-gallery"></a>从库中添加 Zoho

若要配置 Zoho 与 Azure AD 的集成，需要从库中将 Zoho 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Zoho” 。
1. 在结果面板中选择“Zoho”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>配置并测试 Zoho 的 Azure AD SSO

使用名为“B.Simon”的测试用户配置并测试 Zoho 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Zoho 相关用户之间建立关联。

若要配置并测试 Zoho 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Zoho SSO](#configure-zoho-sso) - 在应用程序端配置单一登录设置。
    1. **[创建 Zoho 测试用户](#create-zoho-test-user)** - 在 Zoho 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Zoho”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company name>.zohomail.com`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Zoho 客户端支持团队](https://www.zoho.com/mail/contact.html)来获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Zoho”部分，根据要求复制相应 URL  。

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

在本部分中，你将通过授予 B.Simon 访问 Zoho 的权限，允许该用户使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Zoho”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

### <a name=&quot;configure-zoho-sso&quot;></a>配置 Zoho SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Zoho Mail 公司站点。

2. 转到“控制面板”  。
   
    ![控制面板](./media/zoho-mail-tutorial/control-panel.png &quot;控制面板")

3. 单击“SAML 身份验证”选项卡。 
   
    ![SAML 身份验证](./media/zoho-mail-tutorial/saml-authentication.png "SAML 身份验证")

4. 在“SAML 身份验证详细信息”  部分，执行以下步骤：
   
    ![SAML 身份验证详细信息](./media/zoho-mail-tutorial/details.png "SAML 身份验证详细信息")
   
    a. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”   。
   
    b. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”   。
   
    c. 在“更改密码 URL”  文本框中，粘贴从 Azure 门户复制的“更改密码 URL”  值。
       
    d. 在记事本中打开从 Azure 门户下载的 base-64 编码的证书，将其内容复制到剪贴板，再将其粘贴到“公钥”  文本框中。
   
    e. 选择“RSA”，作为 **算法**。
   
    f. 单击“确定”。

### <a name="create-zoho-test-user"></a>创建 Zoho 测试用户

要使 Azure AD 用户能够登录 Zoho Mail，必须将这些用户预配到 Zoho Mail 中。 需要在 Zoho Mail 中手动进行预配。

> [!NOTE]
> 可使用 Zoho Mail 提供的任何其他 Zoho Mail 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：

1. 以管理员身份登录到 **Zoho Mail** 公司站点。

1. 转到“控制面板”\>“邮件和文档”。

1. 转到“用户详细信息\>添加用户”。
   
    ![显示 Zoho 邮件网站的屏幕截图，其中选择了“用户详细信息”和“添加用户”。](./media/zoho-mail-tutorial/add-user-1.png "添加用户")

1. 在“添加用户”  对话框中，执行以下步骤：
   
    ![显示“添加用户”对话框的屏幕截图，可在其中输入所述值。](./media/zoho-mail-tutorial/add-user-2.png "添加用户")
   
    a. 在“名字”  文本框中，键入用户的名字（如“Britta”  ）。

    b. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）。

    c. 在“电子邮件 ID”文本框中，键入用户的电子邮件 ID，例如 brittasimon\@contoso.com 。

    d. 在“密码”文本框中，输入用户的密码。 
   
    e. 单击“确定”。   
      
    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，内附一个激活帐户前进行确认的链接。

### <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Zoho 登录 URL，可以从那里启动登录流。 

* 直接转到 Zoho 登录 URL，从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Zoho 磁贴时，你应该会自动登录到已为其设置了 SSO 的 Zoho。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

在配置 Zoho 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。