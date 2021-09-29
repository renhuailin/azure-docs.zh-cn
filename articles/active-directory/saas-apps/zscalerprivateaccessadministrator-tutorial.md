---
title: 教程：Azure Active Directory 与 Zscaler Private Access Administrator 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zscaler Private Access Administrator 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2021
ms.author: jeedes
ms.openlocfilehash: 9ac5d06265242861a6f995256735bb76fae70249
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124751301"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>教程：Azure Active Directory 与 Zscaler Private Access Administrator 的集成

本教程介绍如何将 Zscaler Private Access Administrator 与 Azure Active Directory (Azure AD) 集成。 将 Zscaler Private Access Administrator 与 Azure AD 集成后，可以执行以下操作：

* 在 Azure AD 中控制谁有权访问 Zscaler Private Access Administrator。
* 让用户使用其 Azure AD 帐户自动登录到 Zscaler Private Access Administrator。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Zscaler Private Access Administrator 的集成，需具备以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Zscaler Private Access Administrator 单一登录的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Zscaler Private Access Administrator 支持 SP 和 IDP 发起的 SSO 。

## <a name="add-zscaler-private-access-administrator-from-the-gallery"></a>从库中添加 Zscaler Private Access Administrator

要配置 Zscaler Private Access Administrator 与 Azure AD 的集成，需要从库中将 Zscaler Private Access Administrator 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Zscaler Private Access Administrator” 。
1. 从结果面板中选择“Zscaler Private Access Administrator”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-zscaler-private-access-administrator"></a>配置并测试 Zscaler Private Access Administrator 的 Azure AD SSO

使用名为“B.Simon”的测试用户配置并测试 Zscaler Private Access Administrator 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Zscaler Private Access Administrator 相关用户之间建立链接关系。

若要配置和测试 Zscaler Private Access Administrator 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Zscaler Private Access Administrator SSO](#configure-zscaler-private-access-administrator-sso) - 在应用程序端配置单一登录。
    1. **[创建 Zscaler Private Access Administrator 测试用户](#create-zscaler-private-access-administrator-test-user)** - 在 Zscaler Private Access Administrator 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Zscaler Private Access Administrator”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.private.zscaler.com/auth/metadata`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.private.zscaler.com/auth/sso`

    c. 单击“设置其他 URL”  。

    d. 在“中继状态”文本框中键入值：`idpadminsso`

5.  如果要在“SP”发起的模式下配置应用程序，请执行以下步骤  ：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.private.zscaler.com/auth/sso`。   

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 若要获取这些值，请联系 [Zscaler Private Access Administrator 客户端支持团队](https://help.zscaler.com/zpa-submit-ticket)。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Zscaler Private Access Administrator”部分，根据要求复制相应的 URL。

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

在本部分中，你将通过授予 B.Simon 访问 Zscaler Private Access Administrator 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Zscaler Private Access Administrator”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-zscaler-private-access-administrator-sso"></a>配置 Zscaler Private Access Administrator SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Zscaler Private Access Administrator。

2. 在顶部单击“管理”，导航到“身份验证”部分，单击“IdP 配置”  。

    ![Zscaler Private Access Administrator 管理](./media/zscalerprivateaccessadministrator-tutorial/admin.png)

3. 在右上角，单击“添加 IdP 配置”。 

    ![Zscaler Private Access Administrator addidp](./media/zscalerprivateaccessadministrator-tutorial/add-configuration.png)

4. 在“添加 IdP 配置”页面上，执行以下步骤：
 
    ![Zscaler Private Access Administrator idpselect](./media/zscalerprivateaccessadministrator-tutorial/select-file.png)

    a. 在“IdP 元数据文件上传”字段中单击“选择文件”，上传从 Azure AD 下载的元数据文件。

    b. 它会从 Azure AD 读取 **IdP 元数据**，并填充所有字段信息，如下所示。

    ![Zscaler Private Access Administrator idpconfig](./media/zscalerprivateaccessadministrator-tutorial/metadata.png)

    c. 在“单一登录”中选择“管理员”。 

    d. 从“域”字段中选择域。
    
    e. 单击“保存”  。

### <a name="create-zscaler-private-access-administrator-test-user"></a>创建 Zscaler Private Access Administrator 测试用户

若要使 Azure AD 用户能够登录到 Zscaler Private Access Administrator，必须将他们预配到 Zscaler Private Access Administrator。 对于 Zscaler Private Access Administrator，需手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Zscaler Private Access Administrator 公司站点。

2. 在顶部单击“管理”，导航到“身份验证”部分，单击“IdP 配置”  。

    ![Zscaler Private Access Administrator 管理](./media/zscalerprivateaccessadministrator-tutorial/admin.png)

3. 在菜单左侧单击“管理员”。

    ![Zscaler Private Access Administrator 管理员](./media/zscalerprivateaccessadministrator-tutorial/administrator.png)

4. 在右上角，单击“添加管理员”。

    ![Zscaler Private Access Administrator 添加管理员](./media/zscalerprivateaccessadministrator-tutorial/add-administrator.png)

5. 在“添加管理员”页上执行以下步骤：

    ![Zscaler Private Access Administrator 用户管理](./media/zscalerprivateaccessadministrator-tutorial/user-admin.png)

    a. 在“用户名”文本框中，键入用户的电子邮件地址（例如 BrittaSimon@contoso.com）。

    b. 在“密码”文本框中，键入密码。

    c. 在“确认密码”文本框中，再次键入该密码。

    d. 在“角色”中选择“Zscaler Private Access Administrator”。 

    e. 在“电子邮件”文本框中，输入用户的电子邮件地址（例如 BrittaSimon@contoso.com）。

    f. 在“电话”文本框中，键入电话号码。

    g. 在“时区”文本框中，选择时区。

    h. 单击“ **保存**”。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Zscaler Private Access Administrator 登录 URL，可以从那里启动登录流。  

* 直接转到 Zscaler Private Access Administrator 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Zscaler Private Access Administrator。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Zscaler Private Access Administrator 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Zscaler Private Access Administrator。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Zscaler Private Access Administrator 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。