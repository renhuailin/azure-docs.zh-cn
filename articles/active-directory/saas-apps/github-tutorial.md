---
title: 教程：Azure Active Directory 与 GitHub Enterprise Cloud 组织集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 GitHub Enterprise Cloud 组织之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 12d80ee98ceef33221b7713703e8106bada6470c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750432"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-a-github-enterprise-cloud-organization"></a>教程：Azure Active Directory 单一登录 (SSO) 与 GitHub Enterprise Cloud 组织的集成

本教程介绍如何将 GitHub Enterprise Cloud 组织与 Azure Active Directory (Azure AD) 集成。 将 GitHub Enterprise Cloud 组织与 Azure AD 集成，可以实现以下目的：

* 在 Azure AD 中控制谁有权访问 GitHub Enterprise Cloud Organization。
* 在一个中心位置管理对 GitHub Enterprise Cloud Organization 的访问 - Azure 门户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 GitHub Enterprise Cloud 组织的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 一家在 [GitHub 企业云](https://help.github.com/articles/github-s-products/#github-enterprise)（需要 [GitHub Enterprise 计费计划](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)）中创建的 GitHub 组织

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* GitHub 支持 **SP** 发起的 SSO

* GitHub 支持 [**自动** 用户预配（组织邀请）](github-provisioning-tutorial.md)


## <a name="adding-github-from-the-gallery"></a>从库中添加 GitHub

要配置 GitHub 与 Azure AD 的集成，需要从库中将 GitHub 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分中，在搜索框中键入“GitHub” 。
1. 从结果面板中选择“GitHub Enterprise Cloud - 组织”，然后添加应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-github"></a>配置并测试 GitHub 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 GitHub 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 GitHub 中的相关用户之间建立链接关系。

若要配置并测试 GitHub 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 GitHub SSO](#configure-github-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 GitHub 测试用户](#create-github-test-user)** - 在 GitHub 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中，在 GitHub 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://github.com/orgs/<Organization ID>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://github.com/orgs/<Organization ID>/saml/consume`

    c. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://github.com/orgs/<Organization ID>/sso` 


    > [!NOTE]
    > 请注意，这些不是实际值。 必须使用实际登录 URL、标识符和回复 URL 更新这些值。 此处我们建议在“标识符”中使用字符串的唯一值。 转到“GitHub 管理”部分检索这些值。

5. GitHub 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中“唯一用户标识符(名称 ID)”通过 user.userprincipalname 进行映射 。 GitHub 应用程序要求通过 user.mail 对“唯一用户标识符(名称 ID)”进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射  。

    ![显示“用户属性”部分的屏幕截图，其中已选择“编辑”图标。](common/edit-attribute.png)

6. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 GitHub”部分中，根据要求复制相应的 URL。

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

在本部分中，将通过授予 B.Simon 访问 GitHub 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“GitHub”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。

    ![用户角色](./media/github-tutorial/user-role.png)

7. 在“添加分配”对话框中，单击“分配”按钮。 

## <a name="configure-github-sso"></a>配置 GitHub SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 GitHub 组织站点。

2. 导航到“设置”并单击“安全性”。 。

    ![显示 GitHub“组织设置”菜单的屏幕截图，其中已选择“安全性”。](./media/github-tutorial/security.png)

3. 选中“启用 SAML 身份验证”框（这样可显示“单一登录”配置字段），执行以下步骤：

    ![显示“SAML 单一登录”部分的屏幕截图，其中突出显示了“启用 SAML 身份验证”和 URL 文本框。](./media/github-tutorial/saml-sso.png)

    a. 复制“单一登录 URL”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”的“登录 URL”文本框中  。
    
    b. 复制“断言使用者服务 URL”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”的“回复 URL”文本框中  。

4. 配置以下字段：

    ![显示“登录 URL”、“颁发者”和“公共证书”文本框的屏幕截图。](./media/github-tutorial/configure.png)

    a. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    b. 在“颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 。

    c. 在记事本中打开从 Azure 门户下载的证书，将内容粘贴到“公共证书”文本框中。

    d. 单击“编辑”图标，将“签名方法”和“摘要方法”从 **RSA-SHA1** 和 **SHA1** 编辑为 **RSA-SHA256** 和 **SHA256**，如下所示  。
    
    e. 从默认 URL 更新“断言使用者服务 URL (回复 URL)”，以便 GitHub 中的 URL 与 Azure 应用注册中的 URL 匹配。

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. 单击“测试 SAML 配置”，确认在 SSO 期间未发生验证失败错误。

    ![设置](./media/github-tutorial/test.png)

6. 单击“保存”

> [!NOTE]
> GitHub 中的单一登录向 GitHub 中的特定组织进行身份验证，并不替换 GitHub 本身的身份验证。 因此，如果用户的 github.com 会话已过期，可能会要求你在单一登录过程中使用 GitHub 的 ID/密码进行身份验证。

### <a name="create-github-test-user"></a>创建 GitHub 测试用户

本部分的目的是在 GitHub 中创建名为“Britta Simon”的用户。 GitHub 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](github-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤：

1. 以管理员身份登录到 GitHub 公司站点。

2. 单击“人员”。

    ![显示 GitHub 站点的屏幕截图，其中已选中“人员”。](./media/github-tutorial/people.png "人员")

3. 单击“邀请成员”。

    ![邀请用户](./media/github-tutorial/invite-member.png "邀请用户")

4. 在“邀请成员”对话框页上，执行以下步骤：

    a. 在“电子邮件”文本框中，键入 Britta Simon 帐户的电子邮件地址。

    ![邀请人员](./media/github-tutorial/email-box.png "邀请人员")

    b. 单击“发送邀请”。

    ![显示“邀请成员”对话框页的屏幕截图，其中已选择“成员”和“发送邀请”按钮。](./media/github-tutorial/send-invitation.png "邀请人员")

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，并且将单击其中的链接以在激活帐户前确认帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 GitHub 登录 URL，可以从那里启动登录流。 

* 直接转到 GitHub 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 GitHub 磁贴时，会重定向到 GitHub 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 GitHub 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)
