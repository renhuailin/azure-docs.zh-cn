---
title: 教程：Azure Active Directory 与 Rollbar 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Rollbar 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: 778916c50909e62d6bc7dfa8a6daddc0de7ffa02
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124821617"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>教程：Azure Active Directory 与 Rollbar 的集成

本教程介绍如何将 Rollbar 与 Azure Active Directory (Azure AD) 集成。 将 Rollbar 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Rollbar。
* 让用户使用其 Azure AD 帐户自动登录到 Rollbar。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Rollbar 的集成，需要具有以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Rollbar 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Rollbar 支持 SP 和 IDP 发起的 SSO。
* Rollbar 支持[自动用户预配](rollbar-provisioning-tutorial.md)。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-rollbar-from-the-gallery"></a>从库中添加 Rollbar

若要配置 Rollbar 与 Azure AD 的集成，需要从库中将 Rollbar 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Rollbar”。
1. 从结果面板中选择“Rollbar”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-rollbar"></a>配置并测试 Rollbar 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Rollbar 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Rollbar 中的相关用户之间建立关联。

若要配置并测试 Rollbar 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Rollbar SSO](#configure-rollbar-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Rollbar 测试用户](#create-rollbar-test-user) - 在 Rollbar 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Rollbar 应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 在“标识符”文本框中键入 URL：`https://saml.rollbar.com`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://rollbar.com/<ACCOUNT_NAME>/saml/sso/azure/`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://rollbar.com/<ACCOUNT_NAME>/saml/login/azure/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“回复 URL”和“注销 URL”更新这些值。 请联系 [Rollbar 客户端支持团队](mailto:support@rollbar.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Rollbar”部分，根据要求复制相应 URL  。

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

在本部分，你将通过授予 B.Simon 访问 Rollbar 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Rollbar”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-rollbar-sso"></a>配置 Rollbar SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Rollbar 公司站点。

1. 单击右上角的“配置文件设置”  ，然后单击“帐户名称设置”  。

    ![显示从“配置文件设置”中选择了帐户名称设置的屏幕截图。](./media/rollbar-tutorial/general.png)

1. 单击“安全性”下的“标识提供者”。 

    ![显示在“安全性”下选择了“标识提供者”的屏幕截图。](./media/rollbar-tutorial/security.png)

1. 在“SAML 标识提供者”  部分中，执行以下步骤：

    ![显示“SAML 标识提供者”的屏幕截图，可在其中输入所述的值。](./media/rollbar-tutorial/configure.png)

    a. 从“SAML 标识提供者”下拉列表中，选择“AZURE”。  

    b. 在记事本中打开元数据文件，将其内容复制到剪贴板，然后将其粘贴到“SAML 元数据”文本框中。 

    c. 单击“保存”  。

1. 单击“保存”按钮后，屏幕将如下所示：

    ![显示“SAML 标识提供者”页中的结果的屏幕截图。](./media/rollbar-tutorial/identity-provider.png)

    > [!NOTE]
    > 若要完成以下步骤，必须先将自己作为用户添加到 Azure 中的 Rollbar 应用。
    
    a. 如果想要要求所有用户通过 Azure 进行身份验证，则单击“通过标识提供者登录”  通过 Azure 重新进行身份验证。  

    b.  返回到屏幕后，选中“要求通过 SAML 标识提供者进行登录”  复选框。

    b. 单击“ **保存**”。

### <a name="create-rollbar-test-user"></a>创建 Rollbar 测试用户

为了使 Azure AD 用户能够登录到 Rollbar，必须将其预配到 Rollbar 中。 对于 Rollbar，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Rollbar 公司站点。

1. 单击右上角的“配置文件设置”  ，然后单击“帐户名称设置”  。

    ![用户](./media/rollbar-tutorial/general.png)

1. 单击“用户”  。

    ![添加员工](./media/rollbar-tutorial/user.png)

1. 单击“邀请团队成员”  。

    ![显示已选择“邀请团队成员”选项的屏幕截图。](./media/rollbar-tutorial/invite-user.png)

1. 在文本框中，输入用户名（例如 brittasimon\@contoso.com），然后单击“添加/邀请”。

    ![显示“添加/邀请成员”的屏幕截图，其中包含提供的地址。](./media/rollbar-tutorial/add-user.png)

1. 用户收到邀请，在接受邀请后，会在系统中创建该用户。

> [!NOTE]
> Rollbar 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./rollbar-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Rollbar 登录 URL，你可以从那里启动登录流。  

* 直接转到 Rollbar 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Rollbar。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 单击“我的应用”中的 Rollbar 磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则会自动登录到为其设置了 SSO 的 Rollbar。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Rollbar 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。