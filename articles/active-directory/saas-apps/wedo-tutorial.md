---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 WEDO 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 WEDO 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 68049d5eb2d20c6b88319925892f0dda642f392c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778450"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wedo"></a>教程：Azure Active Directory 单一登录 (SSO) 与 WEDO 集成

本教程介绍如何将 WEDO 与 Azure Active Directory (Azure AD) 集成。 将 WEDO 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 WEDO。
* 让用户使用其 Azure AD 帐户自动登录到 WEDO。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 WEDO 单一登录 (SSO) 的订阅。 请联系 [WEDO 客户端支持团队](mailto:info@wedo.swiss)获取 SSO 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* WEDO 支持 SP 和 IDP 发起的 SSO。
* WEDO 支持[自动用户预配](wedo-provisioning-tutorial.md)。

## <a name="add-wedo-from-the-gallery"></a>从库中添加 WEDO

若要配置 WEDO 与 Azure AD 的集成，需要从库中将 WEDO 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **WEDO**。
1. 在结果面板中选择“WEDO”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-wedo"></a>配置并测试 WEDO 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 WEDO 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 WEDO 中的相关用户之间建立链接关系。

若要配置并测试 WEDO 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 WEDO SSO](#configure-wedo-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 WEDO 测试用户](#create-wedo-test-user)** - 在 WEDO 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“WEDO”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.wedo.swiss/sp/acs`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.wedo.swiss/sp/acs`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.wedo.swiss/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [WEDO 客户端支持团队](mailto:info@wedo.swiss)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. WEDO 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    | 名称 | 源属性|
    | ------------ | --------- |
    | 电子邮件 | user.email |
    | firstName | user.firstName |
    | lastName | user.lasttName |
    | userName | user.userName |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 WEDO”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 WEDO 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中选择“WEDO”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-wedo-sso"></a>配置 WEDO SSO

遵循以下步骤在 WEDO 中启用 Azure AD SSO。

1. 登录到 [WEDO](https://login.wedo.swiss/)。 需有 **管理员角色**。
1. 在“配置文件设置”中，选择“网络设置”部分中的“身份验证”菜单。
1. 在“SAML 身份验证”页上执行以下步骤：

   ![“SAML 身份验证”链接](media/wedo-tutorial/network-security-authentification.png)

   a. 启用“SAML 身份验证”。

   b. 选择“标识提供者元数据(XML)”选项卡。

   c. 在记事本中打开从 Azure 门户下载的“联合元数据 XML”，复制元数据 XML 的内容，并将其粘贴到“X.509 证书”文本框中。

   d. 单击“保存”  。

### <a name="create-wedo-test-user"></a>创建 WEDO 测试用户

创建 WEDO 测试用户在本部分，你将在 WEDO 中创建名为 Bob Simon 的用户。 使用的信息必须与“创建 Azure AD 测试用户”中的信息相匹配。

1. 在 WEDO 的“配置文件设置”中，选择“网络设置”部分中的“用户”。
1. 单击“添加用户”  。
1. 在“添加用户”弹出窗口中填写用户的信息

    a. 名字 `B`。

    b. 姓氏 `Simon`。

    c. 输入电子邮件 `username@companydomain.extension`。 例如，`B.Simon@contoso.com`。 必须使用域与公司短名称相同的电子邮件。

    d. 用户类型 `User`。

    e. 单击“创建用户”  。

    f. 在“选择团队”页上，单击“保存”。

    g.  在“邀请用户”页上，单击“是”。

1. 使用电子邮件中的链接验证用户

> [!NOTE]
> 若要创建一个虚构用户（上述电子邮件在你的网络中不存在），请联系[我们的支持人员](mailto:info@wedo.swiss)来验证用户*。

> [!NOTE]
> WEDO 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./wedo-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这将重定向到 WEDO 登录 URL，你可以从那里启动登录流。  

* 直接转到 WEDO 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 WEDO。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“WEDO”磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，你应会自动登录到为其设置了 SSO 的 WEDO。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 WEDO 后，可以强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
