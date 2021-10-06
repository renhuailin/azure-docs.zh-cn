---
title: Tutorial:Azure Active Directory 单一登录 (SSO) 与 Parkalot - Car park management 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Parkalot - Car park management 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 630b502d4e743c74b421f01664282c825d507f84
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778621"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-parkalot---car-park-management"></a>Tutorial:Azure Active Directory 单一登录 (SSO) 与 Parkalot - Car park management 集成

本教程介绍如何将 Parkalot - Car park management 与 Azure Active Directory (Azure AD) 集成。 将 Parkalot - Car park management 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Parkalot - Car park management。
* 允许用户使用其 Azure AD 帐户自动登录到 Parkalot - Car park management。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Parkalot - Car park management 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Parkalot - Car park management 支持 SP 发起的 SSO

* Parkalot - Car park management 支持实时用户预配

## <a name="adding-parkalot---car-park-management-from-the-gallery"></a>从库中添加 Parkalot - Car park management

若要配置 Parkalot - Car park management 与 Azure AD 的集成，需要从库中将 Parkalot - Car park management 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Parkalot - Car park management” 。
1. 从结果面板中选择“Parkalot - Car park management”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-parkalot---car-park-management"></a>配置和测试 Parkalot - Car park management 的 Azure AD SSO

使用名为“B.Simon”的测试用户配置并测试 Parkalot - Car park management 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Parkalot - Car park management 相关用户之间建立关联。

若要配置和测试 Parkalot - Car park management 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Parkalot - Car park management SSO](#configure-parkalot-car-park-management-sso)** - 以在应用程序端配置单一登录设置。
    1. **[创建 Parkalot - Car park management 测试用户](#create-parkalot-car-park-management-test-user)** - 以在 Parkalot - Car park management 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Parkalot - Car park management”应用程序集成页上，找到“管理”部分，并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式之一键入 URL：

    | 标识符(实体 ID) |
    | -------------- |
    | `https://parkalot.io` |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    |

    b. 在“回复 URL”文本框中，使用以下模式之一键入 URL：

    | 回复 URL |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    | `https://parkalot-saml.firebaseapp.com/__/auth/handler` |
    | `https://parkalot-saml.web.app/__/auth/handler` |
    | `https://<CustomerName>.parkalot.io/__/auth/handler` |
    |

    c. 在“登录 URL”文本框中，使用以下模式之一键入 URL：

    | 登录 URL |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io/#/login` |
    | `https://parkalot-saml.firebaseapp.com/#/login` |
    | `https://parkalot-saml.web.app/#/login` |
    |

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Parkalot - Car park management 客户端支持团队](mailto:contact-us@parkalot.io)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Parkalot - Car park management”部分中，根据需要复制相应的 URL。

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

在本部分中，将通过授予 B.Simon 访问 Parkalot - Car park management 的权限来允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Parkalot - Car park management”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-parkalot-car-park-management-sso"></a>配置 Parkalot - Car park management SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Parkalot - Car park management 公司站点。

1. 选择“安装 SAML”，并单击“新建”卡片上的“编辑”图标  。

    ![添加新的编辑图标。](./media/parkalot-car-park-management-tutorial/setup-saml.png)

1. 在下一页中执行下述步骤。

    ![配置 Parkalot - Car park management SSO。](./media/parkalot-car-park-management-tutorial/configuration.png)

    a. 在“显示名称”文本框中，为其输入有效名称。

    b. 在“IdP 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 。

    c. 在“SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    d. 在记事本中打开从 Azure 门户下载的“证书(Base64)”，将内容粘贴到“证书”文本框中 。

    e. 单击“保存”  。

### <a name="create-parkalot-car-park-management-test-user"></a>创建 Parkalot - Car park management 测试用户

在本部分中，将在 Parkalot - Car park management 中创建名为 Britta Simon 的用户。 Parkalot - Car park management 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Parkalot - Car park management 中尚不存在用户，系统将在身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Parkalot - Car park management 登录 URL，可以从那里启动登录流。 

* 直接转到 Parkalot - Car park management 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的“Parkalot - Car park management”磁贴时，会重定向到 Parkalot - Car park management 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 Parkalot - Car park management 后，可以强制实施会话控制，从而实时保护组织的敏感数据以防外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。