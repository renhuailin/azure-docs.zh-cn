---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 AwareGo 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 AwareGo 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 096315d314555478b993151bd1b562010cb24949
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761251"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>教程：Azure Active Directory 单一登录与 AwareGo 的集成

在本教程中，你将了解如何将 AwareGo 与 Azure Active Directory (Azure AD) 进行集成。 将 AwareGo 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 AwareGo。
* 让用户使用其 Azure AD 帐户自动登录到 AwareGo。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 AwareGo 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 AwareGo 支持服务提供商 (SP) 发起的 SSO。


## <a name="adding-awarego-from-the-gallery"></a>从库中添加 AwareGo

若要配置 AwareGo 与 Azure AD 的集成，需要从库中将 AwareGo 添加到托管软件即服务 (SaaS) 应用列表。

1. 使用工作帐户或学校帐户或者个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧窗格中选择“Azure Active Directory”服务。 
1. 选择“企业应用程序” > “所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“AwareGo” 。
1. 在结果窗格中选择“AwareGo”，然后添加该应用。 几秒钟后，该应用将添加到租户。


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>配置并测试 AwareGo 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 AwareGo 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 AwareGo 相关用户之间建立链接关系。

若要配置并测试 AwareGo 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。  

    a. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 B.Simon 用户测试 Azure AD 单一登录。  
    b. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使用户 B.Simon 能够使用 Azure AD 单一登录。  

1. **[配置 AwareGo SSO](#configure-awarego-sso)** - 在应用程序端配置单一登录设置。

    a. **[创建 AwareGo 测试用户](#create-an-awarego-test-user)** - 在 AwareGo 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。  
    b. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

若要在 Azure 门户中启用 Azure AD SSO，请执行以下操作：

1. 在 Azure 门户中的“AwareGo”应用程序集成页的“管理”下，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 若要编辑设置，请在“设置 SAML 单一登录”窗格上选择“编辑”按钮 。

   ![“基本 SAML 配置”的“编辑”按钮的屏幕截图。](common/edit-urls.png)

1. 在“编辑”窗格上的“基本 SAML 配置”下，执行以下步骤：

    a. 在“登录 URL”框中，输入以下 URL 之一：

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. 在“标识符(实体 ID)”框中，使用以下模式输入 URL：`https://<SUBDOMAIN>.awarego.com`

    c. 在“回复 URL”框中，输入采用以下模式的 URL：`https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > 上面的值不是实际值。 使用实际标识符和回复 URL 更新它们。 若要获取这些值，请联系 [AwareGo 客户端支持团队](mailto:support@awarego.com)。 也可以参考 Azure 门户中的“基本 SAML 配置”部分显示的示例。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分旁的“证书(Base64)”中，选择“下载”以下载该证书并将其保存到计算机上   。

    ![“SAML 签名证书”窗格上的证书“下载”链接的屏幕截图。](common/certificatebase64.png)

1. 在“设置 AwareGo”部分中，根据要求复制一个或多个 URL。

    ![用于复制配置 URL 的“设置 AwareGo”窗格的屏幕截图。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”，然后选择“用户” > “所有用户”  。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性窗格中，执行以下操作：

   a. 在“名称”框中，输入 **B.Simon**。  
   b. 在“用户名”框中，按以下格式输入用户名：`<username>@<companydomain>.<extension>`（例如 B.Simon@contoso.com）。  
   c. 选中“显示密码”复选框，然后记下“密码”框中显示的值，以供后续使用 。  
   d. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予用户 B.Simon 访问 AwareGo 的权限，使其能够使用 Azure SSO。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  
1. 在应用程序列表中，选择“AwareGo” 。
1. 在应用概述页上的“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”窗格中选择“用户和组”  。
1. 在“用户和组”窗格中的“用户”列表中，选择“B.Simon”，然后选择“选择”按钮   。
1. 如果你希望将某角色分配给用户，可以在“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，则选择“默认访问权限”角色。
1. 在“添加分配”窗格中选择“分配”按钮 。

## <a name="configure-awarego-sso"></a>配置 AwareGo SSO

若要在 AwareGo 端配置单一登录，请将先前下载的“证书(Base64)”证书和先前从 Azure 门户复制的 URL 发送到 [AwareGo 支持团队](mailto:support@awarego.com) 。 支持团队会创建该值，以在两端正确建立 SAML SSO 连接。

### <a name="create-an-awarego-test-user"></a>创建 AwareGo 测试用户

在本部分，我们将在 AwareGo 中创建名为 Britta Simon 的用户。 与 [AwareGo 支持团队](mailto:support@awarego.com)协作，在 AwareGo 平台中添加用户。 必须先创建并激活用户，然后才能使用单一登录。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将执行以下操作测试 Azure AD 单一登录配置： 

* 在 Azure 门户中，选择“测试此应用程序”。 这会将你重定向到 AwareGo 登录页面，你可以在其中启动登录流。 

* 直接转到 AwareGo 登录页面，并从那里启动登录流。

* 转到 Microsoft“我的应用”。 在“我的应用”中选择 AwareGo 磁贴后，你将重定向到 AwareGo 登录页面。 有关详细信息，请参阅[从“我的应用”门户登录和启动应用](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 AwareGo 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制扩展自条件访问应用控制。 有关详细信息，请参阅[了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。