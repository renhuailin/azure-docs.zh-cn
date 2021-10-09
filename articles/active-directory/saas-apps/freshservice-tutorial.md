---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Freshservice 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Freshservice 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: 6c45c00354d0bc60b5a9656c24dd998e324294b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128581584"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Freshservice 集成

本教程介绍如何将 Freshservice 与 Azure Active Directory (Azure AD) 集成。 将 Freshservice 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Freshservice。
* 让用户使用其 Azure AD 帐户自动登录到 Freshservice。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Freshservice 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Freshservice 支持 SP 发起的 SSO。
* Freshservice 支持[自动用户预配](freshservice-provisioning-tutorial.md)。

## <a name="add-freshservice-from-the-gallery"></a>从库中添加 Freshservice

若要配置 Freshservice 与 Azure AD 的集成，需要从库中将 Freshservice 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Freshservice” 。
1. 从结果面板中选择“Freshservice”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-freshservice"></a>配置并测试 Freshservice 的 Azure AD SSO

使用名为“B.Simon”的测试用户为 Freshservice 配置和测试 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Freshservice 相关用户之间建立关联。

若要配置和测试 Freshservice 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Freshservice SSO](#configure-freshservice-sso)** - 在应用程序端配置单一登录。
    1. **[创建 Freshservice 测试用户](#create-freshservice-test-user)** - 在 Freshservice 中创建 B. Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 Freshservice 应用程序集成页上，找到“管理”部分，然后选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<company-name>.freshservice.com`。

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<company-name>.freshservice.com`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<company-name>.freshservice.com/login/saml`
    
    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Freshservice 客户端支持团队](https://support.freshservice.com/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“Azure 门户”上的“设置 Freshservice”部分中，根据要求复制相应的 URL。

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

在本部分中，通过向 B.Simon 授予对 Freshservice 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Freshservice”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-freshservice-sso"></a>配置 Freshservice SSO

1. 若要在“Freshservice”中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 Freshservice”会将你定向到 Freshservice 应用程序。 在这里，提供管理员凭据登录到 Freshservice。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

1. 如果想要手动设置 Freshservice，请以管理员身份登录到 Freshservice 公司网站。

1. 在左侧菜单中，单击“管理”，然后在“常规设置”中选择“支持人员安全性”。

    ![管理员](./media/freshservice-tutorial/configure-1.png "管理员")

1. 在“安全性”中，单击“转到 Freshservice 360 安全性” 。

    ![安全性](./media/freshservice-tutorial/configure-2.png "安全性")

1. 在“安全”部分中，执行以下步骤：

    ![单一登录](./media/freshservice-tutorial/configure-3.png "单一登录")
  
    a. 对于“单一登录”，请选择“启用”。 

    b. 在“登录方法”中，选择“SAML SSO”。

    c. 在“IdP 提供的实体 ID”文本框中，粘贴从 Azure 门户复制的“实体 ID”值 。

    d. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    e. 在“签名选项”中，从下拉菜单中选择“仅限已签名的断言”。

    f. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值 。

    g. 在“安全证书”文本框中，粘贴你之前获得的“证书(Base64)”值。
  
    h. 单击“ **保存**”。


## <a name="create-freshservice-test-user"></a>创建 Freshservice 测试用户

若要使 Azure AD 用户能够登录 FreshService，必须将这些用户预配到 FreshService 中。 对于 FreshService，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 **FreshService** 公司站点。

2. 在左侧菜单中，单击“管理”。

3. 在“用户管理”部分，单击“请求者” 。

    ![申请者](./media/freshservice-tutorial/create-user-1.png "请求者")

4. 单击“新建申请者”。

    ![新建申请者](./media/freshservice-tutorial/create-user-2.png "新建请求者")

5. 在“新建请求者”部分中，输入必填字段，然后单击“保存”。
    ![新建申请者](./media/freshservice-tutorial/create-user-3.png "新建请求者")  

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接
    >  

    > [!NOTE]
    > 可以使用 FreshService 提供的任何其他 FreshService 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。
   
> [!NOTE]
>Freshservice 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./freshservice-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Freshservice 登录 URL，可在其中启动登录流。 

* 直接转到 Freshservice 登录 URL，并在那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Freshservice 磁贴时，应会自动登录到为其设置了 SSO 的 Freshservice。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

 配置 Freshservice 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。