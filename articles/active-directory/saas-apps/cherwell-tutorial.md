---
title: 教程：Azure Active Directory 与 Cherwell 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Cherwell 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/14/2021
ms.author: jeedes
ms.openlocfilehash: 9914d7834a04e1a6c6fbdc4137d54e448770c686
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124826665"
---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>教程：Azure Active Directory 与 Cherwell 集成

本教程介绍如何将 Cherwell 与 Azure Active Directory (Azure AD) 集成。 将 Cherwell 与 Azure AD 集成后，可以：

* 可在 Azure AD 中控制谁有权访问 Cherwell。
* 让用户使用其 Azure AD 帐户自动登录到 Cherwell。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Cherwell 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

* 已启用 Cherwell 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Cherwell 支持 **SP** 发起的 SSO

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-cherwell-from-the-gallery"></a>从库中添加 Cherwell

若要配置 Cherwell 与 Azure AD 的集成，需要从库中将 Cherwell 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Cherwell” 。
1. 从结果面板中选择“Cherwell”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-cherwell"></a>配置并测试 Cherwell 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Cherwell 的 Azure AD SSO。 若要使 SSO 正常运行，需要在 Azure AD 用户与 Cherwell 中的相关用户之间建立链接关系。

若要配置并测试 Cherwell 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    2. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. **[配置 Cherwell SSO](#configure-cherwell-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Cherwell 测试用户](#create-cherwell-test-user)** - 在 Cherwell 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Cherwell”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<companyname>.cherwellondemand.com/cherwellclient`。

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：  `https://*.cherwellondemand.com`
    
    > [!NOTE]
    > 此值不是真实值。 使用实际的登录 URL 和回复 URL 更新该值。 请联系 [Cherwell 客户端支持团队](https://cherwellsupport.com/CherwellPortal)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Cherwell”部分中，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B. Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”。  
1. 在屏幕顶部选择“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“姓名”字段中输入 **B.Simon**。  
   1. 在“用户名”字段中输入 `<username>@<companydomain>.<extension>`。 例如：`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值   。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Cherwell 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Cherwell”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-cherwell-sso"></a>配置 Cherwell SSO

若要在 **Cherwell** 端配置单一登录，需要将下载的 **证书 (Base64)** 以及从 Azure 门户复制的相应 URL 发送给 [Cherwell 支持团队](https://cherwellsupport.com/CherwellPortal)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

> [!NOTE]
> 必须由 Cherwell 支持团队执行实际的 SSO 配置。 在为订阅启用了 SSO 后，将收到通知。

### <a name="create-cherwell-test-user"></a>创建 Cherwell 测试用户

要使 Azure AD 用户能够登录到 Cherwell，必须将其预配到 Cherwell 中。 对于 Cherwell，需要由 [Cherwell 支持团队](https://cherwellsupport.com/CherwellPortal)创建用户帐户。

> [!NOTE]
> 可以使用 Cherwell 提供的任何其他 Cherwell 用户帐户创建工具或 API 来预配 Azure Active Directory 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Cherwell 登录 URL，你可以从此处启动登录流。 

* 直接转到 Cherwell 登录 URL，并从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Cherwell 磁贴时，应当会自动登录到已为其设置了 SSO 的 Cherwell。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Cherwell 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)