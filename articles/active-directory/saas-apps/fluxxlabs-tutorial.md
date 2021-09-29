---
title: 教程：Azure Active Directory 与 Fluxx Labs 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Fluxx Labs 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: jeedes
ms.openlocfilehash: 51056c5c28fa15026a409f9baeb0f7a9ec5fd076
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124834564"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Fluxx Labs 的集成

本教程介绍如何将 Fluxx Labs 与 Azure Active Directory (Azure AD) 集成。 将 Fluxx Labs 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Fluxx Labs。
* 让用户使用其 Azure AD 帐户自动登录到 Fluxx Labs。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Fluxx Labs 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Fluxx Labs 支持 IDP 发起的 SSO。

## <a name="add-fluxx-labs-from-the-gallery"></a>从库中添加 Fluxx Labs

要配置 Fluxx Labs 与 Azure AD 的集成，需要从库中将 Fluxx Labs 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Fluxx Labs” 。
1. 从结果面板中选择“Fluxx Labs”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-fluxx-labs"></a>配置并测试 Fluxx Labs 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Fluxx Labs 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Fluxx Labs 相关用户之间建立关联。

若要使用 Fluxx Labs 配置并测试 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Fluxx Labs SSO](#configure-fluxx-labs-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Fluxx Labs 测试用户](#create-fluxx-labs-test-user)** - 在 Fluxx Labs 中创建 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Fluxx Labs”应用程序集成页上，找到“管理”部分，并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“设置 SAML 单一登录”页上，执行以下步骤：

    a. 在“标识符”文本框中，使用以下模式之一键入 URL：

    | 环境 | URL 模式|
    |-------------|------------|
    | 生产 | `https://<subdomain>.fluxx.io` |
    | 预生产 | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. 在“回复 URL”文本框中，使用以下模式之一键入 URL：

    | 环境 | URL 模式|
    |-------------|------------|
    | 生产 | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | 预生产 | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Fluxx Labs 客户端支持团队](https://fluxx.zendesk.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Fluxx Labs”部分中，根据要求复制相应的 URL。

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

在本部分中，将通过授予 B.Simon 访问 Fluxx Labs 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Fluxx Labs”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-fluxx-labs-sso"></a>配置 Fluxx Labs SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Fluxx Labs 公司站点。

2. 在“设置”部分下，选择“管理员”。

    ![显示已选择“管理员”的“设置”部分的屏幕截图。](./media/fluxxlabs-tutorial/configure-1.png)

3. 在管理面板中，选择“插件” > “集成”，然后选择“SAML SSO (已禁用)”  

    ![显示“集成”选项卡的屏幕截图，其中已选择“SAML SSO (已禁用)”。](./media/fluxxlabs-tutorial/configure-2.png)

4. 在属性部分执行以下步骤：

    ![显示“属性”部分的屏幕截图，其中已选中“SAML SSO”、字段中已输入值，并且已选择“保存”按钮。](./media/fluxxlabs-tutorial/configure-3.png)

    a. 选中“SAML SSO”复选框。

    b. 在“请求路径”文本框中，键入 **/auth/saml**。

    c. 在“回调路径”文本框中，键入 **/auth/saml/callback**。

    d. 在“断言使用者服务 URL (单一登录 URL)”文本框中，输入已在 Azure 门户中输入的“回复 URL”值。

    e. 在“受众(SP 实体 ID)”文本框中，输入已在 Azure 门户中输入的“标识符”值。

    f. 在“标识提供者 SSO 目标 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    g. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“标识提供者证书”文本框。

    h. 在“名称标识符格式”文本框中，输入值 `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`。

    i. 单击“ **保存**”。

    > [!NOTE]
    > 保存内容后，字段会出于安全原因显示空白，但值已保存到配置中。

### <a name="create-fluxx-labs-test-user"></a>创建 Fluxx Labs 测试用户

若要使 Azure AD 用户能够登录到 Fluxx Labs，必须将这些用户预配到 Fluxx Labs 中。 对于 Fluxx Labs，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Fluxx Labs 公司站点。

2. 单击下面显示的 **图标**。

    ![显示管理员选项的屏幕截图，其中已选择“你的仪表板为空”下方的“+”图标。](./media/fluxxlabs-tutorial/configure-6.png)

3. 在仪表板中，单击下面显示的图标打开“新建人员”卡。

    ![显示“联系人管理”菜单的屏幕截图，其中已选择“人员”旁边的“+”图标。](./media/fluxxlabs-tutorial/configure-4.png)

4. 在“新建人员”部分中，执行以下步骤：

    ![Fluxx Labs 配置](./media/fluxxlabs-tutorial/configure-5.png)

    a. Fluxx Labs 使用电子邮件作为 SSO 登录名的唯一标识符。 在“SSO UID”字段中填写用户的电子邮件地址，该值与他们进行 SSO 登录时使用的电子邮件地址匹配。

    b. 单击“ **保存**”。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Fluxx Labs。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Fluxx Labs”磁贴时，你应会自动登录到为其设置了 SSO 的 Fluxx Labs。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Fluxx Labs 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。