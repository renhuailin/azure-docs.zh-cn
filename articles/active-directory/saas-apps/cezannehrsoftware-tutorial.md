---
title: 教程：Azure Active Directory 与 Cezanne HR Software 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Cezanne HR Software 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: 8128c30bc0dddd5f58c1c6ebe7386cb3c2902367
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124773258"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>教程：Azure Active Directory 与 Cezanne HR Software 的集成

在本教程中，了解如何将 Cezanne HR Software 与 Azure Active Directory (Azure AD) 集成。 将 Cezanne HR Software 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Cezanne HR Software。
* 让用户使用其 Azure AD 帐户自动登录到 Cezanne HR Software。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Cezanne HR Software 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Cezanne HR Software 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-cezanne-hr-software-from-the-gallery"></a>从库中添加 Cezanne HR Software

要配置 Cezanne HR Software 与 Azure AD 的集成，需要从库中将 Cezanne HR Software 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Cezanne HR Software” 。
1. 从结果面板中选择“Cezanne HR Software”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-cezanne-hr-software"></a>配置并测试 Cezanne HR Software 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Cezanne HR Software 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Cezanne HR Software 中的相关用户之间建立链接关系。

若要配置并测试 Cezanne HR Software 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Cezanne HR Software SSO](#configure-cezanne-hr-software-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Cezanne HR Software 测试用户](#create-cezanne-hr-software-test-user)** - 在 Cezanne HR Software 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Cezanne HR Software”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>` 

    b. 在“标识符(实体 ID)”文本框中，键入 URL：`https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > 这些不是实际值。 请使用实际的登录 URL 和回复 URL 更新这些值。 若要获取这些值，请联系 [Cezanne HR Software 客户端支持团队](https://cezannehr.com/services/support/)。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Cezanne HR Software”部分中，根据需要复制相应的 URL  。

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

在本部分中，通过授予 B.Simon 访问 Cezanne HR Software 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Cezanne HR Software”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-cezanne-hr-software-sso"></a>配置 Cezanne HR Software SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Cezanne HR Software 租户。

2. 在侧边菜单中，单击“管理”。 然后，导航到“安全设置”，并单击“单一登录” 。

    ![屏幕截图显示 Cezanne HR Software 租户，其中“安全设置”和“单一登录配置”处于选中状态。](./media/cezannehrsoftware-tutorial/settings.png)

3. 在“允许用户使用以下单一登录 (SSO) 服务进行登录”面板上，选中“SAML 2.0”框并选择“高级配置”选项。   

    ![屏幕截图显示“允许用户”窗格，其中“SAML 2.0”和“高级配置”处于选中状态。](./media/cezannehrsoftware-tutorial/configuration.png)

4. 单击“新增”按钮。 

    ![屏幕截图显示“新增”按钮。](./media/cezannehrsoftware-tutorial/new-button.png)

5. 在“SAML 2.0 IDENTITY PROVIDERS”部分的以下字段中输入内容并单击“确定” 。

    ![屏幕截图显示一个窗格，可在其中输入此步骤所述的值。](./media/cezannehrsoftware-tutorial/identity-provider.png)

    a. **显示名称** - 输入标识提供者的名称作为显示名称。

    b. **实体标识符** - 在“实体标识符”文本框中，粘贴从 Azure 门户复制的“Azure Ad 标识符”值。

    c. **SAML 绑定** - 将“SAML 绑定”更改为“POST”。

    d. **安全令牌服务终结点** - 在“安全令牌服务终结点”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    e. **用户 ID 属性名称** - 在“用户 ID 属性名称”文本框中，输入 http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress 。

    f. **公钥证书** - 单击“上传”图标，上传从 Azure 门户下载的证书。

6. 单击“确定”。

7. 单击“保存”按钮  。 

    ![屏幕截图显示单一登录配置的“保存”按钮。](./media/cezannehrsoftware-tutorial/save-button.png)

### <a name="create-cezanne-hr-software-test-user&quot;></a>创建 Cezanne HR Software 测试用户

为了使 Azure AD 用户能够登录到 Cezanne HR Software，必须将其预配到 Cezanne HR Software 中。 对于 Cezanne HR Software，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Cezanne HR Software 公司站点。

2. 在侧边菜单中，单击“管理”。 然后，导航到“用户”，并单击“添加新用户” 

    ![屏幕截图显示 Cezanne HR Software 租户，其中“管理用户”和“添加新用户”处于选中状态。](./media/cezannehrsoftware-tutorial/manage-users.png &quot;新建用户")

3. 在“人员详细信息”  部分中，执行以下步骤：

    ![屏幕截图显示“人员详细信息”部分，可在其中输入此步骤所述的值。](./media/cezannehrsoftware-tutorial/details.png "新建用户")

    a. 将“内部用户”设置为“关闭”。 

    b. 输入名字   

    c. 输入姓氏

    d. 输入电子邮件地址。

4. 在“帐户信息”  部分中，执行以下步骤：

    ![屏幕截图显示“帐户信息”，可在其中输入此步骤所述的值。](./media/cezannehrsoftware-tutorial/account.png "新建用户")

    a. 在“用户名”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    b. 在“密码”文本框中，键入用户的密码。 

    c. 对于“安全角色”，选择“HR 专业人员”。  

    d. 单击 **“确定”** 。
    ![屏幕截图显示“确定”按钮。](https://user-images.githubusercontent.com/80324891/115694644-f6eb5700-a358-11eb-9b23-a87a24921052.png)

5. 导航到“单一登录”选项卡，并在“SAML 2.0 标识符”区域中选择“新增”。   

    ![屏幕截图显示“单一登录”选项卡，可在其中选择“新增”。](./media/cezannehrsoftware-tutorial/single-sign-on.png "用户")

6. 对于“标识提供者”，选择标识提供者，在“用户标识符”文本框中，输入用户电子邮件地址 。

    ![屏幕截图显示“SAML 2.0 标识符”，可在其中选择“标识提供者”和“用户标识符”。](./media/cezannehrsoftware-tutorial/user-identifier.png "用户")

7. 单击“保存”按钮  。

    ![屏幕截图显示“用户设置”的“保存”按钮。](./media/cezannehrsoftware-tutorial/save.png "用户")

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Cezanne HR Software 登录 URL，可以从那里启动登录流。 

* 直接转到 Cezanne HR Software 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Cezanne HR Software 磁贴时，将会重定向到 Cezanne HR Software 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Cezanne HR Software 后，可以强制实施会话控制，以实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。