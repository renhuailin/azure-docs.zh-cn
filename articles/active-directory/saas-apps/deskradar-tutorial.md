---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Deskradar 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Deskradar 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 394f4300beb731c1331da25f01c7ca9b7d70cde9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124826506"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-deskradar"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Deskradar 集成

本教程介绍如何将 Deskradar 与 Azure Active Directory (Azure AD) 集成。 将 Deskradar 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Deskradar。
* 让用户使用其 Azure AD 帐户自动登录到 Deskradar。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Deskradar 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Deskradar 支持 SP 和 IDP 发起的 SSO 

## <a name="add-deskradar-from-the-gallery"></a>从库中添加 Deskradar

若要配置 Deskradar 与 Azure AD 的集成，需要从库中将 Deskradar 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Deskradar”   。
1. 从结果面板中选择“Deskradar”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-deskradar"></a>配置并测试 Deskradar 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Deskradar 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Deskradar 相关用户之间建立链接关系。

若要配置并测试 Deskradar 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Deskradar SSO](#configure-deskradar-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Deskradar 测试用户](#create-deskradar-test-user)** - 在 Deskradar 中创建 B. Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Deskradar”应用程序集成页面上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<YOURDOMAIN>.deskradar.cloud`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<YOURDOMAIN>.deskradar.cloud/auth/sso/saml/consume`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<YOURDOMAIN>.deskradar.cloud/auth/sso/saml/login`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 将 **YOURDOMAIN** 替换为 Deskradar 实例域。 请联系 [Deskradar 客户端支持团队](mailto:support@deskradar.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Deskradar 应用程序需要特定格式的 SAML 断言，这要求你将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/edit-attribute.png)

1. 除了上述属性，Deskradar 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ---------------| --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | 电子邮件 | user.userprincipalname |
    | | |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Deskradar”部分中，根据要求复制相应 URL  。

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

在本部分中，通过授予 B.Simon 访问 Deskradar 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Deskradar”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-deskradar-sso"></a>配置 Deskradar SSO

1. 若要在 Deskradar 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“安装 Deskradar”会定向到 Deskradar 应用程序  。 在此处，提供管理员凭据以登录到 Deskradar。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 Deskradar，请打开新的 Web 浏览器窗口，以管理员身份登录 Deskradar 公司站点，并执行以下步骤：

1. 单击侧栏中的图标，打开“团队”面板。 

1. 切换到“身份验证”选项卡。 

1. 在“SAML 2.0”选项卡上执行以下步骤： 

    ![显示“SAML 2.0”选项卡的屏幕截图，其中箭头指向“SAML SSD URL”和“标识符提供者颁发者”字段。](./media/deskradar-tutorial/14-paste-urls.jpg)

    a. 启用 **SAML** 身份验证方法。

    b. 在“SAML SSO URL”文本框中，输入从 Azure 门户复制的“登录 URL”值   。

    c. 在“标识提供者颁发者”文本框中，输入从 Azure 门户复制的“Azure AD 标识符”值   。

1. 使用文本编辑器打开已下载的“证书(Base64)”文件，将其内容复制并粘贴到 Deskradar 中的“公共证书”字段。  

    ![Deskradar 配置](./media/deskradar-tutorial/15-paste-certificate.jpg)

### <a name="create-deskradar-test-user"></a>创建 Deskradar 测试用户

在本部分，你将在 Deskradar 中创建名为 B.Simon 的用户。 与 [Deskradar 客户端支持团队](mailto:support@deskradar.com)合作，以在 Deskradar 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Deskradar 登录 URL，可以从那里启动登录流。  

* 直接转到 Deskradar 登录 URL，从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”，你应该会自动登录到为其设置了 SSO 的 Deskradar。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 当你在“我的应用”中单击 Deskradar 磁贴时，如果这是在 SP 模式下配置的，系统会将你重定向到应用程序登录页来启动登录流；如果这是在 IDP 模式下配置的，系统会让你自动登录到为其设置了 SSO 的 Deskradar。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

在配置 Deskradar 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。