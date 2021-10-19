---
title: 教程：Azure AD SSO 与 Percolate 集成
description: 本教程介绍如何在 Azure Active Directory 与 Percolate 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/06/2021
ms.author: jeedes
ms.openlocfilehash: f07ddbb4b0692f82a3915559925ef4807304e8da
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856145"
---
# <a name="tutorial-azure-ad-sso-integration-with-percolate"></a>教程：Azure AD SSO 与 Percolate 集成

本教程介绍如何将 Percolate 与 Azure Active Directory (Azure AD) 集成。 将 Percolate 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Percolate。
* 让用户使用其 Azure AD 帐户自动登录到 Percolate。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Percolate 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 Percolate 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD 单一登录。

* Percolate 支持 SP 和 IdP 发起的 SSO。

## <a name="add-percolate-from-the-gallery"></a>从库中添加 Percolate

若要配置 Percolate 与 Azure AD 的集成，需要从库中将 Percolate 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Percolate” 。
1. 从结果面板中选择“Percolate”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-percolate"></a>配置并测试 Percolate 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Percolate 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Percolate 相关用户之间建立链接关系。

若要配置并测试 Percolate 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Percolate SSO](#configure-percolate-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Percolate 测试用户](#create-percolate-test-user)** - 在 Percolate 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Percolate”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”对话框中，无需执行任何操作即可在 IdP 发起的模式下配置应用程序。  该应用已与 Azure 集成。

5. 如果要在 SP 发起的模式下配置应用程序，请选择“设置其他 URL”，然后在“登录 URL”框中输入 https://percolate.com/app/login  。

6. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，选择“复制”图标，以复制“应用联合元数据 URL”     。 保存此 URL。

    ![复制应用联合元数据 URL](common/copy-metadataurl.png)

7. 在“设置 Percolate”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 Percolate 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中，选择“Percolate”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-percolate-sso"></a>配置 Percolate SSO

1. 在新 Web 浏览器窗口中，以管理员身份登录到 Percolate。

2. 在主页左侧，选择“设置”  ：
    
    ![选择“设置”](./media/percolate-tutorial/menu.png)

3. 在左窗格中，选择“组织”下的“SSO”   ：

    ![选择“组织”下的“SSO”](./media/percolate-tutorial/metadata.png)

    1. 在“登录 URL”框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    1. 在“实体 ID”框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    1. 在记事本中打开从 Azure 门户下载的 base-64 编码证书。 复制其内容并粘贴到“x509 证书”  框中。

    1. 在“电子邮件属性”  框中，输入“emailaddress”  。

    1. “标识提供者元数据 URL”  框是可选字段。 如果从 Azure 门户复制“应用联合元数据 Url”  ，则可以将它粘贴到此框中。

    1. 在“是否应该对 AuthNRequests 签名?”列表中，选择“否”。  

    1. 在“启用 SSO 自动预配”列表中，选择“否”。  

    1. 选择“保存”。 

### <a name="create-percolate-test-user"></a>创建 Percolate 测试用户

若要使 Azure AD 用户能够登录到 Percolate，需要将其添加到 Percolate 中。 需要手动添加它们。

若要创建用户帐户，请执行以下步骤：

1. 以管理员身份登录到 Percolate。

2. 在左窗格中，选择“组织”下的“用户”   。 选择“新建用户”  ：

    ![选择“新建用户”](./media/percolate-tutorial/users.png)

3. 在“创建用户”页上执行以下步骤。 

    ![“创建用户”页](./media/percolate-tutorial/new-user.png)

    1. 在“电子邮件”  框中，输入用户的电子邮件地址。 例如，brittasimon@contoso.com 。

    1. 在“全名”  框中，输入用户的姓名。 例如，Brittasimon  。

    1. 选择“创建用户”  。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Percolate 登录 URL，可以从那里启动登录流。  

* 直接转到 Percolate 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Percolate。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 单击“我的应用”中的“Percolate”磁贴时，如果该应用程序是在 SP 模式下配置的，系统会将你重定向到应用程序登录页来启动登录流；如果它是在 IDP 模式下配置的，你会自动登录到为其设置了 SSO 的 Percolate。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Percolate 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。