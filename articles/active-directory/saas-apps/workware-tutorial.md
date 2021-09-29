---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Workware 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workware 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: 5ed4be15346e1f3248773472b68e8972457c34fb
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785451"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Workware 集成

在本教程中，了解如何将 Workware 与 Azure Active Directory (Azure AD) 集成。 将 Workware 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Workware。
* 让用户使用其 Azure AD 帐户自动登录到 Workware。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Workware 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Workware 支持 IDP 发起的 SSO

## <a name="adding-workware-from-the-gallery"></a>从库中添加 Workware

若要配置 Workware 与 Azure AD 的集成，需要从库中将 Workware 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Workware” 。
1. 从结果面板中选择“Workware”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>为 Workware 配置并测试 Azure AD SSO

使用名为“B.Simon”的测试用户配置和测试 Workware 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Workware 相关用户之间建立链接关系。

若要配置并测试 Workware 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Workware SSO](#configure-workware-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Workware 测试用户](#create-workware-test-user)** - 在 Workware 中创建 B.Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Workware”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“使用 SAML 设置单一登录”页上，输入以下字段的值： 

    a. 在“标识符”文本框中，使用以下模式键入 URL：`<WORKWARE_URL>/WW/AuthServices`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 若要获取这些值，请与 [Workware 客户端支持团队](mailto:support@activeops.com)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Workware”部分中，根据要求复制相应 URL。

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

在本部分中，将通过授予 B.Simon 访问 Workware 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Workware”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-workware-sso"></a>配置 Workware SSO

若要在 Workware 中使用 SSO 功能，需要完成以下设置：

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>为 Workware 系统管理员启用 SSO 权限

* 若要允许 Workware 系统管理员设置 SSO 身份验证，需要（在“系统设置”>“角色权限”屏幕的“管理”>“系统”配置权限类别中）为 Workware 系统管理员启用 SSO 身份验证权限。

    ![SSO 身份验证权限](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>在 Workware 中设置 SSO 身份验证

1. 转到“系统设置”页，然后单击“SSO 身份验证” 

1. 在“SSO 身份验证”部分，单击“添加 SSO 身份验证”按钮，然后执行以下步骤 ： 

    ![SSO 身份验证](./media/workware-tutorial/authentication.png)

    1. 在“外部标识提供者”中，提供 IDP 的名称。
    1. 选择“SAML2.0”作为“身份验证类型” 
    1. 在“标识提供者登录 URL”文本框中，输入从 Azure 门户复制的“登录 URL”值 。
    1. 在“标识提供者颁发者 URL”文本框中，输入从 Azure 门户复制的“Azure AD 标识符”值 。
    1. 在“标识提供者注销 URL”文本框中，输入从 Azure 门户复制的“注销 URL”值 。
    1. 单击“启用”。
    1. 将已下载的“证书”从 Azure 门户上传到“标识提供者证书”中 。
    1. 单击“保存” 


### <a name="create-workware-test-user"></a>创建 Workware 测试用户

1. 以管理员身份登录到 Workware 网站。

1. 选择“管理员”>“创建”或“查看”>“用户帐户”>“新增帐户”

1. 在下面的页中执行以下步骤。

    ![测试用户](./media/workware-tutorial/create-user.png)

    a. 在“名称”字段中提供有效的名称。

    b. 选择“身份验证类型”作为“SSO” 。

    c. 输入必填字段，然后单击“保存”。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应该会自动登录到为其设置了 SSO 的 Workware

* 可以使用 Microsoft 访问面板。 在访问面板中单击“Workware”磁贴时，应会自动登录到为其设置了 SSO 的 Workware。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)（访问面板简介）。


## <a name="next-steps"></a>后续步骤

配置 Workware 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。