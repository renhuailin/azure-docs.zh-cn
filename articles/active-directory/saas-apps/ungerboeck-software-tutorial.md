---
title: 教程：Azure Active Directory 与 Ungerboeck Software 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Ungerboeck Software 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: 6f9a77fb3052edc344d35ec1c5ea2b12629698c6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124808140"
---
# <a name="tutorial-integrate-ungerboeck-software-with-azure-active-directory"></a>教程：将 Ungerboeck Software 与 Azure Active Directory 集成

本教程介绍如何将 Ungerboeck Software 与 Azure Active Directory (Azure AD) 集成。 将 Ungerboeck Software 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Ungerboeck Software。
* 让用户使用其 Azure AD 帐户自动登录到 Ungerboeck Software。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Ungerboeck Software 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Ungerboeck Software 支持 **SP** 发起的 SSO。

## <a name="add-ungerboeck-software-from-the-gallery"></a>从库中添加 Ungerboeck Software

要配置 Ungerboeck Software 与 Azure AD 的集成，需要从库中将 Ungerboeck Software 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Ungerboeck Software”   。
1. 从结果面板中选择“Ungerboeck Software”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-ungerboeck-software"></a>配置并测试 Ungerboeck Software 的 Azure AD SSO

使用名为 B. Simon 的测试用户配置和测试 Ungerboeck Software 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Ungerboeck Software 相关用户之间建立链接关系。

若要配置并测试 Ungerboeck Software 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Ungerboeck Software SSO](#configure-ungerboeck-software-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Ungerboeck Software 测试用户](#create-ungerboeck-software-test-user)，在 Ungerboeck Software 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Ungerboeck Software”应用程序集成页上，找到“管理”部分，并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，执行以下步骤：

    1. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.ungerboeck.com/prod` 

    1. 在“标识符(实体 ID)”文本框中，使用以下模式之一键入 URL：
    
    *  **对于生产环境**：

       - `https://<SUBDOMAIN>.ungerboeck.com/prod`
       - `https://<SUBDOMAIN>.ungerboeck.net/prod`
       - `https://<SUBDOMAIN>.ungerboeck.io/prod`

   * **对于测试环境**：

     - `https://<SUBDOMAIN>.ungerboeck.com/test`
     - `https://<SUBDOMAIN>.ungerboeck.net/test`
     - `https://<SUBDOMAIN>.ungerboeck.io/test`

   > [!NOTE]
   > 这些不是实际值。 请使用本教程稍后在“配置 Ungerboeck Software 单一登录”部分介绍的实际“登录 URL”和“标识符”更新这些值。 

1. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

1. 在“SAML 签名证书”部分中，复制 **指纹** 并将其保存在计算机上。

    ![复制指纹值](common/copy-thumbprint.png)

1. 在“设置 Ungerboeck Software”部分，根据要求复制相应的 URL。 

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

在本部分，我们通过授予 B.Simon 访问 Ungerboeck Software 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Ungerboeck Software”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-ungerboeck-software-sso"></a>配置 Ungerboeck Software SSO

若要在 **Ungerboeck Software** 端配置单一登录，需要将“指纹值”以及从 Azure 门户复制的相应 URL 发送给 [Ungerboeck Software 支持团队](mailto:Rhonda.Jannings@ungerboeck.com)  。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-ungerboeck-software-test-user"></a>创建 Ungerboeck Software 测试用户

在本部分，我们将在 Ungerboeck Software 中创建名为 B.Simon 的用户。 与 [Ungerboeck Software 支持团队](mailto:Rhonda.Jannings@ungerboeck.com)协作，在 Ungerboeck Software 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Ungerboeck Software 登录 URL，你可以从那里启动登录流。 

* 直接转到 Ungerboeck Software 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Ungerboeck Software”磁贴时，会重定向到 Ungerboeck Software 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Ungerboeck Software 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。