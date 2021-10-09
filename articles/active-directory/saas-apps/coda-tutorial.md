---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Coda 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Coda 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2021
ms.author: jeedes
ms.openlocfilehash: 0717ed4934bd2a8ff9c0dbc4b893710aaaff870b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650793"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Coda 集成

本教程介绍如何将 Coda 与 Azure Active Directory (Azure AD) 集成。 将 Coda 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Coda。
* 让用户使用其 Azure AD 帐户自动登录到 Coda。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Coda 单一登录 (SSO) 但已禁用 GDrive 集成的订阅（企业版）。 若要为组织禁用 GDrive 集成（如果当前已启用），请联系 [Coda 支持团队](mailto:support@coda.io)。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Coda 支持 IDP 发起的 SSO。

* Coda 支持实时用户预配。

* Coda 支持[自动用户预配](coda-provisioning-tutorial.md)。

## <a name="add-coda-from-the-gallery"></a>从库中添加 Coda

若要配置 Coda 与 Azure AD 的集成，需要从库中将 Coda 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Coda**。
1. 在结果面板中选择“Coda”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-coda"></a>配置并测试 Coda 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 Coda 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Coda 中的相关用户之间建立链接关系。

若要配置并测试 Coda 的 Azure AD SSO，请执行以下步骤：

1. **[开始配置 Coda SSO](#begin-configuration-of-coda-sso)** - 开始在 Coda 中配置 SSO。
1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
   1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
   1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Coda SSO](#configure-coda-sso)** - 在 Coda 中完成配置单一登录设置。
   1. **[创建 Coda 测试用户](#create-coda-test-user)** - 在 Coda 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="begin-configuration-of-coda-sso"></a>开始配置 Coda SSO

在 Coda 中执行以下步骤开始配置。

1. 在 Coda 中，打开“组织设置”面板。

   ![打开“组织设置”](media/coda-tutorial/settings.png)

1. 确保组织已禁用 GDrive 集成。 如果当前已启用，请联系 [Coda 支持团队](mailto:support@coda.io)帮助你移走 GDrive。

   ![已禁用 GDrive](media/coda-tutorial/gdrive-off.png)

1. 在“使用 SSO 进行身份验证(SAML)”下，选择“配置 SAML”选项。

   ![SAML 设置](media/coda-tutorial/settings-link.png)

1. 记下“实体 ID”和“SAML 响应 URL”的值，因为在后续步骤中需要用到。

   ![要在 Azure 中使用的实体 ID 和 SAML 响应 URL](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Coda”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

   a. 在“标识符”文本框中，输入前面记下的“实体 ID”。 它应采用以下模式：`https://coda.io/samlId/<CUSTOMID>`

   b. 在“回复 URL”文本框中，输入前面记下的“SAML 响应 URL”。 它应采用以下模式：`https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > 你的值将不同于上面所示的值；可以在 Coda 的“配置 SAML”控制台中找到自己的值。 请使用实际标识符和回复 URL 更新这些值。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上   。

   ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Coda”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 Coda 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中选择“Coda”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-coda-sso"></a>配置 Coda SSO

若要完成设置，请在 Coda 的“配置 SAML”面板中，输入 Azure Active Directory 中的值。

1. 在 Coda 中，打开“组织设置”面板。
1. 在“使用 SSO 进行身份验证(SAML)”下，选择“配置 SAML”选项。
1. 将“SAML 提供者”设置为“Azure Active Directory”。
1. 在“标识提供者登录 URL”中，粘贴从 Azure 控制台获取的“登录 URL”。
1. 在“标识提供者颁发者”中，粘贴从 Azure 控制台获取的“Azure AD 标识符”。
1. 在“标识提供者公共证书”中选择“上传证书”选项，然后选择前面下载的证书文件。
1. 选择“保存”。

配置 SAML SSO 连接设置所需的操作到此完成。

### <a name="create-coda-test-user"></a>创建 Coda 测试用户

在本部分，我们将在 Coda 中创建名为 B.Simon 的用户。 Coda 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Coda 中尚不存在用户，身份验证后会创建一个新用户。

Coda 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./coda-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你会自动登录到为其设置了 SSO 的 Coda。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Coda 磁贴时，你会自动登录到为其设置了 SSO 的 Coda。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Coda 后，可以强制实施会话控制，实时防止组织的敏感数据遭受外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。