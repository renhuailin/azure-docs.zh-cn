---
title: 教程：Azure Active Directory 与 Everbridge 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Everbridge 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: jeedes
ms.openlocfilehash: 55d2c0962df43da34c9acc2926dd3506a803778c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124826420"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>教程：Azure Active Directory 与 Everbridge 集成

本教程介绍了如何将 Everbridge 与 Azure Active Directory (Azure AD) 集成。
将 Everbridge 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Everbridge。
* 让用户可使用其 Azure AD 帐户自动登录到 Everbridge。 这种访问控制称为单一登录 (SSO)。
* 使用 Azure 门户在一个中心位置管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Everbridge 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 使用单一登录的 Everbridge 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Everbridge 支持 IDP 发起的 SSO。

## <a name="add-everbridge-from-the-gallery"></a>从库中添加 Everbridge

要配置 Everbridge 与 Azure AD 的集成，需要从库中将 Everbridge 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Everbridge” 。
1. 从结果面板中选择“Everbridge”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-everbridge"></a>配置并测试 Everbridge 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Everbridge 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Everbridge 相关用户之间建立链接关系。

若要配置并测试 Everbridge 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Everbridge SSO](#configure-everbridge-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Everbridge 测试用户](#create-everbridge-test-user)** - 在 Everbridge 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Everbridge”应用程序集成页上，找到“管理”部分，选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

    >[!NOTE]
    >将应用程序配置为 Azure 门户和 Everbridge 门户的管理员门户或成员门户。

4. 若要将 Everbridge 应用程序配置为 Everbridge 管理员门户，请在“基本 SAML 配置”部分中执行以下步骤：

    a. 在“标识符”框中，输入采用以下模式的 URL。
    `https://sso.everbridge.net/<API_Name>`

    b. 在“回复 URL”框中，输入采用以下模式的 URL。
    `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符和回复 URL 将其更新。 请联系 [Everbridge 支持团队](mailto:support@everbridge.com)获取。 还可参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 若要将 Everbridge 应用程序配置为 Everbridge 成员门户，请在“基本 SAML 配置”部分中执行以下步骤：

  * 如果要在 IDP 发起的模式下配置应用程序，请执行以下步骤：

    a. 在“标识符”框中，输入采用以下模式的 URL：`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. 在“回复 URL”框中，输入采用以下模式的 URL：`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * 如果要在 SP 发起的模式下配置应用程序，选择“设置其他 URL”并执行以下步骤：

     a. 在“登录 URL”框中，输入采用以下模式的 URL：`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > 这些不是实际值。 使用实际标识符、回复 URL 和登录 URL 将其更新。 请联系 [Everbridge 支持团队](mailto:support@everbridge.com)获取。 还可参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

6. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，选择“下载”以下载“联合元数据 XML”。 然后将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Everbridge”部分，根据要求复制所需的 URL：

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

在本部分中，将通过授予 B.Simon 访问 Everbridge 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Everbridge”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-everbridge-sso"></a>配置 Everbridge SSO

若要将 Everbridge 上的 SSO 配置为“Everbridge 管理员门户”应用程序，请执行以下步骤。
 
1. 在另一 Web 浏览器窗口中，以管理员身份登录到 Everbridge。

1. 在顶部菜单中，选择“设置”选项卡。在“安全性”下，选择“适用于管理员门户的单一登录”  。
   
     ![配置单一登录](./media/everbridge-tutorial/settings.png)
   
     a. 在“名称”框中，输入标识符提供者的名称。 例如，贵公司的名称。
   
     b. 在“API 名称”框中，输入 API 的名称。
   
     c. 选择“选择文件”，上传从 Azure 门户下载的元数据文件。
   
     d. 对于“SAML 标识位置”，请选择“标识位于 Subject 语句的 NameIdentifier 元素中”。
   
     e. 在“标识提供者登录 URL”框中，粘贴从 Azure 门户复制的“登录 URL”值。
   
     f. 对于“服务提供程序发起的请求绑定”，选择“HTTP 重定向”。

     g. 选择“保存”。

## <a name="configure-everbridge-as-everbridge-member-portal-sso"></a>将 Everbridge 配置为 Everbridge 成员门户 SSO

若要将 Everbridge 的单一登录配置为 Everbridge 成员门户，请将下载的“联合元数据 XML”发送给 [Everbridge 支持团队](mailto:support@everbridge.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-everbridge-test-user"></a>创建 Everbridge 测试用户

在本部分中，会在 Everbridge 中创建测试用户 Britta Simon。 要将用户添加到 Everbridge 平台，请与 [Everbridge 支持团队](mailto:support@everbridge.com)协作。 使用单一登录前，必须先在 Everbridge 中创建并激活用户。 

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Everbridge。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Everbridge 磁贴时，你应当会自动登录到为其设置了 SSO 的 Everbridge。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Everbridge 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。