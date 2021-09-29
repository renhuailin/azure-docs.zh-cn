---
title: 教程：Azure Active Directory 与 TigerConnect Secure Messenger 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 TigerConnect Secure Messenger 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: jeedes
ms.openlocfilehash: 7d3caa31ec5f4f1a02f6364b2a42cdb406ec7cb5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124789496"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>教程：Azure Active Directory 与 TigerConnect Secure Messenger 的集成

本教程介绍如何将 TigerConnect Secure Messenger 与 Azure Active Directory (Azure AD) 集成。 将 TigerConnect Secure Messenger 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 TigerConnect Secure Messenger。
* 让用户使用其 Azure AD 帐户自动登录到 TigerConnect Secure Messenger。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 TigerConnect Secure Messenger 的集成，需要以下各项：

* 一个 Azure AD 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录的 TigerConnect Secure Messenger 订阅。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中配置和测试 Azure AD 单一登录，并将 TigerConnect Secure Messenger 与 Azure AD 集成。

* TigerConnect Secure Messenger 支持 SP 发起的 SSO。

## <a name="add-tigerconnect-secure-messenger-from-the-gallery"></a>从库中添加 TigerConnect Secure Messenger

若要配置 TigerConnect Secure Messenger 与 Azure AD 的集成，需要将库中的 TigerConnect Secure Messenger 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“TigerConnect Secure Messenger” 。
1. 从结果面板中选择“TigerConnect Secure Messenger”，然后添加应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-tigerconnect-secure-messenger"></a>配置并测试 TigerConnect Secure Messenger 的 Azure AD SSO

在本部分中，将基于名为 Britta Simon 的测试用户配置和测试 TigerConnect Secure Messenger 的 Azure AD 单一登录。 若要运行单一登录，必须在 Azure AD 用户与 TigerConnect Secure Messenger 相关用户之间建立链接。

若要配置并测试 TigerConnect Secure Messenger 的 Azure AD 单一登录，需要执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以便使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 Britta Simon 能够使用 Azure AD 单一登录。
1. [配置 TigerConnect Secure Messenger SSO](#configure-tigerconnect-secure-messenger-sso)，在应用程序端配置单一登录设置。
    1. [创建 TigerConnect Secure Messenger 测试用户](#create-a-tigerconnect-secure-messenger-test-user)，以便在 TigerConnect Secure Messenger 中创建名为 Britta Simon 的用户，该用户与名为 Britta Simon 的 Azure AD 用户相关联。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 TigerConnect Secure Messenger 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“TigerConnect Secure Messenger”应用程序集成页上，找到“管理”部分并选择“单一登录” 。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    1. 在“登录 URL”框中，键入 URL：

       `https://home.tigertext.com`

    1. 在“标识符(实体 ID)”框中，使用以下模式键入 URL：

       `https://saml-lb.tigertext.me/v1/organization/<INSTANCE_ID>`

    > [!NOTE]
    > “标识符(实体 ID)”值不是真实值。 请将此值更新为实际标识符。 若要获取该值，请联系 [TigerConnect Secure Messenger 支持团队](mailto:prosupport@tigertext.com)。 还可以参考 Azure 门户中的“基本 SAML 配置”  窗格中显示的模式。

1. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，选择“下载”以从给定的选项下载“联合元数据 XML”并将其保存在计算机中     。

    ![联合元数据 XML 下载选项](common/metadataxml.png)

1. 在“设置 TigerConnect Secure Messenger”部分，根据要求复制相应的 URL。

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

在本部分，通过向 B.Simon 授予访问 TigerConnect Secure Messenger 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“TigerConnect Secure Messenger”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-tigerconnect-secure-messenger-sso"></a>配置 TigerConnect Secure Messenger SSO

若要在 TigerConnect Secure Messenger 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [TigerConnect Secure Messenger 支持团队](mailto:prosupport@tigertext.com)。 TigerConnect Secure Messenger 团队将确保在两端正确设置 SAML SSO 连接。

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>创建 TigerConnect Secure Messenger 测试用户

在本部分中，你将在 TigerConnect Secure Messenger 中创建名为 Britta Simon 的用户。 请与 [TigerConnect Secure Messenger 支持团队](mailto:prosupport@tigertext.com)协作，将 Britta Simon 作为用户添加到 TigerConnect Secure Messenger 中。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 TigerConnect Secure Messenger 登录 URL，可以在其中启动登录流。 

* 直接转到 TigerConnect Secure Messenger 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“TigerConnect Secure Messenger”磁贴时，将会重定向到 TigerConnect Secure Messenger 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 TigerConnect Secure Messenger 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。