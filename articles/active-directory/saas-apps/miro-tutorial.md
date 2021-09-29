---
title: 教程：Azure Active Directory 与 Miro 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Miro 之间配置单一登录。
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
ms.openlocfilehash: 6e2b3c611c5bc06e29fb739f01dc69605509a63a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124795177"
---
# <a name="tutorial-integrate-miro-with-azure-active-directory"></a>教程：将 Miro 与 Azure Active Directory 集成

本教程介绍如何将 Miro 与 Azure Active Directory (Azure AD) 集成。 可以在 help.miro.com 上找到本教程的另一个版本。 将 Miro 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Miro。
* 让用户使用其 Azure AD 帐户自动登录到 Miro。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Miro 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 
* Miro 支持 **SP 和 IDP** 发起的 SSO，并支持 **实时** 用户预配。
* Miro 支持[自动用户预配和取消预配](miro-provisioning-tutorial.md)（推荐）。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-miro-from-the-gallery"></a>从库中添加 Miro

要配置 Miro 与 Azure AD 的集成，需要从库中将 Miro 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Miro”。
1. 从结果面板中选择“Miro”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-miro"></a>配置并测试 Miro 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Miro 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Miro 相关用户之间建立链接关系。

若要配置并测试 Miro 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Miro SSO](#configure-miro-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Miro 测试用户](#create-miro-test-user)** - 以在 Miro 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Miro”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤   ：

    在“标识符”文本框中键入 URL：`https://miro.com/`

5. 如果要在 SP 发起的模式下配置应用程序，然后在“登录 URL”文本框中键入 URL，请执行以下操作：`https://miro.com/sso/login/` 

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。 你将需要在 Miro 端配置 SSO。

   ![证书下载链接](common/certificatebase64.png "证书下载链接")

1. 在“设置 Miro”部分，复制登录 URL。 你将需要在 Miro 端配置 SSO。

   ![复制登录 URL](./media/miro-tutorial/login.png "复制登录 URL")

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

在本部分中，通过授予 B.Simon 访问 Miro 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Miro”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

* 或者转到应用程序的“属性”，并关闭“需要用户分配”
![禁用分配要求](./media/miro-tutorial/properties.png "禁用分配要求") 

## <a name="configure-miro-sso"></a>配置 Miro SSO

若要在 Miro 端配置单一登录，请使用之前下载的证书和之前复制的登录 URL。 在“Miro 帐户”设置中，转到“安全性”部分，然后打开“启用 SSO/SAML” 。 

1. 将登录 URL 粘贴到“SAML 登录 URL”字段中。
1. 使用文本编辑器打开证书文件并复制证书序列。 将序列粘贴到“密钥 x509 证书”字段中。
![Miro 设置](./media/miro-tutorial/security.png "Miro 设置")

1. 在“域”字段中键入你的域地址，单击“添加”并按照验证程序进行操作 。 若有其他域地址，请重复此操作。 Miro SSO 功能可供列表中域的最终用户使用。 
![域](./media/miro-tutorial/add-domain.png "域")

1. 确定是否使用“实时预配”（在用户注册 Miro 期间将其拉入订阅），然后单击“保存”以完成 Miro 端的 SSO 配置。
![实时预配](./media/miro-tutorial/save-configuration.png "实时预配") 

### <a name="create-miro-test-user"></a>创建 Miro 测试用户

本部分将在 Miro 中创建一个名为 B.Simon 的用户。 Miro 支持实时用户预配并且默认启用。 此部分不存在任何操作项。 如果 Miro 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用测试用户 B.Simon 通过以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 直接转到 Miro 登录 URL，从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”，然后选择以 B.Simon 身份登录。 你应会自动登录到为其设置了 SSO 的 Miro 订阅。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Miro 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Miro。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。