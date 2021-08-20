---
title: 教程：Azure Active Directory 与 Iris Intranet 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Iris Intranet 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/04/2021
ms.author: jeedes
ms.openlocfilehash: 59e7d6dcf8a5b384b82c5d3294d5f627d990fb7b
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745468"
---
# <a name="tutorial-azure-active-directory-integration-with-iris-intranet"></a>教程：Azure Active Directory 与 Iris Intranet 集成

本教程介绍如何将 Iris Intranet 与 Azure Active Directory (Azure AD) 集成。 将 Iris Intranet 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Iris Intranet。
* 让用户能够使用其 Azure AD 帐户自动登录到 Iris Intranet。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Iris Intranet 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Iris Intranet 支持 SP 发起的 SSO。

* Iris Intranet 支持实时用户预配。

## <a name="add-iris-intranet-from-the-gallery"></a>从库中添加 Iris Intranet

若要配置 Iris Intranet 与 Azure AD 的集成，需要从库中将 Iris Intranet 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Iris Intranet” 。
1. 从结果面板中选择“Iris Intranet”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-iris-intranet"></a>配置并测试 Iris Intranet 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Iris Intranet 的 Azure AD SSO。 要使 SSO 正常工作，需要在 Azure AD 用户与 Iris Intranet 中的相关用户之间建立关联。

若要配置并测试 Iris Intranet 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Iris Intranet SSO](#configure-iris-intranet-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Iris Intranet 测试用户](#create-iris-intranet-test-user)** - 在 Iris Intranet 中创建 B.Simon 的对应用户，并将其关联到用户在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Iris Intranet”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.irisintranet.com`

    b. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.irisintranet.com` 

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符和登录 URL 更新这些值。 请联系 [Iris Intranet 客户端支持团队](mailto:support@triptic.nl)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

本部分将通过授予 B.Simon 访问 Iris Intranet 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中，选择“Iris Intranet”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-iris-intranet-sso"></a>配置 Iris Intranet SSO

若要在 **Iris Intranet** 端配置单一登录，需要将“应用联合元数据 URL”发送给 [Iris Intranet 支持团队](mailto:support@triptic.nl)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-iris-intranet-test-user"></a>创建 Iris Intranet 测试用户

在本部分，我们将在 Iris Intranet 中创建名为 Britta Simon 的用户。 Iris Intranet 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Iris Intranet 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Iris Intranet 登录 URL，你可以在那里启动登录流。 

* 直接转到 Iris Intranet 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Iris Intranet”磁贴时，会重定向到 Iris Intranet 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Iris Intranet 后，即可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
