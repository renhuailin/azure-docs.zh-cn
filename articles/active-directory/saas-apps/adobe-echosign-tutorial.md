---
title: 教程：Azure AD 与 Adobe Sign 的 SSO 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Adobe Sign 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 62791850857770cba44eabf54719176e80cea48b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803411"
---
# <a name="tutorial-azure-ad-sso-integration-with-adobe-sign"></a>教程：Azure AD 与 Adobe Sign 的 SSO 集成

本教程介绍如何将 Adobe Sign 与 Azure Active Directory (Azure AD) 集成。 将 Adobe Sign 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Adobe Sign。
* 让用户使用其 Azure AD 帐户自动登录到 Adobe Sign。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：
 
* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Adobe Sign 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Adobe Sign 支持 SP 发起的 SSO。

## <a name="add-adobe-sign-from-the-gallery"></a>从库中添加 Adobe Sign

若要配置 Adobe Sign 与 Azure AD 的集成，需要从库中将 Adobe Sign 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Adobe Sign” 。
1. 从结果面板中选择 Adobe Sign，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>配置并测试 Adobe Sign 的 Azure AD SSO

在本部分，我们将基于名为“Britta Simon”的测试用户配置和测试 Adobe Sign 的 Azure AD 单一登录。 
若要使单一登录有效，需要在 Azure AD 用户与 Adobe Sign 中的相关用户之间建立链接关系。

若要配置并测试 Adobe Sign 的 Azure AD 单一登录，需要执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[配置 Adobe Sign SSO](#configure-adobe-sign-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Adobe Sign 测试用户](#create-adobe-sign-test-user)** - 在 Adobe Sign 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Adobe Sign 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 Adobe Sign 应用程序集成页上，选择“单一登录”。

1. 在 **选择单一登录方法** 对话框中，选择 **SAML/WS-Fed** 模式以启用单一登录。

1. 在“使用 SAML 设置单一登录”页面上，单击“铅笔”图标以打开“基本 SAML 配置”对话框 。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.echosign.com/` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<companyname>.echosign.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Adobe Sign”部分，根据要求复制相应的 URL  。

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

本部分中将通过授予 B.Simon 访问 Adobe Sign 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Adobe Sign”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-adobe-sign-sso"></a>配置 Adobe Sign SSO

1. 在配置之前，请联系 [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)，以便将域添加到 Adobe Sign 允许列表中。 下面介绍如何添加域：

    a. [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)会向你发送随机生成的令牌。 对于域，令牌将如下所示：**adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. 在 DNS 文本记录中发布该验证令牌并通知 [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)。

    > [!NOTE]
    > 这可能需要几天或更长时间。 请注意，DNS 传播延迟意味着 DNS 中发布的值可能在一小时或更长时间内不可见。 IT 管理员应具备有关如何在 DNS 文本记录中发布此令牌的丰富知识。

    c. 发布令牌并通过支持票证通知[Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)后，他们会验证该域并将其添加到你的帐户。

    d. 一般而言，在 DNS 记录中发布令牌的方法如下：

    * 登录到域帐户
    * 查找用于更新 DNS 记录的页面。 此页面可能名为“DNS 管理”、“名称服务器管理”或“高级设置”。
    * 查找你的域的 TXT 记录。
    * 添加带有 Adobe 提供的完整令牌值的 TXT 记录。
    * 保存所做更改。

1. 在其他 Web 浏览器窗口中，以管理员身份登录到 Adobe Sign 公司站点。

1. 在 SAML 菜单中，选择“帐户设置”   >   “SAML 设置”。

    ![Adobe Sign SAML 设置页的屏幕截图。](./media/adobe-echosign-tutorial/settings.png "帐户")

1. 在“SAML 设置”部分执行以下步骤  ：

    ![突出显示“SAML 设置”（包括“SAML 强制”）的屏幕截图。](./media/adobe-echosign-tutorial/profile.png "SAML 设置")

   ![SAML 设置的屏幕截图。](./media/adobe-echosign-tutorial/certificate.png "SAML 设置")

   a. 在“SAML 模式”下  ，选择“SAML 强制”  。

   b. 选择“允许 Echosign 帐户管理员使用 Echosign 凭据登录”  。

   c. 在“用户创建”下  ，选择“自动添加通过 SAML 验证了身份的用户”  。

   d. 将从 Azure 门户复制的“Azure AD 标识符”粘贴到“Idp 实体 ID”文本框   。

   e. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“Idp 登录 URL”   。

   f. 在“Idp 注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”   。

   g. 在记事本中打开已下载的 **Certificate(Base64)** 文件。 将其内容复制到剪贴板，然后将其粘贴到“IdP 证书”  文本框。

   h. 选择“保存更改”。 

### <a name="create-adobe-sign-test-user"></a>创建 Adobe Sign 测试用户

为了使 Azure AD 用户能够登录到 Adobe Sign，必须对其进行预配。 这是一个手动任务。

>[!NOTE]
>可以使用任何其他 Adobe Sign 用户帐户创建工具或 Adobe Sign 提供的 API 预配 Azure AD 用户帐户。 

1. 以管理员身份登录到 **Adobe Sign** 公司站点。

2. 在顶部菜单中，选择“帐户”。  然后，在左窗格中选择“用户和组” > “创建新用户”。

    ![Adobe Sign 公司站点的屏幕截图，其中突出显示了“帐户”、“用户和组”和“创建新用户”](./media/adobe-echosign-tutorial/account.png "帐户")

3. 在“创建新用户”  部分中，执行以下步骤：

    ![“创建新用户”部分的屏幕截图](./media/adobe-echosign-tutorial/user.png "创建用户")

    a. 键入希望在相关文本框中预配的有效 Azure AD 帐户的“电子邮件地址”  、“名字”  和“姓氏”  。

    b. 选择“创建用户”  。

>[!NOTE]
>Azure Active Directory 帐户持有者收到一封电子邮件，该邮件包含在激活帐户前确认帐户的链接。 

### <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Adobe 登录 URL，可从那里启动登录流。 

* 直接转到 Adobe 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Adobe Sign 磁贴时，应会自动登录到为其设置了 SSO 的 Adobe Sign。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Adobe Sign 后，可以强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。