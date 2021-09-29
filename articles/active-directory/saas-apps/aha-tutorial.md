---
title: 教程：Azure Active Directory 与 Aha! 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Aha! 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 7c973bdb6fad6b7974e170d455c46a095b4a7754
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124822893"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>教程：将 Aha! 与 Azure Active Directory 集成

本教程介绍如何将 Aha! 与 Azure Active Directory (Azure AD) 集成。 将 Aha! 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Aha!。
* 让用户使用其 Azure AD 帐户自动 登录到 Aha!。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* Aha! 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Aha! 支持 **SP** 发起的 SSO
* Aha! 支持 **实时** 用户预配

## <a name="add-aha-from-the-gallery"></a>添加 Aha! 添加 Aha!

若要配置 Aha! 与 Azure AD 的集成，你需要将 Aha! 从库中 添加到托管式 SaaS 应用的列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，  键入“Aha!”。
1. 从结果面板中选择“Aha!”， 然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-aha"></a>配置并测试 Aha! 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Aha! 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Aha! 相关用户之间建立链接关系。

若要配置并测试 Aha! 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. **[配置 Aha!SSO](#configure-aha-sso)** - 在应用程序端配置单一登录设置。
    1. [创建 Aha! 测试用户](#create-aha-test-user) - 在 Aha! 中创建 B.Simon 的对应用户， 并将其链接到用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Aha!  应用程序集成页上，找到“管理”部分，选择“单一登录” 。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

    ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.aha.io/session/new` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<companyname>.aha.io`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 若要获取这些值，请联系 [Aha! 客户端支持团队](https://www.aha.io/company/contact)。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Aha!” 部分，根据要求复制相应 URL。

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

在本部分中，将通过授予 B.Simon 访问 Aha! 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Aha!”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-aha-sso"></a>配置 Aha! SSO

1. 若要在“Aha!”中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展” 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Aha!”， 此时将定向到 Aha! 配置单一登录。 在此处，提供管理员凭据以登录到 Aha!。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-8。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Aha!， 请打开新的 Web 浏览器窗口，以管理员身份登录到 Aha! 公司站点，并执行以下步骤：

4. 在顶部菜单中，单击“设置”。

    ![设置](./media/aha-tutorial/setting.png "设置")

5. 单击“帐户”。

    ![配置文件](./media/aha-tutorial/account.png "配置文件")

6. 单击“安全性和单一登录”。

    ![突出显示“安全性和单一登录”菜单选项的屏幕截图。](./media/aha-tutorial/security.png "安全性和单一登录")

7. 在“单一登录”部分中，对于“标识提供者”，选择“SAML2.0”。

    ![安全性和单一登录](./media/aha-tutorial/saml.png "安全性和单一登录")

8. 在“单一登录”配置页上，执行以下步骤：

    ![单一登录](./media/aha-tutorial/sso.png "单一登录")

    a. 在“名称”文本框中，键入配置名称。

    b. 对于“配置使用”，请选择“元数据文件”。

    c. 若要上传下载的元数据文件，请单击“浏览”。

    d. 单击“更新”。

### <a name="create-aha-test-user"></a>创建 Aha! 测试用户

本部分将在 Aha! 中创建一个名为 B.Simon 的用户。 Aha! 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Aha! 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Aha! 登录 URL，可以从那里启动登录流。 

* 转到 Aha! 直接登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在访问面板中单击“Aha!” 磁贴在“我的应用”中，这会重定向到 Aha! 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Aha! 后 可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。