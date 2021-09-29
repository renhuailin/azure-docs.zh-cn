---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 PurelyHR 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 PurelyHR 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/26/2021
ms.author: jeedes
ms.openlocfilehash: 9effe05f134bf483b93a8c73653329dca8be630b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124816564"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>教程：Azure Active Directory 单一登录 (SSO) 与 PurelyHR 集成

在本教程中，了解如何将 PurelyHR 与 Azure Active Directory (Azure AD) 集成。 将 PurelyHR 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 PurelyHR。
* 让用户使用其 Azure AD 帐户自动登录到 PurelyHR。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 PurelyHR 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* PurelyHR 支持 SP 和 IDP 发起的 SSO。
* PurelyHR 支持实时用户预配。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-purelyhr-from-the-gallery"></a>从库中添加 PurelyHR

要配置 PurelyHR 与 Azure AD 的集成，需要从库中将 PurelyHR 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“PurelyHR”。
1. 从结果面板中选择“PurelyHR”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-purelyhr"></a>配置并测试 PurelyHR 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 PurelyHR 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 PurelyHR 相关用户之间建立链接关系。

若要配置并测试 PurelyHR 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 PurelyHR SSO](#configure-purelyhr-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 PurelyHR 测试用户](#create-purelyhr-test-user)** - 在 PurelyHR 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“PurelyHR”应用程序集成页上，找到“管理”部分，并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤   ：

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://<COMPANY_ID>.purelyhr.com/sso-consume`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<COMPANY_ID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“回复 URL”和“注销 URL”更新这些值。 请联系 [PurelyHR 客户端支持团队](https://support.purelyhr.com/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 PurelyHR”部分中，根据要求复制相应 URL。

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

在本部分中，将通过授予 B.Simon 访问 PurelyHR 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“PurelyHR”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-purelyhr-sso"></a>配置 PurelyHR SSO

1. 若要在 PurelyHR 中自动执行配置，需要通过单击“安装扩展”来安装 **我的应用安全登录浏览器扩展**。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 PurelyHR”，此时会将你定向到 PurelyHR 应用程序。 在此处，提供管理员凭据以登录到 PurelyHR。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-5。

    ![设置配置](common/setup-sso.png)

1. 如果想要手动设置 PurelyHR，请打开新的 Web 浏览器窗口并以管理员身份登录 PurelyHR 公司站点，然后执行以下步骤：

1. 通过工具栏中的选项打开“仪表板”，并单击“SSO 设置”。

1. 按如下所述在框中粘贴值-

    ![配置单一登录](./media/purelyhr-tutorial/dashboard.png)    

    a. 在记事本中打开从 Azure 门户下载的 **Certificate(Bas64)** 并复制证书值。 将复制的值粘贴到“X.509 证书”框中。

    b. 在“Idp 颁发者 URL”框中，粘贴从 Azure 门户复制的“Azure AD 标识符”。

    c. 在“IDP 终结点 URL”框中，粘贴从 Azure 门户复制的“登录 URL”。 

    d. 选中“自动创建用户”复选框，在 PurelyHR 中启用自动用户预配。

    e. 单击“保存更改”保存这些设置。

### <a name="create-purelyhr-test-user"></a>创建 PurelyHR 测试用户

此步骤通常不是必需的，因为应用程序支持实时用户预配。 如果未启用自动用户预配，则可手动创建用户，如下所述。

以管理员身份登录到 Velpic SAML 公司站点，并执行以下步骤：

1. 单击“管理”选项卡，转到“用户”部分，并单击“新建”按钮添加用户。

    ![添加用户](./media/purelyhr-tutorial/new-user.png)

2. 在“新建用户”对话框页中，执行以下步骤。 

    ![user](./media/purelyhr-tutorial/create-user.png)

    a. 在“名字”文本框中键入名字 B。 

    b. 在“姓氏”文本框键入姓氏 Simon。 

    c. 在“用户名”  文本框中，键入 B.Simon 的用户名。

    d. 在“电子邮件”文本框中，键入 B.Simon@contoso.com 帐户的电子邮件地址  。

    e. 其余信息为可选，可根据需要填写。

    f. 单击“保存”  。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 PurelyHR 登录 URL，你可以从那里启动登录流。  

* 直接转到 PurelyHR 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 PurelyHR。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“PurelyHR”磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则会自动登录到为其设置了 SSO 的 PurelyHR。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 PurelyHR 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。