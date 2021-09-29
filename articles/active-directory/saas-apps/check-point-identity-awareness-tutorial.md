---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Check Point Identity Awareness 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Check Point Identity Awareness 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2021
ms.author: jeedes
ms.openlocfilehash: 44dbc49b6bca6e0bf4b9079ee7199dc96ede06e6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796089"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-identity-awareness"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Check Point Identity Awareness 集成

在本教程中，你将学习如何将 Check Point Identity Awareness 与 Azure Active Directory（Azure AD）集成。 将 Check Point Identity Awareness 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁可以访问 Check Point Identity Awareness。
* 允许用户使用 Azure AD 帐户自动登录 Check Point Identity Awareness。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Check Point Identity Awareness 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Check Point Identity Awareness 支持 SP 发起的 SSO。

## <a name="adding-check-point-identity-awareness-from-the-gallery"></a>从库中添加 Check Point Identity Awareness

要配置 Check Point Identity Awareness 与 Azure AD 的集成，需将 Check Point Identity Awareness 从库中添加到托管 SaaS 应用列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Check Point Identity Awareness” 。
1. 从结果面板中选择“Check Point Identity Awareness”，添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-check-point-identity-awareness"></a>配置并测试 Check Point Identity Awareness 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试面向 Check Point Identity Awareness 的 Azure AD SSO。 要使 SSO 正常工作，需要在 Azure AD 用户与 Check Point Identity Awareness 中相关用户之间建立链接关系。

若要配置并测试 Check Point Identity Awareness 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Check Point Identity Awareness SSO](#configure-check-point-identity-awareness-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Check Point Identity Awareness 测试用户](#create-check-point-identity-awareness-test-user)** - 在 Check Point Identity Awareness 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Check Point Identity Awareness 应用程序集成页上，找到“管理”部分，并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<GATEWAY_IP>/connect/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<GATEWAY_IP>/connect/spPortal/ACS/Login/<IDENTIFIER_UID>`

    c. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<GATEWAY_IP>/connect` 

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符、回复 URL 和登录 URL 更新这些值。 联系 [Check Point Identity Awareness](mailto:support@checkpoint.com) 客户支持团队获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Check Point Identity Awareness”部分，根据需要复制相应的 URL。

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

在本节中，你将通过授予 B.Simon 对 Check Point Identity Awareness 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Check Point Identity Awareness”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-check-point-identity-awareness-sso"></a>配置 Check Point Identity Awareness SSO

1. 以管理员身份登录 Check Point Identity Awareness 公司网站。

1. 在“SmartConsole”>“网关和服务器”视图中，单击“新建”>“更多”>“用户/标识”>“标识提供者” 。

    ![新“标识提供者”的屏幕截图。](./media/check-point-identity-awareness-tutorial/identity-provider.png)

1. 在“新建标识提供者”窗口中执行以下步骤。

    ![“标识提供者”部分的屏幕截图。](./media/check-point-identity-awareness-tutorial/new-identity-provider.png)

    a. 在“网关”字段中，选择需要执行 SAML 身份验证的安全网关。

    b. 在“服务”字段中，选择下拉列表中的“标识感知” 。

    c. 复制“标识符（实体 ID）”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中  。

    d. 复制“回复URL”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中  。

    e. 选择“导入元数据文件”，上传从 Azure 门户下载的“联合元数据 XML” 。

    > [!NOTE]
    > 此外，还可以选择“手动插入”，手动将“实体 ID”和“登录 URL”值粘贴到相应的字段中，并上传从 Azure 门户获取的证书文件   。

    f. 单击“确定”。

### <a name="create-check-point-identity-awareness-test-user"></a>创建 Check Point Identity Awareness 测试用户

在本部分中，将在 Check Point Identity Awareness 中创建名为 Britta Simon 的用户。 与 [Check Point Identity Awareness 支持团队](mailto:support@checkpoint.com)合作，将用户添加到 Check Point Identity Awareness 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Check Point Identity Awareness 登录 URL，可在其中启动登录流。 

* 直接转到 Check Point Identity Awareness 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的“Check Point Identity Awareness”磁贴后，会重定向到“Check Point Identity Awareness”登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 Check Point Identity Awareness 后，可以强制实施会话控制，实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。