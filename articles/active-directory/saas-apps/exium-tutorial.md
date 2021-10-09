---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Exium 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Exium 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jeedes
ms.openlocfilehash: 36829f44d818356bbe61fe3f37265c2a76ed6f8a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617975"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-exium"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Exium 集成

本教程将介绍如何将 Exium 与 Azure Active Directory (Azure AD) 集成。 将 Exium 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Exium。
* 让用户使用其 Azure AD 帐户自动登录到 Exium。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 Exium 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Exium 支持 **SP** 发起的 SSO。
* Exium 支持[自动用户预配](exium-provisioning-tutorial.md)。

## <a name="adding-exium-from-the-gallery"></a>从库中添加 Exium

要配置 Exium 与 Azure AD 的集成，需要从库中将 Exium 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在 **从库中添加** 部分的搜索框中，键入 **Exium**。
1. 在结果面板中选择 **Exium**，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-exium"></a>配置并测试 Exium 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 Exium 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Exium 相关用户之间建立关联。

若要配置并测试 Exium 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Exium SSO](#configure-exium-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Exium 测试用户](#create-exium-test-user)** - 在 Exium 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 **Exium** 应用程序集成页上，找到 **管理** 部分，选择 **单一登录**。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://subapi.exium.net/saml/<WORKSPACE_ID>/metadata`
    
    b.  在“回复 URL”文本框中，使用以下模式键入 URL：`https://subapi.exium.net/saml/<WORKSPACE_ID>/acs`

    c. 在“登录 URL”文本框中，键入 URL：`https://service.exium.net/sign-in`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Exium 客户端支持团队](mailto:support@exium.net)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

本部分将通过授予 B.Simon 访问 Exium 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择 **Exium**。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-exium-sso"></a>配置 Exium SSO

1. 以管理员身份登录到 Exium 公司站点。

1. 在 **管理控制台** 中，选择 **公司配置文件** 面板。

    ![管理控制台的屏幕截图](./media/exium-tutorial/company-profile.png)

1. 在 **配置文件** 中，单击 **SSO 设置** 并对其进行 **编辑**。

1. 在 **SSO 设置** 部分中执行以下步骤。

    ![SSO 设置的屏幕截图](./media/exium-tutorial/update.png)

    a. 从下拉列表中选择 **SSO 类型** 为 **AzureAD**。

    b. 在 **SAML 2.0 IDP 元数据 URL** 字段中粘贴 **应用联合元数据 Url** 值。

    c. 复制 **SAML 2.0 SSO URL** 值，并将此值粘贴到 Azure 门户的 **基本 SAML 配置** 部分中的 **回复 URL** 文本框中。

    d. 复制 **SAML 2.0 SP 实体 ID** 值，并将此值粘贴到 Azure 门户的 **基本 SAML 配置** 部分中的 **标识符** 文本框中。  

    e. 单击“更新”。

### <a name="create-exium-test-user"></a>创建 Exium 测试用户

1. 以管理员身份登录到 Exium 公司站点。

1. 转到 **用户管理 -> 用户** 并单击 **添加用户**。

    ![创建测试用户的屏幕截图](./media/exium-tutorial/add-user.png)

1. 在以下页中填写必填字段，然后单击 **保存**。

    ![用于创建具有保存按钮的测试用户字段的屏幕截图](./media/exium-tutorial/add-user-2.png)

> [!NOTE]
>Exium 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./exium-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Exium 登录 URL，可以从这里启动登录流。 

* 直接转到 Exium 登录 URL，并从这里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Exium 磁贴时，会重定向到 Exium 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 Exium 后，可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。