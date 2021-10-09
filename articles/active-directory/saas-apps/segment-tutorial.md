---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Segment 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Segment 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 52b944e60edbdc3eb784fcc91736bfca531340a0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124801631"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-segment"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Segment 集成

在本教程中，了解如何将 Segment 与 Azure Active Directory (Azure AD) 集成。 将 Segment 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Segment。
* 让用户使用其 Azure AD 帐户自动登录到 Segment。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Segment 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Segment 支持 SP 和 IDP 发起的 SSO。
* Segment 支持实时用户预配。
* Segment 支持[自动用户预配](segment-provisioning-tutorial.md)。

## <a name="add-segment-from-the-gallery"></a>从库中添加 Segment

要配置 Segment 与 Azure AD 的集成，需要从库中将 Segment 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Segment”。 
1. 从结果面板中选择“Segment”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-segment"></a>配置并测试 Segment 的 Azure AD SSO

使用名为“B.Simon”的测试用户来配置并测试 Segment 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Segment 中的相关用户之间建立链接关系。

若要配置并测试 Segment 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Segment SSO](#configure-segment-sso)** - 在应用程序端配置单一登录设置。
    1. [创建 Segment 测试用户](#create-segment-test-user) - 在 Segment 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Segment”应用程序集成页上，找到“管理”部分，并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 在“标识符”文本框中，使用以下模式键入值：`urn:auth0:segment-prod:samlp-<CUSTOMER_VALUE>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://segment-prod.auth0.com/login/callback?connection=<CUSTOMER_VALUE>`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://app.segment.com`

    > [!NOTE]
    > 这些值是占位符。 需要使用实际的标识符和回复 URL。 获取这些值的步骤稍后在本教程中介绍。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上   。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Segment”部分中，根据要求复制相应 URL。

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

在本部分中，将通过授予 Segment 访问权限，允许 B.Simon 使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Segment”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-segment-sso"></a>配置 Segment SSO

1. 在新的 Web 浏览器窗口中，以管理员身份登录到 Segment 公司站点。

1. 单击“设置”图标并向下滚动到“身份验证”，然后单击“连接”。  

    ![屏幕截图显示选择了“设置”图标，然后从“身份验证”菜单中选择了“连接”。](./media/segment-tutorial/connections.PNG)

1. 单击“添加新连接”。

    ![屏幕截图显示了“连接”部分，其中选择了“新增连接”按钮。](./media/segment-tutorial/new-connections.PNG)

1. 选择“SAML 2.0”作为要配置的连接，然后单击“选择连接”按钮。 

    ![屏幕截图显示了“选择连接”部分，其中选择了“ SAML 2.0”和“选择连接”按钮。](./media/segment-tutorial/select-connections.PNG)

1. 在下面的页上，执行以下步骤：

    ![屏幕截图显示了“配置标识提供者”页，其中突出显示了“单一登录 URL”和“受众 URL”文本框，并选择了下一步”按钮。](./media/segment-tutorial/configure.PNG)

    a. 复制“单一登录 URL”值，将其粘贴到“回复 URL”框中，位于 Azure 门户的“基本 SAML 配置”对话框中。  

    b. 复制“受众 URL”值，将其粘贴到“标识符 URL”框中，位于 Azure 门户的“基本 SAML 配置”对话框中。 

    c. 单击“下一步”。

    ![Segment 配置](./media/segment-tutorial/certificate.PNG)

1. 在“SAML 2.0 终结点 URL”框中，粘贴从 Azure 门户复制的“登录 URL”值 。

1. 在记事本中打开从 Azure 门户下载的“证书(Base64)”，将内容粘贴到“公共证书”文本框中。 

1. 单击“配置连接”

### <a name="create-segment-test-user"></a>创建 Segment 测试用户

在本部分中，将在 Segment 中创建一个名为 B.Simon 的用户。 Segment 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Segment 中尚不存在用户，将在身份验证后创建一个新用户。

Segment 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./segment-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Segment 登录 URL，你可以从那里启动登录流。  

* 直接转到 Segment 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Segment。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“Segment”磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则会自动登录到为其设置了 SSO 的 Segment。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Segment 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。