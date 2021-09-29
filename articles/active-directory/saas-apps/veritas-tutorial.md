---
title: 教程：Azure Active Directory 与 Veritas Enterprise Vault.cloud SSO 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Veritas Enterprise Vault.cloud SSO 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: f3a25e3411182dfb712125dac82c2e41806f52a8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820969"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>教程：Azure Active Directory 与 Veritas Enterprise Vault.cloud SSO 集成

在本教程中，你将了解如何将 Veritas Enterprise Vault.cloud SSO 与 h Azure Active Directory (Azure AD) 集成。 将 Veritas Enterprise Vault.cloud SSO 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 Veritas Enterprise Vault.cloud SSO。
* 让用户使用其 Azure AD 帐户自动登录到 Veritas Enterprise Vault.cloud SSO。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Veritas Enterprise Vault.cloud SSO 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Veritas Enterprise Vault.cloud SSO 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Veritas Enterprise Vault.cloud SSO 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>从库中添加 Veritas Enterprise Vault.cloud SSO

要配置 Veritas Enterprise Vault.cloud SSO 与 Azure AD 的集成，需要从库中将 Veritas Enterprise Vault.cloud SSO 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Veritas Enterprise Vault.cloud SSO”。 
1. 从结果面板中选择“Veritas Enterprise Vault.cloud SSO”，然后添加应用程序。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-veritas-enterprise-vaultcloud-sso"></a>配置和测试 Veritas Enterprise Vault.cloud SSO 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Veritas Enterprise Vault.cloud SSO 的 Azure AD SSO。 若要正常运行 SSO，你需要在 Azure AD 用户与 Veritas Enterprise Vault.cloud SSO 相关用户之间建立链接关系。

若要配置和测试 Veritas Enterprise Vault.cloud SSO 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Veritas Enterprise Vault.cloud SSO 单一登录](#configure-veritas-enterprise-vaultcloud-sso-sso)：在应用程序端配置单一登录设置。
    1. **[创建 Veritas Enterprise Vault.cloud SSO 测试用户](#create-veritas-enterprise-vaultcloud-sso-test-user)** ：在 Veritas Enterprise Vault.cloud SSO 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中，在 Veritas Enterprise Vault.cloud SSO 应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`。

    b. 在“标识符”选框中，根据数据中心键入其中一个 URL：：

    | 数据中心| 代码 |
    |----------|----|
    | 北美| `https://auth.lax.archivecloud.net` |
    | 欧洲 | `https://auth.ams.archivecloud.net` |
    | 亚太区| `https://auth.syd.archivecloud.net`|

    c. 在“回复 URL”文本框中，根据数据中心键入其中一个 URL：

    | 数据中心| 代码 |
    |----------|----|
    | 北美| `https://auth.lax.archivecloud.net` |
    | 欧洲 | `https://auth.ams.archivecloud.net` |
    | 亚太区| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > 此值不是真实值。 使用实际登录 URL 更新此值。 请联系 [Veritas Enterprise Vault.cloud SSO 客户端支持团队](https://www.veritas.com/support/.html)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Veritas Enterprise Vault.cloud SSO”部分，根据要求复制相应的 URL。 

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

在本部分中，牛通过授予 B.Simon 访问 Veritas Enterprise Vault.cloud SSO 的权限，允许对方使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Veritas Enterprise Vault.cloud SSO”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-veritas-enterprise-vaultcloud-sso-sso"></a>配置 Veritas Enterprise Vault.cloud SSO 单一登录

若要在 **Veritas Enterprise Vault.cloud SSO** 端配置单一登录，需要将下载的“证书(Base64)”以及从 Azure 门户复制的相应 URL 发送给 [Veritas Enterprise Vault.cloud SSO 支持团队](https://www.veritas.com/support/.html)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>创建 Veritas Enterprise Vault.cloud SSO 测试用户

在本部分中，会在 Veritas Enterprise Vault.cloud SSO 中创建一个名为“Britta Simon”的用户。 与 [Veritas Enterprise Vault.cloud SSO 支持团队](https://www.veritas.com/support/.html)协作，在 Veritas Enterprise Vault.cloud SSO 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Veritas Enterprise Vault.cloud SSO 登录 URL，你从此处启动登录流。 

* 直接转到 Veritas Enterprise Vault.cloud SSO 登陆 URL，并从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的“Veritas Enterprise Vault.cloud SSO”磁贴时，这会重定向到 Veritas Enterprise Vault.cloud SSO 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Veritas Enterprise Vault.cloud SSO 后，即可强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。