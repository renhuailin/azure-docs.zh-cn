---
title: 教程：Azure Active Directory 与 Yodeck 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Yodeck 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2021
ms.author: jeedes
ms.openlocfilehash: fecf2152cfbc398539a20b41bd3a10140fe77d24
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124789268"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>教程：Azure Active Directory 与 Yodeck 集成

本教程介绍如何将 Yodeck 与 Azure Active Directory (Azure AD) 集成。 将 Yodeck 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Yodeck。
* 让用户使用其 Azure AD 帐户自动登录到 Yodeck。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Yodeck 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录的 Yodeck 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Yodeck 支持 SP 和 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-yodeck-from-the-gallery"></a>从库中添加 Yodeck

若要配置 Yodeck 与 Azure AD 的集成，需要从库中将 Yodeck 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Yodeck”。
1. 在结果面板中选择“Yodeck”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-yodeck"></a>配置并测试 Yodeck 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Yodeck 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Yodeck 中的相关用户之间建立关联。

若要配置并测试 Yodeck 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Yodeck SSO](#configure-yodeck-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Yodeck 测试用户](#create-yodeck-test-user) - 在 Yodeck 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Yodeck”应用程序集成页上，找到“管理”部分，选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤   ：

    在“标识符”文本框中键入 URL：`https://app.yodeck.com/api/v1/account/metadata/`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://app.yodeck.com/login`

6. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

本部分中，你将通过授予 B.Simon 访问 Yodeck 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Yodeck”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-yodeck-sso"></a>配置 Yodeck SSO

1. 若要在 Yodeck 中自动完成配置，需要单击“安装扩展”安装“我的应用安全登录浏览器扩展”  。

    ![显示“安装扩展”按钮的屏幕截图。](./media/target-process-tutorial/install_extension.png)

1. 将该扩展添加到浏览器后，单击“设置 Yodeck”定向到 Yodeck 应用程序。 在此处提供管理员凭据以登录到 Yodeck。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-5。

    ![设置配置](common/setup-sso.png)

    **如果要手动配置应用程序，请执行以下步骤：**

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Yodeck 公司站点。

1. 单击页面右上角的“用户设置”选项表单，然后选择“帐户设置”。

    ![屏幕截图显示为用户选择了“帐户设置”。](./media/yodeck-tutorial/account.png)

1. 选择“SAML”并执行以下步骤：

    ![屏幕截图显示了“SAML”选项卡，可在其中执行这些步骤。](./media/yodeck-tutorial/configure.png)

    a. 选择“从 URL 导入”。

    b. 在“URL”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”值，然后单击“导入”。
    
    c. 导入“应用联合元数据 URL”后，其余字段将自动填充。

    d. 单击“保存”  。

### <a name="create-yodeck-test-user"></a>创建 Yodeck 测试用户

为使 Azure AD 用户能够登录 Yodeck，必须将其预配置到 Yodeck 中。 就 Yodeck 来说，预配任务需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Yodeck 公司站点。

2. 单击页面右上角的“用户设置”选项表单，然后选择“用户”。

    ![屏幕截图显示为用户选择了“用户”。](./media/yodeck-tutorial/user.png)

3. 单击“+用户”以打开“用户详细信息”选项卡。

    ![屏幕截图显示了“用户”按钮。](./media/yodeck-tutorial/user-details.png)

4. 在“用户详细信息”对话框页上，执行以下步骤：

    ![屏幕截图显示了“用户详细信息”选项卡，可在其中执行这些步骤。](./media/yodeck-tutorial/user-page.png)

    a. 在“名字”文本框中，键入用户的名字（如“Britta”）。

    b. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）。

    c. 在“电子邮件”文本框中，键入用户的电子邮件地址（例如 brittasimon@contoso.com）。

    d. 根据组织要求选择适当的“帐户权限”选项。
    
    e. 单击“ **保存**”。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Yodeck 登录 URL，你可以从那里启动登录流。  

* 直接转到 Yodeck 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 Yodeck。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“Yodeck”磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Yodeck。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Yodeck 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。