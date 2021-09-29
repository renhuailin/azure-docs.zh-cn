---
title: 教程：Azure Active Directory 与 Clarizen One 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Clarizen One 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 04aeeea93b38ca358fd3d85be5d47a68c61d6e1d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124753983"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>教程：Azure Active Directory 与 Clarizen One 的集成

本教程介绍如何将 Clarizen One 与 Azure Active Directory (Azure AD) 进行集成。 将 Clarizen One 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 Clarizen One。
* 让用户能够使用其 Azure AD 帐户自动登录到 Clarizen One。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Clarizen One 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Clarizen One 支持 IDP 发起的 SSO。
* Clarizen One 支持[自动用户预配和取消预配](clarizen-one-provisioning-tutorial.md)（推荐）。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-clarizen-one-from-the-gallery"></a>从库中添加 Clarizen One

要配置 Clarizen One 与 Azure AD 的集成，需要从库中将 Clarizen One 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Clarizen One” 。
1. 在结果面板中选择“Clarizen One”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>配置并测试 Clarizen One 的 Azure AD SSO

使用名为“B.Simon”的测试用户配置并测试 Clarizen One 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Clarizen One 中的相关用户之间建立链接关系。

若要配置并测试 Clarizen One 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Clarizen One SSO](#configure-clarizen-one-sso) - 在应用程序端配置单一登录设置。
    1. **[创建 Clarizen 测试用户](#create-clarizen-one-test-user)** - 在 Clarizen One 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Clarizen One”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”页上，执行以下步骤：

    a. 在“标识符”文本框中键入以下值：`Clarizen`

    b. 在“回复 URL”文本框中键入 URL：`https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Clarizen One”部分，根据要求复制相应的 URL。

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

在本部分中，将通过授予 B.Simon 访问 Clarizen One 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Clarizen One”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-clarizen-one-sso&quot;></a>配置 Clarizen One SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Clarizen One 公司站点。

1. 单击用户名，然后单击“设置”。

    ![单击用户名下的“设置”](./media/clarizen-tutorial/setting.png &quot;设置")

1. 单击“全局设置”选项卡。然后，在“联合身份验证”旁边，单击“编辑”。 

    ![“全局设置”选项卡](./media/clarizen-tutorial/authentication.png "全局设置")

1. 在“联合身份验证”对话框中，执行以下步骤：

    ![“联合身份验证”对话框](./media/clarizen-tutorial/federated-authentication.png "联合身份验证")

    a. 选择“启用联合身份验证”。

    b. 单击 **“上载”** 上载你下载的证书。

    c. 在“登录 URL”框中，输入 Azure AD 应用程序配置窗口中的“登录 URL”值。

    d. 在“注销 URL”框中，输入 Azure AD 应用程序配置窗口中的“注销 URL”值。

    e. 选择 **“使用 POST”**。

    f. 单击“保存”  。

### <a name="create-clarizen-one-test-user"></a>创建 Clarizen One 测试用户

本部分的目的是在 Clarizen One 中创建名为“Britta Simon”的用户。

如果需要手动创建用户，请执行以下步骤： 

若要使 Azure AD 用户能够登录到 Clarizen One，必须预配用户帐户。 对于 Clarizen One，需要手动执行预配。

1. 以管理员身份登录到 Clarizen One 公司站点。

2. 单击“人员”。

    ![单击“人员”](./media/clarizen-tutorial/people.png "人员")

3. 单击 **“邀请用户”**。

    ![“邀请用户”按钮](./media/clarizen-tutorial/user.png "邀请用户")

1. 在“邀请人员”对话框中，执行以下步骤：

    ![“邀请人员”对话框](./media/clarizen-tutorial/invite-people.png "邀请人员")

    a. 在“电子邮件”框中，键入 Britta Simon 帐户的电子邮件地址。

    b. 单击“邀请”。 

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，并且将单击其中的链接以在激活帐户前确认帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”，你应该会自动登录到为其设置了 SSO 的 Clarizen One。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Clarizen One 磁贴时，应当会自动登录到为其设置了 SSO 的 Clarizen One。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

在配置 Clarizen One 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。