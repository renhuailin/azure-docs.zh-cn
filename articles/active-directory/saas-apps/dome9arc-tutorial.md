---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Check Point CloudGuard 状况管理集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Check Point CloudGuard 状况管理之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/13/2021
ms.author: jeedes
ms.openlocfilehash: 7ef19bebce43858272b33d5ab72298347a256179
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124770218"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-posture-management"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Check Point CloudGuard 状况管理集成

在本教程中，你将学习如何将 Check Point CloudGuard 状况管理与 Azure Active Directory (Azure AD) 集成。 将 Check Point CloudGuard 状况管理与 Azure AD 集成时，可以实现以下操作：

- 在 Azure AD 中控制谁可以访问 Check Point CloudGuard 状况管理。
- 允许用户使用 Azure AD 帐户自动登录 Check Point CloudGuard 状况管理。
- 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Check Point CloudGuard 状况管理单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Check Point CloudGuard 状况管理支持 SP 和 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-check-point-cloudguard-posture-management-from-the-gallery"></a>从库中添加 Check Point CloudGuard 状况管理

要配置 Check Point CloudGuard 状况管理与 Azure AD 的集成，需将 Check Point CloudGuard 状况管理从库中添加到托管 SaaS 应用列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Check Point CloudGuard 状况管理” 。
1. 从结果面板中选择“Check Point CloudGuard 状况管理”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-check-point-cloudguard-posture-management"></a>配置并测试 Check Point CloudGuard 状况管理的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试面向 Check Point CloudGuard 状况管理的 Azure AD SSO。 要使 SSO 正常工作，需要在 Azure AD 用户与 Check Point CloudGuard 状况管理中相关用户之间建立链接关系。

若要配置并测试 Check Point CloudGuard 状况管理的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
   1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
   1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Check Point CloudGuard 状况管理 SSO](#configure-check-point-cloudguard-posture-management-sso)** - 在应用程序端配置单一登录设置。
   1. **[创建 Check Point CloudGuard 状况管理测试用户](#create-check-point-cloudguard-posture-management-test-user)** - 在 Check Point CloudGuard 状况管理中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Check Point CloudGuard 状况管理应用程序集成页上，找到“管理”部分，并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤   ：

   在“回复 URL”文本框中，使用以下模式键入 URL：`https://secure.dome9.com/sso/saml/<YOURCOMPANYNAME>`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

   在“登录 URL”  文本框中，使用以下模式键入 URL：`https://secure.dome9.com/sso/saml/<YOURCOMPANYNAME>`。

   > [!NOTE]
   > 这些不是实际值。 使用实际的回复 URL 和登录 URL 更新这些值。 你将从“配置 Check Point CloudGuard 状况管理 SSO”部分中获得 `<company name>` 值，本教程后面将对此进行说明。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Check Point CloudGuard 状况管理应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

   ![图像](common/edit-attribute.png)

1. 除上述属性外，Check Point CloudGuard 状况管理应用程序还要求在 SAML 响应中传回更多属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

   | 名称     | 源属性   |
   | -------- | ------------------ |
   | memberof | user.assignedroles |

   > [!NOTE]
   > 单击[此处](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)以了解如何在 Azure AD 中创建角色。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

   ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Check Point CloudGuard 状况管理”部分，根据需要复制相应的 URL。

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

在本节中，你将通过授予 B.Simon 对 Check Point CloudGuard 状况管理的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Check Point CloudGuard 状况管理”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果已如上述所示设置角色，可从“选择角色”下拉列表中选择角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-check-point-cloudguard-posture-management-sso"></a>配置 Check Point CloudGuard 状况管理 SSO

1. 若要在 Check Point CloudGuard 状况管理中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展” 。

   ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Check Point CloudGuard 状况管理”会将你定向到 Check Point CloudGuard 状况管理应用程序。 在此处，请提供管理员凭据以登录到 Check Point CloudGuard 状况管理。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

   ![设置配置](common/setup-sso.png)

3. 若要手动设置 Check Point CloudGuard 状况管理，请打开新的 Web 浏览器窗口，以管理员身份登录 Check Point CloudGuard 状况管理公司站点，并执行以下步骤：

4. 单击右上角的“配置文件设置”  ，然后单击“帐户设置”  。

   ![显示已选择“帐户设置”的“个人资料设置”菜单的屏幕截图。](./media/dome9arc-tutorial/account-settings.png)

5. 导航到 **SSO**，然后单击“启用”。 

   ![显示“SSO”选项卡和“启用”已选定的屏幕截图。](./media/dome9arc-tutorial/enable.png)

6. 在“SSO 配置”部分，执行以下步骤：

   ![Check Point CloudGuard 状况管理配置](./media/dome9arc-tutorial/configuration.png)

   a. 在“帐户 ID”文本框中输入公司名称。  此值要在 Azure 门户的“基本 SAML 配置”  部分中提到的“答复”  和“登录”  URL 中使用。

   b. 在“颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

   c. 在“Idp 终结点 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

   d. 在记事本中打开下载的 Base64 编码证书，将其内容复制到剪贴板，并粘贴到“X.509 证书”  文本框中。

   e. 单击“保存”  。

### <a name="create-check-point-cloudguard-posture-management-test-user"></a>创建 Check Point CloudGuard 状况管理测试用户

要使 Azure AD 用户能够登录 Check Point CloudGuard 状况管理，需将他们预配到应用程序中。 Check Point CloudGuard 状况管理支持即时预配，但要使此功能正常工作，必须选择特定“角色”并将其分配给用户。

> [!Note]
> 有关“角色”创建和其他详细信息，请联系[ Check Point CloudGuard 状况管理客户支持团队](mailto:Dome9@checkpoint.com)。

**若要手动预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 Check Point CloudGuard 状况管理公司站点。

2. 单击“用户和角色”  ，然后单击“用户”  。

   ![显示已选择“用户”操作的“用户和角色”的屏幕截图。](./media/dome9arc-tutorial/users.png)

3. 单击“添加用户”。 

   ![显示已选择“添加用户”按钮的“用户和角色”的屏幕截图。](./media/dome9arc-tutorial/add-user.png)

4. 在“创建用户”  部分中，执行以下步骤：

   ![添加员工](./media/dome9arc-tutorial/create-user.png)

   a. 在“电子邮件”文本框中，键入用户的电子邮件，例如 B.Simon@contoso.com。 

   b. 在“名字”文本框中，键入用户的名字，如 B  。

   c. 在“姓氏”文本框中，键入用户的姓氏（如 Simon）  。

   d. 使“SSO 用户”处于“启用”状态。  

   e. 单击“创建”  。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Check Point CloudGuard 状况管理登录 URL，可在其中启动登录流。

* 直接转到 Check Point CloudGuard 状况管理登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Check Point CloudGuard 状况管理

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Check Point CloudGuard 状况管理磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Check Point CloudGuard 状况管理。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Check Point CloudGuard 状况管理后，可以强制实施会话控制，实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。