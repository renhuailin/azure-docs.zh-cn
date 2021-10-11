---
title: 教程：Azure Active Directory 与 Soloinsight-CloudGate SSO 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Soloinsight-CloudGate SSO 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: f21c120374a7e6a42c5bb98a1db8687f00aba548
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778469"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>教程：将 Soloinsight-CloudGate SSO 与 Azure Active Directory 集成

本教程介绍如何将 Soloinsight-CloudGate SSO 与 Azure Active Directory (Azure AD) 集成。 将 Soloinsight-CloudGate SSO 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Soloinsight-CloudGate SSO。
* 让用户使用其 Azure AD 帐户自动登录到 Soloinsight-CloudGate SSO。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 Soloinsight-CloudGate SSO 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Soloinsight-CloudGate SSO 支持 **SP** 发起的 SSO。
* Soloinsight-CloudGate SSO 支持[自动用户预配](soloinsight-cloudgate-sso-provisioning-tutorial.md)。

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>从库中添加 Soloinsight-CloudGate SSO

若要配置 Soloinsight-CloudGate SSO 与 Azure AD 的集成，需要从库中将 Soloinsight-CloudGate SSO 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Soloinsight-CloudGate SSO”。
1. 从结果面板中选择“Soloinsight-CloudGate SSO”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-soloinsight-cloudgate-sso"></a>配置并测试 Soloinsight-CloudGate SSO 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Soloinsight-CloudGate SSO 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Soloinsight-CloudGate SSO 相关用户之间建立链接关系。

若要配置并测试 Soloinsight-CloudGate SSO 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso) - 在应用程序端配置单一登录。
    1. [创建 Soloinsight-CloudGate SSO 测试用户](#create-soloinsight-cloudgate-sso-test-user) - 在 Soloinsight-CloudGate SSO 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Soloinsight-CloudGate SSO”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，执行以下步骤：

    1. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.sigateway.com/login` 

    1. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.sigateway.com/process/sso` 

   > [!NOTE]
   > 这些不是实际值。 请使用本教程稍后在“配置 Soloinsight-CloudGate SSO 单一登录”部分介绍的实际“登录 URL”和“标识符”更新这些值。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

   ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Soloinsight-CloudGate SSO”部分，根据要求复制相应的 URL。

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

在本部分，通过授予 B.Simon 访问 Soloinsight-CloudGate SSO 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中，选择“Soloinsight-CloudGate SSO”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-soloinsight-cloudgate-sso"></a>配置 Soloinsight-CloudGate SSO

1. 若要在 Soloinsight-CloudGate SSO 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Soloinsight-CloudGate SSO”，此时将定向到 Soloinsight-CloudGate SSO 应用程序。 在此处，提供管理员凭据以登录到 Soloinsight-CloudGate SSO。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-8。

    ![设置配置](common/setup-sso.png)

3. 如果想要手动设置 Soloinsight-CloudGate SSO，请打开新的 Web 浏览器窗口并以管理员身份登录 Soloinsight-CloudGate SSO 公司站点，然后执行以下步骤：

4. 若要获取配置基本 SAML 时要在 Azure 门户中粘贴的值，请使用凭据登录到 CloudGate Web 门户，然后访问 SSO 设置，其路径如下：“主页”>“管理”>“系统设置”>“常规”。

    ![CloudGate SSO 设置](./media/soloinsight-cloudgate-sso-tutorial/main-settings.png)

5. **SAML 使用者 URL**

    * 复制“SAML 使用者 URL”和“重定向 URL”字段旁边提供的链接，并分别将其粘贴到 Azure 门户的“基本 SAML 配置”部分中的“标识符(实体 ID)”和“回复 URL”字段    。

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/identifier.png)

6. **SAML 签名证书**

    * 转到从 Azure 门户的“SAML 签名证书”列表下载的“证书(Base64)”文件的源，并右键单击它。 从列表中选择“使用 Notepad++ 进行编辑”选项。 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * 复制“证书(Base64)”Notepad++ 文件中的内容。

        ![证书复制](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * 在 CloudGate Web 门户的“SSO 设置”中的“证书”字段内粘贴该内容，然后单击“保存”按钮。

        ![证书门户](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **默认组**

    * 从 CloudGate Web 门户中“默认组”选项下拉列表内选择“企业管理员”

        ![默认组](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD 标识符和登录 URL**

    * 从 Azure 门户的“设置 Soloinsight CloudGate SSO”配置中复制的“登录 URL”将在 CloudGate Web 门户的“SSO 设置”部分中输入 。

    * 在 CloudGate Web 门户的“AD 登录 URL”字段中，粘贴 Azure 门户中提供的“登录 URL”链接 。

    * 在 CloudGate Web 门户的“AD 标识符”字段中，粘贴 Azure 门户中提供的“Azure AD 标识符”链接 

        ![AD 登录](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>创建 Soloinsight-CloudGate SSO 测试用户

若要创建测试用户，请从 CloudGate Web 门户的主菜单中选择“员工”，并填写“添加新员工”窗体。 要分配给测试用户的权限级别为“企业管理员”。填写所有必填字段后，单击“创建”。

![员工测试](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

> [!NOTE]
> Soloinsight-CloudGate SSO 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./soloinsight-cloudgate-sso-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这将重定向到 Soloinsight-CloudGate SSO 登录 URL，你可以从那里启动登录流。 

* 直接转到 Soloinsight-CloudGate SSO 登录 URL，从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Soloinsight-CloudGate SSO 磁贴时，将会重定向到 Soloinsight-CloudGate SSO 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Soloinsight-CloudGate SSO 后，即可强制实施会话控制，实时防止组织的敏感数据遭到外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。