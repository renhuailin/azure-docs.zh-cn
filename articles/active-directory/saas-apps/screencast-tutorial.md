---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Screencast-O-Matic 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Screencast-O-Matic 之间配置单一登录。
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
ms.openlocfilehash: a08356ad94b47f2d1088f66de2b5af138a246e9e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124741459"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Screencast-O-Matic 的集成

在本教程中，你将了解如何将 Screencast-Matic 与 Azure Active Directory (Azure AD) 集成。 将 Screencast-O-Matic 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权限访问 Screencast-O-Matic。
* 使用户能够使用其 Azure AD 帐户自动登录到 Screencast-O-Matic。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Screencast-O-Matic 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Screencast-O-Matic 支持 SP 发起的 SSO。
* Screencast-O-Matic 支持恰时用户预配。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-screencast-o-matic-from-the-gallery"></a>从库中添加 Screencast-O-Matic

若要配置 Screencast-O-Matic 与 Azure AD 的集成，需要从库中将 Screencast-O-Matic 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”  部分中，在搜索框中键入“Screencast-O-Matic”  。
1. 从结果面板中选择“Screencast-O-Matic”  ，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-screencast-o-matic"></a>配置并测试 Screencast-O-Matic 的 Azure AD SSO

通过 Screencast-O-Matic 使用名为“B.Simon”  的测试用户来配置和测试 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Screencast-O-Matic 相关用户之间建立关联。

若要配置并测试 Screencast-O-Matic 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Screencast-O-Matic SSO](#configure-screencast-o-matic-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Screencast-O-Matic 测试用户](#create-screencast-o-matic-test-user)** - 在 Screencast-O-Matic 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 身份。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Screencast-O-Matic”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Screencast-O-Matic 客户端支持团队](mailto:support@screencast-o-matic.com)以获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Screencast-O-Matic”部分，根据要求复制相应 URL  。

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

在本部分中，通过授予 B.Simon 访问 Screencast-O-Matic 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Screencast-O-Matic”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-screencast-o-matic-sso"></a>配置 Screencast-O-Matic SSO

1. 若要在 Screencast-O-Matic 中自动执行配置，需要通过单击“安装扩展”  来安装“我的应用安全登录浏览器扩展”  。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 Screencast-O-Matic”，此时会将你定向到 Screencast-O-Matic 应用程序  。 在此处，提供管理员凭据以登录到 Screencast-O-Matic。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-10。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 Screencast-O-Matic，请打开新的 Web 浏览器窗口，以管理员身份登录 Screencast-O-Matic 公司站点，并执行以下步骤：

1. 单击“订阅”。 

    ![显示“订阅”的屏幕截图。](./media/screencast-tutorial/subscribe.png)

1. 在“访问页”部分，单击“设置” 。

    ![显示选择了“设置”按钮的“访问页”部分的屏幕截图。](./media/screencast-tutorial/setup.png)

1. 在“设置访问页”  上，执行以下步骤。

1. 在“访问 URL”部分的指定文本框中，键入实例名。

    ![显示“访问 URL”部分的屏幕截图，其中突出显示了实例名称文本框。](./media/screencast-tutorial/access-page.png)

1. 在“SAML 用户限制(可选)”部分选择“需要域用户”。  

1. 在“上传 IDP 元数据 XML 文件”下单击“选择文件”，以便上传已从 Azure 门户下载的元数据。  

1. 单击“确定”。 

    ![显示“访问”的屏幕截图。](./media/screencast-tutorial/metadata.png)

### <a name="create-screencast-o-matic-test-user"></a>创建 Screencast-O-Matic 测试用户

在本部分，我们会在 Screencast-O-Matic 中创建一个名为 Britta Simon 的用户。 Screencast-O-Matic 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 Screencast-O-Matic 中尚不存在用户，身份验证后会创建一个新用户。 如果需要手动创建用户，请联系 [Screencast-O-Matic 客户端支持团队](mailto:support@screencast-o-matic.com)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Screencast-O-Matic 登录 URL，你可以在那里启动登录流。 

* 直接转到 Screencast-O-Matic 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Screencast-O-Matic”磁贴时，会重定向到 Screencast-O-Matic 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Screencast-O-Matic 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。