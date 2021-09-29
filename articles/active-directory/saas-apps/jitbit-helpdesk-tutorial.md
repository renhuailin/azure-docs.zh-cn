---
title: 教程：Azure Active Directory 与 Jitbit Helpdesk 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Jitbit Helpdesk 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2021
ms.author: jeedes
ms.openlocfilehash: cadd59b0e600a546c820746db01133cbced8ed1f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734131"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>教程：Azure Active Directory 与 Jitbit Helpdesk 集成

本教程介绍如何将 Jitbit Helpdesk 与 Azure Active Directory (Azure AD) 集成。 将 Jitbit Helpdesk 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Jitbit Helpdesk。
* 让用户使用其 Azure AD 帐户自动登录到 Jitbit Helpdesk。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Jitbit Helpdesk 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Jitbit Helpdesk 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-jitbit-helpdesk-from-the-gallery"></a>从库中添加 Jitbit Helpdesk

若要配置 Jitbit Helpdesk 与 Azure AD 的集成，需要从库中将 Jitbit Helpdesk 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Jitbit Helpdesk” 。
1. 在结果面板中选择“Jitbit Helpdesk”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-jitbit-helpdesk"></a>配置并测试 Jitbit Helpdesk 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Jitbit Helpdesk 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Jitbit Helpdesk 中的相关用户之间建立链接关系。

若要配置并测试 Jitbit Helpdesk 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Jitbit Helpdesk SSO](#configure-jitbit-helpdesk-sso)** - 在应用程序端配置单一登录。
    1. **[创建 Jitbit Helpdesk 测试用户](#create-jitbit-helpdesk-test-user)** - 在 Jitbit Helpdesk 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Jitbit Helpdesk”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入任一 URL：
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > 此值不是真实值。 使用实际登录 URL 更新此值。 请联系 [Jitbit Helpdesk 客户端支持团队](https://www.jitbit.com/support/)获取此值。

    b. 在“标识符(实体 ID)”文本框中，键入 URL：`https://www.jitbit.com/web-helpdesk/`

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Jitbit Helpdesk”部分，根据要求复制相应的 URL  。

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

在本部分，你将通过授予 B.Simon 访问 Jitbit Helpdesk 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Jitbit Helpdesk”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-jitbit-helpdesk-sso&quot;></a>配置 Jitbit Helpdesk SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Jitbit Helpdesk 公司站点。

1. 在顶部工具栏中，单击“管理”  。

    ![管理](./media/jitbit-helpdesk-tutorial/settings.png &quot;管理")

1. 单击“常规设置”  。

    ![屏幕截图显示“常规设置”链接。](./media/jitbit-helpdesk-tutorial/general.png "用户、公司和权限")

1. 在“身份验证设置”  配置部分中，执行以下步骤：

    ![身份验证设置](./media/jitbit-helpdesk-tutorial/authentication.png "身份验证设置")

    a. 选择“启用 SAML 2.0 单一登录”，通过“OneLogin”使用单一登录 (SSO) 进行登录   。

    b. 在“终结点 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。  

    c. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后再粘贴到“X.509 证书”文本框中  

    d. 单击“保存更改”。 

### <a name="create-jitbit-helpdesk-test-user"></a>创建 Jitbit Helpdesk 测试用户

若要使 Azure AD 用户能够登录到 Jitbit Helpdesk，必须在 Jitbit Helpdesk 中预配这些用户。 对于 Jitbit Helpdesk，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **Jitbit Helpdesk** 租户。

1. 在顶部菜单中，单击“管理”。 

    ![管理](./media/jitbit-helpdesk-tutorial/settings.png "管理")

1. 单击“用户、公司和权限”  。

    ![用户、公司和权限](./media/jitbit-helpdesk-tutorial/users.png "用户、公司和权限")

1. 单击“添加用户”  。

    ![添加用户](./media/jitbit-helpdesk-tutorial/add.png "添加用户")

1. 在“创建”部分中，键入要预配的 Azure AD 帐户，如下所示：

    ![创建](./media/jitbit-helpdesk-tutorial/create-section.png "创建")

   a. 在“用户名”文本框中，键入类似于 **BrittaSimon** 的用户名。 

   b. 在“电子邮件”文本框中，键入用户的电子邮件地址（如 BrittaSimon@contoso.com）   。

   c. 在“名字”文本框中，键入用户的名字（如“Britta”）   。

   d. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）   。

   e. 单击“创建”。 

> [!NOTE]
> 可以使用任何其他 Jitbit Helpdesk 用户帐户创建工具或 Jitbit Helpdesk 提供的 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 此操作会重定向到 Jitbit Helpdesk 登录 URL，你可在其中启动登录流。 

* 直接转到 Jitbit Helpdesk 登录 URL，并从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Jitbit Helpdesk 磁贴时，会重定向到 Jitbit Helpdesk 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Jitbit Helpdesk 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。