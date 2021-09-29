---
title: 教程：Azure Active Directory 与 Lucidchart 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Lucidchart 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 6c180ab24ea5d9c3a997c9e7253c57f1f45d994c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124802125"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>教程：Azure Active Directory 与 Lucidchart 的单一登录 (SSO) 集成

本教程介绍如何将 Lucidchart 与 Azure Active Directory (Azure AD) 集成。 将 Lucidchart 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Lucidchart。
* 让用户使用其 Azure AD 帐户自动登录到 Lucidchart。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Lucidchart 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Lucidchart 支持 SP 发起的 SSO
* Lucidchart 支持[自动用户预配和取消预配](lucidchart-provisioning-tutorial.md)（推荐）。
* Lucidchart 支持恰时用户预配

## <a name="add-lucidchart-from-the-gallery"></a>从库中添加 Lucidchart

若要配置 Lucidchart 与 Azure AD 的集成，需要从库中将 Lucidchart 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中键入“Lucidchart”。
1. 从结果面板中选择“Lucidchart”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-lucidchart"></a>配置并测试 Lucidchart 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 Lucidchart 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Lucidchart 中的相关用户之间建立链接关系。

若要配置并测试 Lucidchart 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Lucidchart SSO](#configure-lucidchart-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Lucidchart 测试用户](#create-lucidchart-test-user)** - 在 Lucidchart 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 Lucidchart 应用程序集成页上，找到“管理”部分，然后选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分，输入以下字段的值：

   在“登录 URL”文本框中键入 URL：`https://chart2.office.lucidchart.com/saml/sso/azure`

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Lucidchart”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    
### <a name="create-an-azure-ad-test-user&quot;></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Lucidchart 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Lucidchart”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-lucidchart-sso&quot;></a>配置 Lucidchart SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Lucidchart 公司站点。

2. 在顶部菜单中，单击 **“团队”**。

    ![团队](./media/lucidchart-tutorial/ic791190.png &quot;组")

3. 单击“应用程序”\>“管理 SAML”。

    ![管理 SAML](./media/lucidchart-tutorial/ic791191.png "管理 SAML")

4. 在 **“SAML 身份验证设置”** 对话框页上，执行以下步骤：

    a. 选择 **“启用 SAML 身份验证”**，然后单击 **“可选”**。

    ![SAML 身份验证设置](./media/lucidchart-tutorial/ic791192.png "SAML 身份验证设置")

    b. 在 **“域”** 文本框中输入你的域，然后单击 **“更改证书”**。

    ![更改证书](./media/lucidchart-tutorial/ic791193.png "更改证书")

    c. 打开所下载的元数据文件，复制其中的内容，然后粘贴到 **“上载元数据”** 文本框中。

    ![上传元数据](./media/lucidchart-tutorial/ic791194.png "上传元数据")

    d. 选择“自动将新用户添加到团队”，然后单击“保存更改”。

    ![保存更改](./media/lucidchart-tutorial/ic791195.png "保存更改")

### <a name="create-lucidchart-test-user"></a>创建 Lucidchart 测试用户

没有操作项可用于配置预配到 Lucidchart 的用户。  当已分配的用户尝试使用访问面板登录到 Lucidchart 时，Lucidchart 会检查该用户是否存在。  

如果尚无用户帐户可用，Lucidchart 会自动创建该帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Lucidchart 登录 URL，可在其中启动登录流。 

* 直接转到 Lucidchart 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Lucidchart 磁贴时，应会自动登录到为其设置了 SSO 的 Lucidchart。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

 配置 Lucidchart 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)