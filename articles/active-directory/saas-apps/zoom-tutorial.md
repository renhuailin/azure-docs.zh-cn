---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Zoom 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zoom 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
ms.openlocfilehash: ddbfb074753dbc0906021b84ddff482fcf8debd7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764690"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Zoom 集成

本教程介绍了如何将 Zoom 与 Azure Active Directory (Azure AD) 进行集成。 将 Zoom 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Zoom。
* 让用户使用其 Azure AD 帐户自动登录到 Zoom。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 Zoom 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Zoom 支持 SP  发起的 SSO 
* Zoom 支持[自动  用户预配](./zoom-provisioning-tutorial.md)。

## <a name="adding-zoom-from-the-gallery"></a>从库中添加 Zoom

若要配置 Zoom 与 Azure AD 的集成，需要从库中将 Zoom 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Zoom”   。
1. 从结果面板中选择“Zoom”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-zoom"></a>配置并测试 Zoom 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 Zoom 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Zoom 相关用户之间建立链接关系。

若要配置并测试 Zoom 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. **[配置 Zoom SSO](#configure-zoom-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Zoom 测试用户](#create-zoom-test-user)** - 在 Zoom 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Zoom”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.zoom.us` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`<companyname>.zoom.us`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.zoom.us`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Zoom 客户端支持团队](https://support.zoom.us/hc/)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Zoom”部分中，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

> [!NOTE]
> 若要了解如何在 Azure AD 中配置角色，请参阅[为企业应用程序配置 SAML 令牌中颁发的角色声明](../develop/active-directory-enterprise-app-role-management.md)。

> [!NOTE]
> Zoom 可能需要 SAML 有效负载中有组声明。 如果你已创建任何组，请与 [Zoom 客户端支持团队](https://support.zoom.us/hc/)联系并提供组信息，以便他们可以在其自己的一端配置组信息。 你还需要向 [Zoom 客户端支持团队](https://support.zoom.us/hc/)提供对象 ID，以便他们可以在其自己的一端配置对象 ID。 若要获取对象 ID，请参阅[使用 Azure 配置 Zoom](https://support.zoom.us/hc/articles/115005887566)。

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

在本部分中，将通过授予 B.Simon 访问 Zoom 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Zoom”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-zoom-sso"></a>配置 Zoom SSO

1. 若要在 Zoom 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录浏览器扩展” 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Zoom”，此时会将你定向到 Zoom 应用程序。 在此处，提供管理员凭据以登录到 Zoom。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Zoom，请在另一个 Web 浏览器窗口中，以管理员身份登录到 Zoom 公司站点。

2. 单击 **“单一登录”** 选项卡。

    ![“单一登录”选项卡](./media/zoom-tutorial/zoom-sso1.png "单一登录")

3. 单击 **“安全控件”** 选项卡，然后转到 **“单一登录”** 设置。

4. 在“单一登录”部分中，执行以下步骤：

    ![“单一登录”部分](./media/zoom-tutorial/zoom-sso2.png "单一登录")

    a. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    b. 对于 **注销页 URL** 值，需要转到 Azure 门户并单击左侧的 **Azure Active Directory**，然后导航到“应用注册”。

    ![“Azure Active Directory”按钮](./media/zoom-tutorial/appreg.png)

    c. 单击“终结点”

    ![“终结点”按钮](./media/zoom-tutorial/endpoint.png)

    d. 复制 **SAML-P 注销终结点** 并将其粘贴到“注销页 URL”文本框中。

    ![“复制终结点”按钮](./media/zoom-tutorial/endpoint1.png)

    e. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到 **“标识提供程序证书”** 文本框中。

    f. 在“颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 。 

    g. 单击 **“保存更改”** 。

    > [!NOTE]
    > 有关详细信息，请访问 zoom 文档 [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>创建 Zoom 测试用户

本部分的目标是在 Zoom 中创建一个名为 B.Simon 的用户。 Zoom 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](./zoom-provisioning-tutorial.md)。

> [!NOTE]
> 如果需要手动创建用户，则需要联系 [Zoom 客户端支持团队](https://support.zoom.us/hc/)

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Zoom 登录 URL，可以在其中启动登录流。

* 直接转到 Zoom 登录 URL，并在其中启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Zoom 磁贴时，会重定向到 Zoom 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Azure AD Zoom 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制扩展自条件访问。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)