---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Nuclino 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Nuclino 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/21/2021
ms.author: jeedes
ms.openlocfilehash: 39699417d0d3ba7ab1da26624270c80367ce273c
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601803"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nuclino"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Nuclino 的集成

本教程介绍如何将 Nuclino 与 Azure Active Directory (Azure AD) 集成。 将 Nuclino 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Nuclino。
* 让用户使用其 Azure AD 帐户自动登录到 Nuclino。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Nuclino 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Nuclino 支持 SP 和 IDP 发起的 SSO。
* Nuclino 支持实时用户预配。

## <a name="add-nuclino-from-the-gallery"></a>从库中添加 Nuclino

若要配置 Nuclino 与 Azure AD 的集成，需要从库中将 Nuclino 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Nuclino”   。
1. 从结果面板中选择“Nuclino”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-nuclino"></a>配置并测试 Nuclino 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 Nuclino 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Nuclino 相关用户之间建立链接关系。

若要配置并测试 Nuclino 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Nuclino SSO](#configure-nuclino-sso)  - 在应用程序端配置单一登录设置。
    1. **[创建 Nuclino 测试用户](#create-nuclino-test-user)** - 在 Nuclino 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Nuclino”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > 这些不是实际值。 使用“身份验证”部分中的实际“标识符”和“回复 URL”更新这些值，本教程稍后将提供相关的介绍。 

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Nuclino 客户端支持团队](mailto:contact@nuclino.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. Nuclino 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/edit-attribute.png)

7. 除了上述属性，Nuclino 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 |  源属性|
    | ---------------| --------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Nuclino”部分中，根据需要复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Nuclino 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Nuclino”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-nuclino-sso"></a>配置 Nuclino SSO

1. 若要在 Nuclino 中自动执行配置，需要通过单击“安装扩展”  来安装 **我的应用安全登录浏览器扩展**。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Nuclino”，此时会将你定向到 Nuclino 应用程序  。 在此处提供管理员凭据以登录到 Nuclino。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Nuclino，请打开新的 Web 浏览器窗口，以管理员身份登录 Nuclino 公司站点，并执行以下步骤：

4. 单击“ICON”  。

    ![屏幕截图显示选择了“Azure AD SSO”旁边的“菜单”图标。](./media/nuclino-tutorial/menu.png)

5. 单击“Azure AD SSO”，然后从下拉列表中选择“团队设置”。  

    ![屏幕截图显示选择了“团队设置”的“Azure AD SSO”下拉列表。](./media/nuclino-tutorial/team-settings.png)

6. 从左侧导航窗格中选择“身份验证”。 

    ![屏幕截图显示已选择“身份验证”。](./media/nuclino-tutorial/authentication.png)

7. 在“身份验证”  部分执行以下步骤：

    ![Nuclino 配置](./media/nuclino-tutorial/configuration.png)

    a. 选择“基于 SAML 的单一登录(SSO)”。 

    b. 复制“ACS URL”值（需将其复制并粘贴到 SSO 提供程序），然后将其粘贴到 Azure 门户的“基本 SAML 配置”部分的“回复 URL”文本框中。   

    c. 复制“实体 ID”值（需将其复制并粘贴到 SSO 提供程序），然后将其粘贴到 Azure 门户的“基本 SAML 配置”部分的“标识符”文本框中。   

    d. 在“SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    e. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    f. 在记事本中打开已下载的 **Certificate(Base64)** 文件。 将其内容复制到剪贴板，然后将其粘贴到“公共证书”  文本框中。

    g. 单击“保存更改”。 

### <a name="create-nuclino-test-user"></a>创建 Nuclino 测试用户

本部分将在 Nuclino 中创建一个名为 B.Simon 的用户。 Nuclino 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 Nuclino 中尚不存在用户，身份验证后会创建一个新用户。

> [!Note]
> 如果需要手动创建用户，请联系 [Nuclino 支持团队](mailto:contact@nuclino.com)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Nuclino 登录 URL，可在其中启动登录流。  

* 直接转到 Nuclino 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Nuclino。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Nuclino 磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页面来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Nuclino。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Nuclino 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
