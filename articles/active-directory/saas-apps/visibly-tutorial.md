---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Visibly 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Visibly 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: f482e1e35b303cb4dfd355e2a8da796de5296a99
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820905"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-visibly"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Visibly 集成

本教程介绍如何将 Visibly 与 Azure Active Directory (Azure AD) 集成。 将 Visibly 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 Visibly。
* 让用户能够使用其 Azure AD 帐户自动登录到 Visibly。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Visibly 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Visibly 支持 SP 发起的 SSO。
* Visibly 支持[自动用户预配](visibly-provisioning-tutorial.md)。

## <a name="add-visibly-from-the-gallery"></a>从库中添加 Visibly

要配置 Visibly 与 Azure AD 的集成，需要从库中将 Visibly 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Visibly” 。
1. 在结果面板中选择“Visibly”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-visibly"></a>为 Visibly 配置并测试 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Visibly 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Visibly 中的相关用户之间建立链接关系。

若要配置并测试 Visibly 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Visibly SSO](#configure-visibly-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Visibly 测试用户](#create-visibly-test-user) - 在 Visibly 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Visibly”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，键入 URL：`https://app.visibly.io/`

    b. 在“回复 URL”文本框中键入 URL：`https://api.visibly.io/api/v1/verifyResponse`

1. Visibly 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![图像](common/default-attributes.png)

1. 除了上述属性，Visibly 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 |  源属性|
    | ----------- | --------- |
    | city | user.city |
    | lastName | user.surname |
    | state | user.state |
    | department | user.department |
    | 电子邮件 | user.mail |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Visibly”部分，根据要求复制相应的 URL。

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

在本部分中，你将通过授予 B.Simon 访问 Visibly 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Visibly”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-visibly-sso"></a>配置 Visibly SSO

1. 使用凭据登录 Visibly。

1. 导航到导航菜单中的“设置”选项。

    ![显示选择了“设置”选项的屏幕截图。](./media/visibly-tutorial/settings.png)

1. 单击“设置”中的“集成”。

    ![显示从“设置”菜单选择了“集成”的屏幕截图。](./media/visibly-tutorial/integrations.png)

1. 在“集成”中，选择 SSO 。

    ![显示从“集成”中选择了“SSO”的屏幕截图。](./media/visibly-tutorial/sso.png)

1. 在下面的页中执行以下步骤。

    ![显示“SSO 集成”页面的屏幕截图，你可以在其中输入所述值。](./media/visibly-tutorial/configuration.png)

    a. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“实体 ID”值 。

    b. 在“SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    c. 在“SSO 名称”文本框中，提供任一有效名称。

    d. 在记事本中打开从 Azure 门户下载的“证书(Base64)”，将内容粘贴到“证书”文本框中，或者通过选择“上传证书”来上传“证书”   。

    e. 单击“保存”  。

### <a name="create-visibly-test-user"></a>创建 Visibly 测试用户

本部分将在 Visibly 中创建一个名为 B.Simon 的用户。 Visibly 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Visibly 中尚不存在用户，身份验证后会创建一个新用户。

Visibly 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./visibly-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Visibly 登录 URL，让你可以从这里启动登录流。 

* 直接转到 Visibly 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Visibly 磁贴时，会重定向到 Visibly 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Visibly 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
