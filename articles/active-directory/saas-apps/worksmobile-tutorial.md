---
title: 教程：Azure Active Directory 与 LINE WORKS 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 LINE WORKS 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: jeedes
ms.openlocfilehash: c460f350a8650380b05eb891819942446f31b6e7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744966"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>教程：Azure Active Directory 与 LINE WORKS 的集成

本教程介绍了如何将 LINE WORKS 与 Azure Active Directory (Azure AD) 进行集成。
将 LINE WORKS 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 LINE WORKS。
* 可让用户使用其 Azure AD 帐户自动登录到 LINE WORKS（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 LINE WORKS 的集成，需要具有以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用单一登录的 LINE WORKS 订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* LINE WORKS 支持 **SP** 发起的 SSO

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-line-works-from-the-gallery"></a>从库中添加 LINE WORKS

若要配置 LINE WORKS 与 Azure AD 的集成，需要从库中将 LINE WORKS 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“LINE WORKS” 。
1. 从结果面板中选择“LINE WORKS”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 LINE WORKS 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 LINE WORKS 相关用户之间建立链接关系。

若要配置并测试 LINE WORKS 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. **[配置 LINE WORKS SSO](#configure-line-works-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 LINE WORKS 测试用户](#create-line-works-test-user)** - 在 LINE WORKS 中创建 Britta Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 LINE WORKS 应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://auth.worksmobile.com/d/login/<domain>/` 

    b. 在“响应 URL”文本框中，使用以下模式键入 URL：`https://auth.worksmobile.com/acs/ <domain>`

    > [!NOTE]
    > 这些不是实际值。 使用实际的登录 URL 和响应 URL 更新这些值。 若要获取这些值，请与 [LINE WORKS 支持团队](https://line.worksmobile.com/jp/en/contactus/)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 LINE WORKS”部分中，根据要求复制相应 URL。

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

本部分将通过授予 B.Simon 访问 LINE WORKS 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“LINE WORKS”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-line-works-sso"></a>配置 LINE WORKS SSO

若要在 **LINE WORKS** 端配置单一登录，请阅读 [LINE WORKS SSO 文档](https://developers.worksmobile.com/jp/document/1001080101)并配置 LINE WORKS 设置。

> [!NOTE]
> 需要将下载的证书文件从 .cert 转换为 .pem


### <a name="create-line-works-test-user"></a>创建 LINE WORKS 测试用户

在本部分中，将在 LINE WORKS 中创建名为 Britta Simon 的用户。 访问 [LINE WORKS 管理员页](https://admin.worksmobile.com)并在 LINE WORKS 平台中添加用户。

### <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

1. 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 LINE WORKS 登录 URL，可在其中启动登录流。 

2. 直接转到 LINE WORKS 登录 URL，并从那里启动登录流。

3. 可以使用 Microsoft 访问面板。 在访问面板中单击 LINE WORKS 磁贴时，将会重定向到 LINE WORKS 登录 URL。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)（访问面板简介）。

## <a name="next-steps"></a>后续步骤

配置 LINE WORKS 后，即可强制实施会话控制，以实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。