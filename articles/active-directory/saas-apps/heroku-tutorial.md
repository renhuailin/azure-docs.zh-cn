---
title: 教程：Azure Active Directory 与 Heroku 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Heroku 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/05/2021
ms.author: jeedes
ms.openlocfilehash: e2dcb9b534a67fdaf4ebc4be224f9e1f3bbb770a
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111888997"
---
# <a name="tutorial-azure-active-directory-integration-with-heroku"></a>教程：Azure Active Directory 与 Heroku 集成

在本教程中，你将了解如何将 Heroku 与 Azure Active Directory (Azure AD) 集成。 将 Heroku 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 Heroku。
* 让用户使用其 Azure AD 帐户自动登录到 Heroku。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Heroku 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Heroku 支持 SP 发起的 SSO。
* Heroku 支持“实时”用户预配。

## <a name="add-heroku-from-the-gallery"></a>从库中添加 Heroku

要配置 Heroku 与 Azure AD 的集成，需要将库中的 Heroku 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Heroku” 。
1. 从结果面板中选择“Heroku”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-heroku"></a>配置并测试 Heroku 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Heroku 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Heroku 中的相关用户之间建立链接关系。

若要配置并测试 Heroku 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Heroku SSO](#configure-heroku-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Heroku 测试用户](#create-heroku-test-user) - 在 Heroku 中创建 B.Simon 的对应用户，并且将其链接到该用户在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Heroku”应用程序集成页面上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://sso.heroku.com/saml/<company-name>/init` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://sso.heroku.com/saml/<company-name>`

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 可从 Heroku 团队获取这些值，本文的稍后部分将对此进行描述。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Heroku”部分中，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Heroku 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Heroku”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-heroku-sso"></a>配置 Heroku SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Heroku 租户。

2. 单击“设置”选项卡。

3. 在“单一登录”页面上，单击“上载元数据”。

4. 上传从 Azure 门户下载的元数据文件。

5. 安装成功后，管理员会看到一个确认对话框，其中显示最终用户的 SSO 登录 URL。

6. 复制“Heroku 登录 URL”和“Heroku 实体 ID”值，返回 Azure 门户中的“基本 SAML 配置”部分，并分别将这两个值粘贴到“登录 URL”和“标识符(实体 ID)”文本框中。

    ![配置单一登录](./media/heroku-tutorial/single-sign-on.png)

7. 单击“下一步”  。

### <a name="create-heroku-test-user"></a>创建 Heroku 测试用户

本部分将在 Heroku 中创建一个名为 B.Simon 的用户。 Heroku 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Heroku 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Heroku 登录 URL，你可从那里启动登录流。 

* 直接转到 Heroku 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的“Heroku”磁贴时，这会重定向到 Heroku 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Heroku 后，即可强制实施会话控制，从而实时保护组织的敏感数据，使其免遭外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。