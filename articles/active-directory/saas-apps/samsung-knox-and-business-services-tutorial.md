---
title: Tutorial:Azure Active Directory 单一登录 (SSO) 与 Samsung Knox and Business Services 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Samsung Knox and Business Services 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: a501c12c81da0e2fbacfa6db3a46d590f2501a54
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746407"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Tutorial:Azure Active Directory 单一登录 (SSO) 与 Samsung Knox and Business Services 的集成

本教程介绍如何将 Samsung Knox and Business Services 与 Azure Active Directory (Azure AD) 集成。 将 Samsung Kno 和业务服务与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Samsung Knox and Business Services。
* 让用户使用其 Azure AD 帐户自动登录到 Samsung Knox and Business Services。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个 Samsung Knox 帐户。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Samsung Knox and Business Services 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>从库中添加 Samsung Kno 和业务服务

若要配置 Samsung Knox and Business Services 与 Azure AD 的集成，需要从库中将 Samsung Knox and Business Services 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Samsung Knox and Business Services” 。
1. 从结果面板中选择 Samsung Knox and Business Services，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>配置并测试 Samsung Kno 和业务服务的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Samsung Knox and Business Services 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 [SamsungKnox.com](https://samsungknox.com/) 中相关用户之间建立链接关系。

若要配置并测试 Samsung Knox and Business Services 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Samsung Knox and Business Services SSO](#configure-samsung-knox-and-business-services-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Samsung Knox and Business Services 测试用户](#create-samsung-knox-and-business-services-test-user)** - 在 Samsung Knox and Business Services 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Samsung Knox and Business Services”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    * 在“登录 URL”文本框中，键入 URL：`https://www2.samsungknox.com/en/sso/login/ad`
    * 在“回复 URL (断言使用者服务 URL)”文本框中，键入 URL：`https://central.samsungknox.com/ams/ad/saml/acs`
    
    ![基本 SAML 配置值](https://docs.samsungknox.com/assets/merge/ad-sso/basic-saml-configuration.png)

1. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

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

本部分将通过授予 B.Simon 访问 Samsung Knox and Business Services 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Samsung Kno 和业务服务”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-samsung-knox-and-business-services-sso"></a>配置 Samsung Knox and Business Services SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 [SamsungKnox.com](https://samsungknox.com/)。

1. 单击右上角的“头像”。

    ![Samsung Knox 头像](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. 在左侧边栏中，单击“Active Directory 设置”，并执行以下步骤。

    ![ACTIVE DIRECTORY 设置](https://docs.samsungknox.com/assets/merge/ad-sso/ad-5.png)

    a. 在“标识符(实体 ID)”文本框中，输入已在 Azure 门户中输入的“标识符”值 。

    b. 在“应用联合元数据 URI”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”值 。

    c. 单击“连接到 AD SSO”。

### <a name="create-samsung-knox-and-business-services-test-user"></a>创建 Samsung Knox and Business Services 测试用户

本部分将在 Samsung Knox and Business Services 中创建名为 Britta Simon 的用户。 有关如何邀请副管理员或测试用户加入 Samsung Knox 组织的说明，请参阅 [Knox 配置](https://docs.samsungknox.com/admin/knox-configure/Administrators.htm)或 [Knox 移动注册](https://docs.samsungknox.com/admin/knox-mobile-enrollment/kme-add-an-admin.htm)管理员指南。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 [SamsungKnox.com](https://samsungknox.com/)，可以从那里启动登录流。 

* 直接转到 [SamsungKnox.com](https://samsungknox.com/)，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Samsung Knox 和业务服务”磁贴时，会重定向到 [SamsungKnox.com](https://samsungknox.com/)。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Samsung Knox and Business Services 后，可以强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。