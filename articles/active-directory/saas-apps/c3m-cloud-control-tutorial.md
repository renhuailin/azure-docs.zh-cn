---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 C3M Cloud Control 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 C3M Cloud Control 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/11/2021
ms.author: jeedes
ms.openlocfilehash: 01356cb42143eb72e9a8c0126dd0f40b111c3b84
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729373"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-c3m-cloud-control"></a>教程：Azure Active Directory 与 C3M Cloud Control 的单一登录 (SSO) 集成

本教程介绍如何将 C3M Cloud Control 与 Azure Active Directory (Azure AD) 集成。 将 C3M Cloud Control 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 C3M Cloud Control。
* 让用户使用其 Azure AD 帐户自动登录到 C3M Cloud Control。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 C3M Cloud Control 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* C3M Cloud Control 支持 SP 发起的 SSO。
* C3M Cloud Control 支持实时用户预配。

## <a name="add-c3m-cloud-control-from-the-gallery"></a>从库中添加 C3M Cloud Control

若要配置 C3M Cloud Control 与 Azure AD 的集成，需要从库中将 C3M Cloud Control 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“C3M Cloud Control” 。
1. 从结果面板中选择“C3M Cloud Control”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-c3m-cloud-control"></a>配置并测试 C3M Cloud Control 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 C3M Cloud Control 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 C3M Cloud Control 相关用户之间建立链接关系。

若要配置并测试 C3M Cloud Control 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 C3M Cloud Control SSO](#configure-c3m-cloud-control-sso) - 在应用程序端配置单一登录设置。
    1. [创建 C3M Cloud Control 测试用户](#create-c3m-cloud-control-test-user) - 在 C3M Cloud Control 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“C3M Cloud Control”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<C3MCLOUDCONTROL_ACCESS_URL>/api/sso/saml`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<C3MCLOUDCONTROL_ACCESS_URL>/api/sso/saml`

    c. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<C3MCLOUDCONTROL_ACCESS_URL>` 

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [C3M Cloud Control 客户端支持团队](mailto:support@c3m.io)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 C3M Cloud Control”部分中，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 C3M Cloud Control 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“C3M Cloud Control”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-c3m-cloud-control-sso"></a>配置 C3M Cloud Control SSO

 若要为 C3M Cloud Control 配置 SSO，请遵循[文档](https://c3m.freshdesk.com/support/solutions/articles/44001946272-configuring-sso-using-saml-azure)进行操作。

### <a name="create-c3m-cloud-control-test-user"></a>创建 C3M Cloud Control 测试用户

在本部分中，将在 C3M Cloud Control 中创建名为 B.Simon 的用户。 C3M Cloud Control 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 C3M Cloud Control 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这将重定向到 C3M Cloud Control 登录 URL，可以从那里启动登录流。 

* 直接转到 C3M Cloud Control 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 当你单击“我的应用”中的 C3M Cloud Control 磁贴时，系统会将你重定向到 C3M Cloud Control 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 C3M Cloud Control 后，即可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。