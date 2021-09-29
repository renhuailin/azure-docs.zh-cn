---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Sonarqube 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Sonarqube 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/25/2021
ms.author: jeedes
ms.openlocfilehash: e6f366fcc1f268f3c0fa629bd1903be4f8c8d2b8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785793"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Sonarqube 集成

本教程介绍如何将 Sonarqube 与 Azure Active Directory (Azure AD) 集成。 将 Sonarqube 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Sonarqube。
* 让用户使用其 Azure AD 帐户自动登录到 Sonarqube。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Sonarqube 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Sonarqube 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-sonarqube-from-the-gallery"></a>从库中添加 Sonarqube

要配置 Sonarqube 与 Azure AD 的集成，需要从库中将 Sonarqube 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Sonarqube”   。
1. 从结果面板中选择“Sonarqube”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-sonarqube"></a>配置并测试 Sonarqube 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 Sonarqube 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Sonarqube 相关用户之间建立链接关系。

若要配置并测试 Sonarqube 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Sonarqube SSO](#configure-sonarqube-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Sonarqube 测试用户](#create-sonarqube-test-user)** - 在 Sonarqube 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Sonarqube”应用程序集成页上，找到“管理”部分，然后选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://servicessonar.<YOUR_ORGANIZATION>.com`

    b. 在“登录 URL”文本框中，键入以下 URL 之一： 

    * **对于生产环境**

        `https://servicessonar.corp.microsoft.com/`

    * **对于开发环境**

        `https://servicescode-dev.westus.cloudapp.azure.com`

    > [!NOTE]
    > 此值不是真实值。 需要使用实际的回复 URL 更新该值（本教程稍后将会介绍）。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Sonarqube”部分中，根据要求复制相应的 URL  。

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

在本部分中，将通过授予 B.Simon 访问 Sonarqube 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Sonarqube”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-sonarqube-sso"></a>配置 Sonarqube SSO

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 Sonarqube 公司站点。

1. 单击“管理”>“配置”>“安全性”，然后转到“SAML 插件”执行以下步骤 。

1. 从 IdP 元数据复制以下详细信息，并将其粘贴到 SonarQube 插件中的相应文本字段。
    1. IdP 实体 ID
    2. 登录 URL
    3. X.509 证书 

1. 保存所有详细信息。

    ![saml 插件 IDP](./media/sonarqube-tutorial/metadata.png)

1. 在 **SAML** 页上执行以下步骤：

    ![Sonarqube 配置](./media/sonarqube-tutorial/configuration.png)

    a. 将“启用”  选项切换到“是”  。

    b. 在“应用程序 ID”文本框中输入名称，例如 sonarqube   。

    c. 在“提供程序名称”文本框中输入名称，例如 SAML   。

    d. 在“提供程序 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    e. 在“SAML 登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    f. 在记事本中打开 Base64 编码的证书，复制其内容，然后将其粘贴到“提供程序证书”文本框中  。

    g. 在“SAML 用户登录名属性”  文本框中，输入值 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。

    h. 在“SAML 用户名称属性”  文本框中，输入值 `http://schemas.microsoft.com/identity/claims/displayname`。

    i. 在“SAML 用户电子邮件属性”  文本框中，输入值 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。

    j. 单击“ **保存**”。

### <a name="create-sonarqube-test-user"></a>创建 Sonarqube 测试用户

在本部分中，将在 Sonarqube 中创建名为 B.Simon 的用户。 请与 [Sonarqube 客户端支持团队](https://www.sonarsource.com/support/)协作，将用户添加到 Sonarqube 平台。 使用单一登录前，必须先创建并激活用户。 

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Sonarqube 登录 URL，可以在其中启动登录流。 

* 直接转到 Sonarqube 登录 URL，并在其中启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Sonarqube 磁贴时，将会重定向到 Sonarqube 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

* 配置 Sonarqube 后，即可强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。