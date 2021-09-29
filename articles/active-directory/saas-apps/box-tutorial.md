---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Box 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Box 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2021
ms.author: jeedes
ms.openlocfilehash: 5f01332c2ab9ee9ecb3ca79a1883ea1a45ce76e9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791183"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-box"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Box 集成

本教程介绍如何将 Box 与 Azure Active Directory (Azure AD) 集成。 将 Box 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Box。
* 让用户使用其 Azure AD 帐户自动登录到 Box。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Box 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Box 支持 SP 发起的 SSO 
* Box 支持 [**自动** 用户预配和取消预配](./box-userprovisioning-tutorial.md)（推荐）
* Box 支持“实时”用户预配 

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-box-from-the-gallery"></a>从库中添加 Box

若要配置 Box 与 Azure AD 的集成，需要从库中将 Box 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Box”   。
1. 从结果面板中选择“Box”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-box"></a>配置并测试 Box 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 Box 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Box 相关用户之间建立链接关系。

若要配置并测试 Box 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Box SSO](#configure-box-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Box 测试用户](#create-box-test-user)** - 在 Box 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Box”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.account.box.com` 

    b. 在“标识符(实体 ID)”文本框中，键入 URL：`box.net`

    c. 在“回复 URL”文本框中键入 URL：`https://sso.services.box.net/sp/ACS.saml2`

    > [!NOTE]
    > 登录 URL 值不是实际值。 请使用实际登录 URL 更新此值。 请联系 [Box 客户端支持团队](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Box 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 Mapbox 令牌属性配置。 以下屏幕截图显示一个示例。 唯一用户标识符的默认值是 user.userprincipalname，但 Box 要求通过用户的电子邮件地址映射此项 。 为此，可以使用列表中的 **user.mail** 属性，或使用基于组织配置的相应属性值。

    ![image](common/default-attributes.png)


1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

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

在本部分中，将通过授予 B.Simon 访问 Box 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Box”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-box-sso"></a>配置 Box SSO

1. 若要在 Box 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录浏览器扩展” 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Box”会将你定向到 Box 应用程序。 在此处，提供管理员凭据以登录到 Box。 浏览器扩展将自动为你配置应用程序并自动执行步骤 3。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Box，请在另一个 Web 浏览器窗口中，以管理员身份登录到 Box 公司站点，并按照[自行设置 SSO](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown) 中的过程进行操作。

> [!NOTE]
> 如果无法为 Box 帐户配置 SSO 设置，则需要将下载的联合元数据 XML 发送给 [Box 支持团队](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire)  。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-box-test-user"></a>创建 Box 测试用户

本部分将在 Box 中创建一个名为 Britta Simon 的用户。 Box 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Box 中不存在用户，则会在身份验证后创建一个新用户。

> [!NOTE]
> 如果需要手动创建用户，请联系[支持团队](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中，选择“测试此应用程序”。 你将重定向到 Box 登录 URL，可以从那里启动登录流。

* 直接转到 Box 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Box 磁贴时，会重定向到 Box 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

### <a name="push-an-azure-group-to-box"></a>将 Azure 组推送到 Box

可以将 Azure 组推送到 Box 并同步该组。 Azure 通过 API 级别的集成将组推送到 Box。

1. 在“用户和组”中，搜索要分配给 Box 的组。
1. 在“预配”中，确保选中“将 Azure Active Directory 组同步到 Box” 。 此设置会同步在上一步中分配的组。 从 Azure 推送这些组可能需要一些时间。

> [!NOTE]
> 如果需要手动创建用户，请联系[支持团队](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire)。

## <a name="next-steps"></a>后续步骤

配置 Box 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制扩展自条件访问。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)