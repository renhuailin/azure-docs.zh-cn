---
title: 教程：Azure Active Directory 与 Infinite Campus 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Infinite Campus 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: jeedes
ms.openlocfilehash: 9ded926c5798147fc53f047b8cdf2b220c815f05
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757242"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>教程：Azure Active Directory 与 Infinite Campus 的集成

本教程介绍如何将 Infinite Campus 与 Azure Active Directory (Azure AD) 集成。 将 Infinite Campus 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Infinite Campus。
* 让用户使用其 Azure AD 帐户自动登录到 Infinite Campus。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Infinite Campus 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Infinite Campus 单一登录的订阅。
* 若要完成配置，你至少需要是 Azure Active Directory 管理员，并充当“学生信息系统 (SIS)”的校园产品安全角色。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Infinite Campus 支持 SP 发起的 SSO。

## <a name="add-infinite-campus-from-the-gallery"></a>从库中添加 Infinite Campus

若要配置 Infinite Campus 与 Azure AD 的集成，需要从库中将 Infinite Campus 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Infinite Campus” 。
1. 在结果面板中选择“Infinite Campus”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-infinite-campus"></a>配置并测试 Infinite Campus 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Infinite Campus 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Infinite Campus 中的相关用户之间建立链接关系。

若要配置并测试 Infinite Campus 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Infinite Campus SSO](#configure-infinite-campus-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Infinite Campus 测试用户](#create-infinite-campus-test-user) - 在 Infinite Campus 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Infinite Campus”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分执行以下步骤（请注意，域根据托管模型而异，但“完全限定的域”值必须与 Infinite Campus 安装相匹配）：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL： `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

5. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

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

在本部分，通过授予 B.Simon 访问 Infinite Campus 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Infinite Campus”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-infinite-campus-sso"></a>配置 Infinite Campus SSO

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Infinite Campus。

2. 在菜单左侧，单击“系统管理”。

    ![管理](./media/infinitecampus-tutorial/admin.png)

3. 导航到“用户安全性” > “SAML 管理” > “SSO 服务提供程序配置”。  

    ![SAML](./media/infinitecampus-tutorial/security.png)

4. 在“SSO 服务提供程序配置”页上执行以下步骤：

    ![SSO](./media/infinitecampus-tutorial/configuration.png)

    a. 选择“启用 SAML 单一登录”。

    b. 编辑“可选属性名称”以包含“名称” 。

    c. 在“选择检索标识提供者(IDP)服务器数据的选项”部分，选择“元数据 URL”，在框中粘贴从 Azure 门户复制的“应用联合元数据 URL”值，然后单击“同步”。

    d. 单击“同步”后，将在“SSO 服务提供程序配置”页中自动填充值。 可以验证这些值是否与上述步骤 4 中显示的值相匹配。

    e. 单击“保存”  。

### <a name="create-infinite-campus-test-user"></a>创建 Infinite Campus 测试用户

Infinite Campus 采用以人口统计为中心的体系结构。 请联系 [Infinite Campus 支持团队](mailto:sales@infinitecampus.com)，以便在 Infinite Campus 平台中添加用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Infinite Campus 登录 URL，可从那里启动登录流。 

* 直接转到 Infinite Campus 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Infinite Campus 磁贴时，会重定向到 Infinite Campus 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Infinite Campus 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。