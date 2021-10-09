---
title: 教程：Azure AD 与 LiquidFiles 的 SSO 集成
description: 了解如何在 Azure Active Directory 和 LiquidFiles 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jeedes
ms.openlocfilehash: 2c20c03e61d7fa7055d1044d69cacc8b234ad8f4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636641"
---
# <a name="tutorial-azure-ad-sso-integration-with-liquidfiles"></a>教程：Azure AD 与 LiquidFiles 的 SSO 集成

本教程介绍如何将 LiquidFiles 与 Azure Active Directory (Azure AD) 进行集成。 将 LiquidFiles 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 LiquidFiles。
* 让用户使用其 Azure AD 帐户自动登录到 LiquidFiles。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 LiquidFiles 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录的 LiquidFiles 订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* LiquidFiles 支持启用了 SP 的 SSO。

## <a name="add-liquidfiles-from-the-gallery"></a>从库中添加 LiquidFiles

要配置 LiquidFiles 与 Azure AD 的集成，需要从库中将 LiquidFiles 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“LiquidFiles”。
1. 从结果面板中选择“LiquidFiles”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-liquidfiles"></a>配置并测试 LiquidFiles 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 LiquidFiles 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 LiquidFiles 相关用户之间建立关联。

若要配置并测试 LiquidFiles 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 LiquidFiles SSO](#configure-liquidfiles-sso) - 在应用程序端配置单一登录设置。
    1. [创建 LiquidFiles 测试用户](#create-liquidfiles-test-user) - 在 LiquidFiles 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“LiquidFiles”应用程序集成页上，找到“管理”部分，选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<YOUR_SERVER_URL>`

    b. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<YOUR_SERVER_URL>/saml/consume`

    c. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<YOUR_SERVER_URL>/saml/init` 

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [LiquidFiles 客户端支持团队](https://www.liquidfiles.com/support.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

6. 在“SAML 签名证书”部分中，复制 **指纹** 并将其保存在计算机上。

    ![复制指纹值](common/copy-thumbprint.png)

7. 在“设置 LiquidFiles”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 LiquidFiles 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“LiquidFiles”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-liquidfiles-sso"></a>配置 LiquidFiles SSO

1. 以管理员身份登录 LiquidFiles 公司站点。

1. 从菜单的“管理员”>“配置”中单击“单一登录”。

1. 在“单一登录”配置页上，执行以下步骤。

    ![配置单一登录](./media/liquidfiles-tutorial/configuration.png)

    a. 选择“SAML 2”作为“单一登录方法”。

    b. 在“IDP 登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    c. 在“IDP 注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    d. 在“IDP 证书指纹”文本框中，粘贴从 Azure 门户复制的“指纹”值。

    e. 在“名称标识符格式”文本框中，键入值 `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`。

    f. 在“身份验证上下文”文本框中，键入值 `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport`。

    g. 单击“保存”  。

### <a name="create-liquidfiles-test-user"></a>创建 LiquidFiles 测试用户

本部分的目的是在 LiquidFiles 中创建名为 Britta Simon 的用户。 请与 LiquidFiles 服务器管理员联系，以在登录到 LiquidFiles 应用程序之前将自己添加为用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 LiquidFiles 登录 URL，你可以从那里启动登录流。 

* 直接转到 LiquidFiles 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 LiquidFiles 磁贴时，会重定向到 LiquidFiles 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 LiquidFiles 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。