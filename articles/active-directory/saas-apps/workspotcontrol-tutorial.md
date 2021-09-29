---
title: 教程：Azure Active Directory 与 Workspot Control 集成 | Microsoft Docs
description: 了解如何为 Azure Active Directory 和 Workspot Control 配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/25/2021
ms.author: jeedes
ms.openlocfilehash: 261278419ffb82d73bd3bc09e1bf6ffab772ebc9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744978"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>教程：Azure Active Directory 与 Workspot Control 集成

在本教程中，你将了解如何将 Workspot Control 与 Azure Active Directory (Azure AD) 集成。 将 Workspot Control 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Workspot Control。
* 让用户使用其 Azure AD 帐户自动登录到 Workspot Control。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Workspot Control 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

* 启用了 Workspot Control 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Workspot Control 支持 SP 和 IDP 发起的 SSO。

## <a name="add-workspot-control-from-the-gallery"></a>从库中添加 Workspot Control

要配置 Workspot Control 与 Azure AD 的集成，需要从库中将 Workspot Control 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Workspot Control” 。
1. 从结果面板中选择“Workspot Control”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-workspot-control"></a>配置并测试 Workspot Control 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Workspot Control 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Workspot Control 相关用户之间建立链接关系。

若要配置并测试 Workspot Control 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Workspot Control SSO](#configure-workspot-control-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Workspot Control 测试用户](#create-workspot-control-test-user)** - 在 Workspot Control 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Workspot Control”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 若要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤： 

    1. 在“标识符”文本框中，使用以下模式键入 URL：<br/>
    `https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata`

    1. 在“回复 URL”文本框中，使用以下模式键入 URL：<br/>
    `https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion`

5. 如果要在 SP 发起的模式下配置应用程序，选择“设置其他 URL”。

    在“登录 URL”文本框中，使用以下模式键入 URL： <br/>
    `https://<<i></i>INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的标识符、回复 URL 和登录 URL 替换这些值。 请联系 [Workspot Control 客户端支持团队](mailto:support@workspot.com)来获取这些值。 也可以参考 Azure 门户的“基本 SAML 配置”部分中显示的模式。

6. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，选择“下载”以根据要求从可用选项中下载“证书(Base64)”。 然后将其保存到计算机上。

    ![证书 (Base64) 下载链接](common/certificatebase64.png)

7. 在“设置 Workspot Control”部分中，根据要求复制响应的 URL：

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

在本部分，你将通过授予 B.Simon 访问 Workspot Control 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Workspot Control”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-workspot-control-sso"></a>配置 Workspot Control SSO

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Workspot Control。

2. 在页面顶部的工具栏中，选择“设置”。，然后选择“SAML”。

    ![安装选项](./media/workspotcontrol-tutorial/setup.png)

3. 在“安全断言标记语言配置”窗口中，执行以下步骤：
 
    ![“安全断言标记语言配置”窗口](./media/workspotcontrol-tutorial/security.png)

    1. 在“实体 ID”框中，粘贴从 Azure 门户复制的“Azure AD 标识符”。

    1. 在“登录服务 URL”框中，粘贴从 Azure 门户复制的“登录 URL”。

    1. 在“注销服务 URL”框中，粘贴从 Azure 门户复制的“登录 URL”。

    1. 选择“更新文件”，将从 Azure 门户下载的 base-64 编码证书上传到 X.509 证书中。

    1. 选择“保存”。 

### <a name="create-workspot-control-test-user"></a>创建 Workspot Control 测试用户

若要使 Azure AD 用户能够登录 Workspot Control，必须将他们预配到 Workspot Control 中。 预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录 Workspot Control。

2. 在页面顶部的工具栏中，选择“用户”。，然后选择“添加用户”。

    ![“用户”选项](./media/workspotcontrol-tutorial/user.png)

3. 在“添加新用户”窗口中，执行以下步骤：

    ![“添加新用户”窗口](./media/workspotcontrol-tutorial/new-user.png)

    1. 在“名字”框中，输入用户的名字，例如 Britta。

    1. 在“姓氏”框中，输入用户的姓氏，例如 Simon。

    1. 在“电子邮件”框中，输入用户的电子邮件地址，如 Brittasimon@contoso.<i></i>com。

    1. 从“角色”下拉列表中选择相应的用户角色。

    1. 从“组”下拉列表中选择相应的用户组。

    1. 选择“添加用户”。 

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Workspot Control 登录 URL，可以从那里启动登录流。  

* 直接转到 Workspot Control 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 Workspot Control。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Workspot Control 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Workspot Control。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Workspot Control 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。