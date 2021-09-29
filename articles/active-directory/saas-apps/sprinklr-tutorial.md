---
title: 教程：Azure Active Directory 与 Sprinklr 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 与 Sprinklr 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2021
ms.author: jeedes
ms.openlocfilehash: e87a80df2ca97cf50a855af9422308f440a9dd4d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800928"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>教程：Azure Active Directory 与 Sprinklr 集成

本教程介绍如何将 Sprinklr 与 Azure Active Directory (Azure AD) 相集成。 将 Sprinklr 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Sprinklr。
* 让用户使用其 Azure AD 帐户自动登录到 Sprinklr。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Sprinklr 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Sprinklr 支持 SP 发起的 SSO。

## <a name="add-sprinklr-from-the-gallery"></a>从库中添加 Sprinklr

若要配置 Sprinklr 与 Azure AD 的集成，需要从库中将 Sprinklr 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 Sprinklr 。
1. 在结果面板中选择“Sprinklr”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-sprinklr"></a>配置并测试 Sprinklr 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Sprinklr 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Sprinklr 中的相关用户之间建立链接关系。

若要配置并测试 Sprinklr 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Sprinklr SSO](#configure-sprinklr-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Sprinklr 测试用户](#create-sprinklr-test-user) - 在 Sprinklr 中创建 B.Simon 的对应用户，并将其链接到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Sprinklr”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.sprinklr.com` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.sprinklr.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Sprinklr 客户端支持团队](https://www.sprinklr.com/contact-us/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Sprinklr”部分，根据要求复制相应 URL  。

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

在本部分，你将通过授予 B.Simon 访问 Sprinklr 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Sprinklr”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-sprinklr-sso&quot;></a>配置 Sprinklr SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Sprinklr 公司站点。

1. 转到“管理”\>“设置”  。

    ![管理](./media/sprinklr-tutorial/settings.png &quot;管理")

1. 从左侧窗格转到“管理合作伙伴”\>“单一登录”  。

    ![管理合作伙伴](./media/sprinklr-tutorial/users.png "管理合作伙伴")

1. 单击“+添加单一登录”  。

    ![显示“添加单一登录”按钮的屏幕截图。](./media/sprinklr-tutorial/add-user.png "单一登录")

1. 在“单一登录”  页上，执行以下步骤：

    ![显示“单一登录”页的屏幕截图，可在其中输入所述的值。](./media/sprinklr-tutorial/configuration.png "单一登录")

    a. 在“名称”文本框中，键入配置名称（例如  ：WAADSSOTest)  。

    b. 选择“启用”。 

    c. 选择“使用新 SSO 证书”  。

    d. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“标识提供者证书”  文本框。

    e. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    f. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    g. 在“标识提供者注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    h. 对于“SAML 用户 ID 类型”，请选择“断言包含用户的 sprinklr.com 用户名”   。

    i. 对于“SAML 用户 ID 位置”，请选择“用户 ID 位于 Subject 语句的 NameIdentifier 元素中”   。

    j. 单击“ **保存**”。

    ![SAML](./media/sprinklr-tutorial/save-configuration.png "SAML")

### <a name="create-sprinklr-test-user"></a>创建 Sprinklr 测试用户

1. 以管理员身份登录 Sprinklr 公司站点。

1. 转到“管理”\>“设置”  。

    ![管理](./media/sprinklr-tutorial/settings.png "管理")

1. 从左侧窗格转到“管理客户端”\>“用户”  。

    ![显示“设置/用户”中的“添加用户”按钮的屏幕截图。](./media/sprinklr-tutorial/client.png "设置")

1. 单击“添加用户”  。

    ![显示“编辑用户”对话框的屏幕截图，可在其中输入所述的值。](./media/sprinklr-tutorial/search-users.png "设置")

1. 在“编辑用户”  对话框中，执行以下步骤：

    ![编辑用户](./media/sprinklr-tutorial/update-users.png "编辑用户")

    a. 在“电子邮件”  、“名字”  和“姓氏”  文本框中，键入要预配的 Azure AD 用户帐户的信息。

    b. 选择“禁用密码”  。

    c. 选择“语言”。 

    d. 选择“用户类型”。 

    e. 单击“更新”  。

    > [!IMPORTANT]
    > 必须选择“禁用密码”  才能让用户通过标识提供者登录。 

1. 单击“角色”  ，并执行以下步骤：

    ![合作伙伴角色](./media/sprinklr-tutorial/role.png "合作伙伴角色")

    a. 从“全局”  列表中，选择 **ALL_Permissions**。  

    b. 单击“更新”  。

> [!NOTE]
> 可以使用任何其他 Sprinklr 用户帐户创建工具或 Sprinklr 提供的 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Sprinklr 登录 URL，可从中启动登录流。 

* 直接转到 Sprinklr 登录 URL，并从中启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Sprinklr”磁贴时，会重定向到 Sprinklr 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Sprinklr 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。