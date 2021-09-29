---
title: 教程：Azure Active Directory 与 Zscaler Private Access (ZPA) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zscaler Private Access (ZPA) 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/03/2021
ms.author: jeedes
ms.openlocfilehash: b1e4ddddea8cf6c25d6daa0b16c99f27fcf80c7b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124751357"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>教程：将 Zscaler Private Access (ZPA) 与 Azure Active Directory 集成

本教程介绍如何将 Zscaler Private Access (ZPA) 与 Azure Active Directory (Azure AD) 集成。 将 Zscaler Private Access (ZPA) 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Zscaler Private Access (ZPA)。
* 让用户使用其 Azure AD 帐户自动登录到 Zscaler Private Access (ZPA)。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Zscaler Private Access (ZPA) 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 

* Zscaler Private Access (ZPA) 支持 SP 发起的 SSO。
* Zscaler Private Access (ZPA) 支持[自动用户预配](zscaler-private-access-provisioning-tutorial.md)。
> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>从库中添加 Zscaler Private Access (ZPA)

要配置 Zscaler Private Access (ZPA) 与 Azure AD 的集成，需要从库中将 Zscaler Private Access (ZPA) 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Zscaler Private Access (ZPA)” 。
1. 从结果面板中选择“Zscaler Private Access (ZPA)”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-zscaler-private-access-zpa"></a>配置并测试 Zscaler Private Access (ZPA) 的 Azure AD SSO

使用名为“B.Simon”的测试用户配置并测试 Zscaler Private Access (ZPA) 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Zscaler Private Access (ZPA) 相关用户之间建立链接关系。

若要配置并测试 Zscaler Private Access (ZPA) 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Zscaler Private Access (ZPA) SSO](#configure-zscaler-private-access-zpa-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Zscaler Private Access (ZPA) 测试用户](#create-zscaler-private-access-zpa-test-user) - 在 Zscaler Private Access (ZPA) 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Zscaler Private Access (ZPA)”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，执行以下步骤：

    1. 在“标识符(实体 ID)”文本框中，键入 URL：`https://samlsp.private.zscaler.com/auth/metadata` 

    1. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://samlsp.private.zscaler.com/auth/login?domain=<DOMAIN_NAME>` 

    > [!NOTE]
    > “登录 URL”值不是实际值。 请使用实际的登录 URL 更新此值。 若要获取这些值，请联系 [Zscaler Private Access (ZPA) 客户端支持团队](https://help.zscaler.com/zpa-submit-ticket)。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

   ![证书下载链接](common/metadataxml.png)

1. 在“设置 Zscaler Private Access (ZPA)”部分，根据需要复制相应的 URL。

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `Britta Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`BrittaSimon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 B.Simon 访问 Zscaler Private Access (ZPA) 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Zscaler Private Access (ZPA)”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-zscaler-private-access-zpa-sso"></a>配置 Zscaler Private Access (ZPA) SSO

1. 若要在 Zscaler Private Access (ZPA) 中自动完成配置，需要单击“安装扩展”安装“我的应用安全登录浏览器扩展”。 

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Zscaler Private Access (ZPA)”转到 Zscaler Private Access (ZPA) 应用程序。 在此处，请提供用于登录到 Zscaler Private Access (ZPA) 的管理员凭据。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Zscaler Private Access (ZPA)，请打开新的 Web 浏览器窗口，以管理员身份登录到 Zscaler Private Access (ZPA) 公司站点，然后执行以下步骤：

4. 在菜单左侧单击“管理”，导航到“身份验证”部分，单击“IdP 配置”    。

    ![Zscaler Private Access Administrator 管理](./media/zscalerprivateaccess-tutorial/administration.png)

5. 在右上角，单击“添加 IdP 配置”  。 

    ![Zscaler Private Access Administrator idp](./media/zscalerprivateaccess-tutorial/metadata.png)

6. 在“添加 IdP 配置”页面上，执行以下步骤：
 
    ![Zscaler Private Access Administrator 选择](./media/zscalerprivateaccess-tutorial/select.png)

    a. 在“IdP 元数据文件上传”字段中单击“选择文件”，上传从 Azure AD 下载的元数据文件。

    b. 它会从 Azure AD 读取 **IdP 元数据**，并填充所有字段信息，如下所示。

    ![Zscaler Private Access Administrator 配置](./media/zscalerprivateaccess-tutorial/configure.png)

    c. 从“域”字段中选择域。
    
    d. 单击“ **保存**”。

### <a name="create-zscaler-private-access-zpa-test-user"></a>创建 Zscaler Private Access (ZPA) 测试用户

在本部分中，会在 Zscaler Private Access (ZPA) 中创建一个名为“Britta Simon”的用户。 请与 [Zscaler Private Access (ZPA) 支持团队](https://help.zscaler.com/zpa-submit-ticket)协作，将用户添加到 Zscaler Private Access (ZPA) 平台。

Zscaler Private Access (ZPA) 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参阅[此处](zscaler-private-access-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Zscaler Private Access (ZPA) 登录 URL，可以从那里启动登录流。 

* 直接转到 Zscaler Private Access (ZPA) 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Zscaler Private Access (ZPA)”磁贴时，将会重定向到 Zscaler Private Access (ZPA) 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

在配置 Zscaler Private Access (ZPA) 后，即可强制实施会话控制，从而实时防止组织的敏感数据遭到外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。