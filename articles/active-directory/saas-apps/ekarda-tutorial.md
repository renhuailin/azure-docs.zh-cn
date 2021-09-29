---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 ekarda 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 ekarda 之间配置单一登录。
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
ms.openlocfilehash: b1bc72ab4608841f0c4cc191f296186e5402f3ba
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124835644"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>教程：Azure Active Directory 单一登录 (SSO) 与 ekarda 的集成

本教程介绍如何将 ekarda 与 Azure Active Directory (Azure AD) 集成。 将 ekarda 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 ekarda。
* 让用户使用其 Azure AD 帐户自动登录 ekarda。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 ekarda 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* ekarda 支持 SP 和 IdP 发起的 SSO。
* ekarda 支持实时用户预配。

## <a name="add-ekarda-from-the-gallery"></a>从库中添加 ekarda

若要配置 ekarda 与 Azure AD 的集成，请从库中将 ekarda 添加到托管 SaaS 应用列表：

1. 使用工作或学校帐户或者个人 Microsoft 帐户登录到 Azure 门户。

1. 在左侧窗格中选择“Azure Active Directory”服务。 
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“ekarda” 。
1. 在结果面板中选择“ekarda”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-ekarda"></a>配置并测试 ekarda 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 ekarda 的 Azure AD SSO。 要使 SSO 正常工作，需要在 Azure AD 用户与 ekarda 中的相关用户之间建立链接关系。

若要配置并测试 ekarda 的 Azure AD SSO，请执行以下步骤：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。

    1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以使用 B.Simon 测试 Azure AD 单一登录。
    1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够使用 Azure AD 单一登录。
1. [配置 ekarda SSO](#configure-ekarda-sso) - 在应用程序端配置单一登录设置。
    * [创建 ekarda 测试用户](#create-an-ekarda-test-user) - 在 ekarda 中创建 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
1. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在 Azure 门户中执行以下步骤来启用 Azure AD SSO：

1. 登录到 Azure 门户。
1. 在 ekarda 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，选择铅笔图标以编辑“基本 SAML 配置”设置 。

   ![“设置 SAML 单一登录”页面的屏幕截图，其中突出显示了铅笔图标。](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，如果有服务提供程序元数据文件，请执行以下步骤 ：
    1. 选择“上传元数据文件”。
    1. 选择文件夹图标将元数据文件选中，然后选择“上传”。
    1. 成功上传元数据文件后，标识符和回复 URL 的值会自动显示在 ekarda 部分的文本框中 。

    > [!Note]
    > 如果标识符和回复 URL 值未自动显示，请根据要求手动输入这些值 。

1. 如果没有在“基本 SAML 配置”部分看到服务提供程序元数据文件，但想要要在 IDP 发起的模式下配置应用程序，请输入以下字段的值 ：

    1. 在“标识符”文本框中，使用以下模式键入 URL：`https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. 如果要在 SP 发起的模式下配置应用程序，选择“设置其他 URL”并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > 前两个步骤中的值不是实际值。 请使用实际的标识符、回复 URL 和登录 URL 值更新这些值。 请联系 [ekarda 客户端支持团队](mailto:contact@ekarda.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“设置 SAML 单一登录”页面的“SAML 签名证书”部分中，选择“下载”，将证书 (Base64) 保存到计算机上   。

    ![“设置 SAML 单一登录”页面的“SAML 签名证书”部分的屏幕截图，其中突出显示了 Base64 证书的下载链接。](common/certificatebase64.png)

1. 在“设置 ekarda”部分中，根据要求复制相应的 URL。

    ![“设置 SAML 单一登录”页面的“设置 ekarda”部分的屏幕截图，其中突出显示了 URL 复制链接。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。

1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，输入 `B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，并记下“密码”框中显示的值 。
   1. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 ekarda 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。
1. 在应用程序列表中，选择“ekarda”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

1. 在“用户和组”对话框中，从用户列表中选择“B.Simon”。   然后选择屏幕底部的“选择”。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中选择“分配”。

## <a name="configure-ekarda-sso"></a>配置 ekarda SSO

1. 若要在 ekarda 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录”浏览器扩展 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 ekarda”会将你定向到 ekarda 应用程序。 在此处提供管理员凭据以登录到 ekarda。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 ekarda，请在另一个 Web 浏览器窗口中，以管理员身份登录到 ekarda 公司站点。

1. 选择“管理” > “我的帐户” 。

    ![ekarda 站点 UI 的屏幕截图，其中突出显示了“管理员”菜单上的“我的帐户”。](./media/ekarda-tutorial/ekarda.png)

1. 在页面底部，找到“SAML 设置”部分。 本部分介绍如何配置 SAML 集成。
1. 在“SAML 设置”部分，请执行以下步骤：

    ![ekarda SAML 设置页面的屏幕截图，其中突出显示了 SAML 配置字段。](./media/ekarda-tutorial/ekarda1.png)

    1. 选择“服务提供程序元数据”链接，并将其保存为计算机上的文件。
    1. 选中“启用 SAML”复选框。
    1. 在“IDP 实体 ID”文本框中，粘贴之前从 Azure 门户复制的 Azure AD 标识符值 。
    1. 在“IDP 登录 URL”文本框中，粘贴之前从 Azure 门户复制的登录 URL 值 。
    1. 在“IDP 登出 URL”文本框中，粘贴之前从 Azure 门户复制的登出 URL 值 。
    1. 在记事本中，打开从 Azure 门户下载的证书 (Base64) 文件。 将该内容粘贴到“IDP x509 证书”文本框。
    1. 在“选项”部分中选择“启用 SLO”复选框 。
    1. 选择“更新”  。

### <a name="create-an-ekarda-test-user"></a>创建 ekarda 测试用户

本部分将在 ekarda 中创建一个名为 B.Simon 的用户。 ekarda 支持默认已启用的实时用户预配。 在本部分中无需执行任何操作。 如果 ekarda 中尚不存在名为 B. Simon 的用户，则会在身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 ekarda 登录 URL，可以从那里启动登录流。

* 直接转到 ekarda 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应该会自动登录到为其设置了 SSO 的 ekarda

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 ekarda 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 ekarda。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 ekarda 后，可强制实施会话控制。 此预防措施可实时保护组织的敏感数据免于外泄和渗透。 会话控制扩展自条件访问应用控制。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。