---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 8x8 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 8x8 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 3de204c960a6369589ee0c52bb6f5770a2cee407
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179945"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>教程：Azure Active Directory 单一登录 (SSO) 与 8x8 的集成

本教程介绍如何将 8x8 与 Azure Active Directory (Azure AD) 集成。 将 8x8 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 8x8。
* 让用户使用其 Azure AD 帐户自动登录到 8x8。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个 8x8 订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* 8x8 支持 **SP 和 IDP** 发起的 SSO
* 8x8 支持 [**自动** 用户预配和取消预配](8x8-provisioning-tutorial.md)（推荐）。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-8x8-from-the-gallery"></a>从库中添加 8x8

若要配置 8x8 与 Azure AD 的集成，需要从库中将 8x8 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 **8x8**。
1. 在结果面板中选择“8x8”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>配置并测试 8x8 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 8x8 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 8x8 中的相关用户之间建立链接关系。

若要配置并测试 8x8 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 8x8 SSO](#configure-8x8-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 8x8 测试用户](#create-8x8-test-user)** - 在 8x8 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“8x8”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符”文本框中键入 URL：`https://sso.8x8.com/saml2`

    b. 在“回复 URL”文本框中键入 URL：`https://sso.8x8.com/saml2`

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上   。 稍后在本教程的“配置 8x8 SSO”部分将使用该证书。

    ![证书下载链接](common/certificatebase64.png)

1. 复制“设置 8x8”部分中的 URL，稍后将在本教程中使用这些 URL 值。

    ![复制配置 URL](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

在本部分，你将通过授予 B.Simon 访问 8x8 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中选择“8x8”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。 

## <a name="configure-8x8-sso"></a>配置 8x8 SSO

本教程的下一部分取决于所用的 8x8 订阅类型。

* 对于使用 Configuration Manager 进行管理的 8x8 Edition 和 X Series 客户，请参阅[配置 8x8 管理控制台](#configure-8x8-admin-console)。
* 使用 Account Manager 进行管理的 Virtual Office 客户请参阅[配置 8x8 Account Manager](#configure-8x8-account-manager)。

### <a name="configure-8x8-admin-console"></a>配置 8x8 管理控制台

1. 若要在 8x8 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录浏览器扩展” 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 8x8”会将你定向到 8x8 应用程序。 在此处，提供管理员凭据以登录到 8x8。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

1. 如果要手动设置 8x8，请以管理员身份登录到 8x8 [管理控制台](https://admin.8x8.com/)。

1. 在主页中，单击“标识管理”。

    ![突出显示“标识管理”磁贴的屏幕截图。](./media/8x8virtualoffice-tutorial/configure1.png)

1. 选中“单一登录(SSO)”，然后选择“Microsoft Azure AD”。 

    ![突出显示“单一登录(SSO)”和“Microsoft Azure AD”选项的屏幕截图。](./media/8x8virtualoffice-tutorial/configure2.png)

1. 将 Azure AD 中的“设置 SAML 单一登录”页上的三个 URL 和签名证书复制到 8x8 管理控制台中的“Microsoft Azure AD SAML 设置”部分 。

    ![8x8 管理控制台](./media/8x8virtualoffice-tutorial/configure3.png)

    a. 将“登录 URL”复制到“IDP 登录 URL”。 

    b. 将“Azure AD 标识符”复制到“IDP 颁发者 URL/URN”。 

    c. 将“注销 URL”复制到“IDP 注销 URL”。 

    d. 下载“证书(Base64)”并上传到“证书”。 

    e. 单击“ **保存**”。

### <a name="configure-8x8-account-manager"></a>配置 8x8 Account Manager

1. 以管理员身份登录到 8x8 Virtual Office 租户。

1. 在“应用程序”面板上选择“Virtual Office 帐户管理器”。

    ![突出显示“Virtual Office 帐户管理器”磁贴的屏幕截图。](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. 选择要管理的“企业”帐户，并单击“登录”按钮。

    ![突出显示“业务”选项和“登录”按钮的屏幕截图。](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. 在菜单列表中单击“帐户”选项卡。

    ![突出显示菜单列表中“帐户”选项卡的屏幕截图。](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. 在帐户列表中单击“单一登录”。

    ![突出显示“单一登录”选项的屏幕截图。](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. 在“身份验证”方法下选择“单一登录”，然后单击“SAML”。

    ![突出显示“单一登录”下的 SAML 的屏幕截图。](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. 在“SAML 单一登录”部分中，执行以下步骤：

    ![在应用端上配置](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    b. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值 。

    c. 在“颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 。

    d. 单击“浏览”按钮，上传已从 Azure 门户下载的证书。

    e. 单击“保存”按钮  。

### <a name="create-8x8-test-user"></a>创建 8x8 测试用户

在本部分，你将在 8x8 中创建名为 Britta Simon 的用户。 在 [8x8 支持团队](https://www.8x8.com/about-us/contact-us)的配合下，将用户添加到 8x8 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 8x8 登录 URL，可以在其中启动登录流。  

* 直接转到 8x8 登录 URL，并在其中启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 8x8 

还可以使用 Microsoft 访问面板在任何模式下测试此应用程序。 在点击访问面板中的 8x8 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页面来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 8x8。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md)（访问面板简介）。


## <a name="next-steps"></a>后续步骤

配置 8x8 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。