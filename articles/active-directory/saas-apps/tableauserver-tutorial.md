---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Tableau Server 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Tableau Server 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 8d1606b09a6adb817c0603b1aa966a21fa9e2f04
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124772612"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Tableau Server 集成

在本教程中，你将了解如何将 Tableau Server 与 Azure Active Directory (Azure AD) 集成。 将 Tableau Server 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Tableau Server。
* 让用户使用其 Azure AD 帐户自动登录到 Tableau Server。
* 在一个中心位置（Azure 门户）管理帐户。


## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Tableau Server 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Tableau Server 支持 **SP** 发起的 SSO

## <a name="add-tableau-server-from-the-gallery"></a>从库中添加 Tableau Server

要配置 Tableau Server 与 Azure AD 的集成，需要从库中将 Tableau Server 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Tableau Server” 。
1. 从结果面板中选择“Tableau Server”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-tableau-server"></a>配置并测试适用于 Tableau Server 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 Tableau Server 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Tableau Server 相关用户之间建立链接关系。

若要配置并测试适用于 Tableau Server 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Tableau Server 单一登录](#configure-tableau-server-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Tableau Server 测试用户](#create-tableau-server-test-user)** - 在 Tableau Server 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Tableau Server”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://azure.<domain name>.link`。

    b. 在“标识符”框中，使用以下模式键入 URL：`https://azure.<domain name>.link`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > 上面的值不是实际值。 请使用来自 Tableau Server 配置页面的实际登录 URL、标识符和回复 URL 更新这些值，本教程下文中介绍了此页面。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上   。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Tableau Server”部分，根据要求复制相应的 URL。

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

在本部分中，将通过授予 B.Simon 访问 Tableau Server 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Tableau Server”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-tableau-server-sso"></a>配置 Tableau Server 单一登录

1. 若要为应用程序配置 SSO，需要以管理员身份登录到 Tableau Server 租户。

2. 在“配置”选项卡上，选择“用户标识和访问”，然后选择“身份验证方法”选项卡。

    ![显示从“用户标识和访问”选择了“身份验证”的屏幕截图。](./media/tableauserver-tutorial/auth.png)

3. 在“配置”页上，执行以下步骤：

    ![显示可在其中输入所述值的“配置”页的屏幕截图。](./media/tableauserver-tutorial/config.png)

    a. 对于 **身份验证方法**，请选择“SAML”。

    b. 选中“为服务器启用 SAML 身份验证”复选框。

    c. Tableau Server 返回 URL（Tableau Server 用户将要访问的 URL），例如 `http://tableau_server`。 但不建议使用 `http://localhost`。 不支持使用带尾部反斜杠的 URL（例如 `http://tableau_server/`）。 复制“Tableau Server 返回 URL”，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“登录 URL”文本框中  。

    d. SAML 实体 ID - 此实体 ID 唯一标识安装到 IdP 的 Tableau Server。 可以在此处再次输入 Tableau Server URL（如果需要），但它不必须是 Tableau Server URL。 复制“SAML 实体 ID”，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中  。

    e. 单击“下载 XML 元数据文件”，并在文本编辑器应用程序中打开该文件。 找到包含 Http Post 和索引 0 的断言使用者服务 URL 并复制该 URL。 现在，将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中 。

    f. 找到从 Azure 门户下载的联合元数据文件，然后在“SAML Idp 元数据文件”中上传它。

    g. 输入 IdP 用来保存用户名、显示名称和电子邮件地址的属性的名称。

    h. 单击“ **保存**”。

    > [!NOTE]
    > 客户必须上传 PEM 编码的 x509 证书文件（扩展名为 .crt）以及作为证书密钥文件的 RSA 或 DSA 私钥文件（扩展名为 .key）。 有关证书文件和证书密钥文件的详细信息，请参阅[此](https://help.tableau.com/current/server/en-us/saml_requ.htm)文档。 如果需要帮助在 Tableau Server 上配置 SAML，请参阅此文：[配置服务器端 SAML](https://help.tableau.com/current/server/en-us/config_saml.htm)。

### <a name="create-tableau-server-test-user"></a>创建 Tableau Server 测试用户

本部分的目的是在 Tableau Server 中创建名为“B.Simon”的用户。 需要在 Tableau Server 中预配所有用户。

用户的用户名应与在 **username** 的 Azure AD 自定义属性中配置的值匹配。 使用正确的映射，此集成应可实现配置 Azure AD 单一登录。

> [!NOTE]
> 如果需要手动创建用户，需要联系组织中的 Tableau Server 管理员。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Tableau Server 登录 URL，你可以在其中启动登录流。 

* 直接转到 Tableau Server 登录 URL，并在其中启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Tableau Server 磁贴时，会重定向到 Tableau Server 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Tableau Server 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。