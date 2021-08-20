---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Serraview Space Utilization Software Solutions 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Serraview Space Utilization Software Solutions 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: a93cc04d7172432f223cd0ae5553c374778fd11b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112292611"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-serraview-space-utilization-software-solutions"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Serraview Space Utilization Software Solutions 的集成

本教程介绍如何将 Serraview Space Utilization Software Solutions 与 Azure Active Directory (Azure AD) 集成。 将 Serraview Space Utilization Software Solutions 与 Azure AD 集成之后，你可以：

* 在 Azure AD 中控制谁有权访问 Serraview Space Utilization Software Solutions。
* 让用户能够使用其 Azure AD 帐户自动登录到 Serraview Space Utilization Software Solutions。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Serraview Space Utilization Software Solutions 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Serraview Space Utilization Software Solutions 支持 SP 和 IDP 发起的 SSO。

## <a name="add-serraview-space-utilization-software-solutions-from-the-gallery"></a>从库中添加 Serraview Space Utilization Software Solutions

若要将 Serraview Space Utilization Software Solutions 的集成配置到 Azure AD，你需要从库中将 Serraview Space Utilization Software Solutions 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中键入 Serraview Space Utilization Software Solutions 。
1. 从结果面板中选择“Serraview Space Utilization Software Solutions”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-serraview-space-utilization-software-solutions"></a>配置和测试 Serraview Space Utilization Software Solutions 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Serraview Space Utilization Software Solutions 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Serraview Space Utilization Software Solutions 相关用户之间建立链接关系。

若要配置并测试 Serraview Space Utilization Software Solutions 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Serraview Space Utilization Software Solutions SSO](#configure-serraview-space-utilization-software-solutions-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Serraview Space Utilization Software Solutions 测试用户](#create-serraview-space-utilization-software-solutions-test-user) - 在 Serraview Space Utilization Software Solutions 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Serraview Space Utilization Software Solutions”应用程序集成页上，找到“管理”部分并选择“单一登录” 。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 在“标识符”文本框中，使用以下模式键入值：`urn:Serraview:<SERRAVIEW_IDENTIFIER>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.serraview.com/SAML/AssertionConsumerService.aspx`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.serraview.com/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Serraview Space Utilization Software Solutions 客户端支持团队](mailto:svprodops@serraview.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Serraview Space Utilization Software Solutions”部分中，根据要求复制相应的 URL。

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

在本部分中，你将通过授予 B.Simon 访问 Serraview Space Utilization Software Solutions 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Serraview Space Utilization Software Solutions”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-serraview-space-utilization-software-solutions-sso"></a>配置 Serraview Space Utilization Software Solutions SSO

若要在 Serraview Space Utilization Software Solutions 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [Serraview Space Utilization Software Solutions 支持团队](mailto:svprodops@serraview.com) 。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-serraview-space-utilization-software-solutions-test-user"></a>创建 Serraview Space Utilization Software Solutions 测试用户

在本部分中，你将在 Serraview Space Utilization Software Solutions 中创建名为 B.Simon 的用户。 请与 [Serraview Space Utilization Software Solutions 支持团队](mailto:svprodops@serraview.com)协作，将用户添加到 Serraview Space Utilization Software Solutions 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Serraview Space Utilization Software Solutions 登录 URL，可以在其中启动登录流。  

* 直接转到 Serraview Space Utilization Software Solutions 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Serraview Space Utilization Software Solutions。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“Serraview Space Utilization Software Solutions”磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Serraview Space Utilization Software Solutions。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Serraview Space Utilization Software Solutions 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
