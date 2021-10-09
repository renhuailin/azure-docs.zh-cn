---
title: 教程：Azure Active Directory 与 AppNeta Performance Manager 的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 AppNeta Performance Manager 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: ceb7c2fcec92e89c65e03fa11db142af514090af
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619849"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-manager"></a>教程：Azure Active Directory 与 AppNeta Performance Manager 的单一登录 (SSO) 集成

本教程介绍如何将 AppNeta Performance Manager 与 Azure Active Directory (Azure AD) 集成。 将 AppNeta Performance Manager 与 Azure AD 集成后，可以：

- 在 Azure AD 中控制有权访问 AppNeta Performance Manager 的人员。
- 允许用户使用其 Azure AD 帐户自动登录到 AppNeta Performance Manager。
- 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
- 已启用 AppNeta Performance Manager 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

- AppNeta Performance Manager 支持 SP 发起的 SSO
- AppNeta Performance Manager 支持即时用户预配

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-appneta-performance-manager-from-the-gallery"></a>从库添加 AppNeta Performance Manager

要配置 AppNeta Performance Manager 与 Azure AD 的集成，需要从库中将 AppNeta Performance Manager 添加到托管 SaaS 应用程序列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 AppNeta Performance Manager 。
1. 从结果面板中选择 AppNeta Performance Manager，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-appneta-performance-manager"></a>配置并测试 AppNeta Performance Manager 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 AppNeta Performance Manager 的 Azure AD SSO。 要正常使用 SSO，需要在 Azure AD 用户与 AppNeta Performance Manager 中的相关用户之间建立链接关系。

要配置并测试 AppNeta Performance Manager 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
   1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
   1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 AppNeta Performance Manager SSO](#configure-appneta-performance-manager-sso)** - 在应用程序端配置单一登录设置。
   1. **[创建 AppNeta Performance Manager 测试用户](#create-appneta-performance-manager-test-user)** - 在 AppNeta Performance Manager 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 AppNeta Performance Manager 应用程序集成页面上，找到“管理”部分，并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](./media/appneta-tutorial/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

   a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.pm.appneta.com` 

   b. 在“回复 URL (断言使用者服务 URL)”字段中，输入：`https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

   > [!NOTE]
   > 上面的“登录 URL”值是一个示例。 使用实际登录 URL 更新此值。 请联系 [AppNeta Performance Manager 客户支持团队](mailto:support@appneta.com)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. AppNeta Performance Manager 应用程序需要特定格式的 SAML 断言，这要求你向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表：

   ![屏幕截图显示了 SAML 令牌的默认属性。](./media/appneta-tutorial/edit-attribute.png)

1. 除上述属性以外，AppNeta Performance Manager 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

   | 名称      | 源属性       |
   | --------- | ---------------------- |
   | firstName | user.givenname         |
   | lastName  | user.surname           |
   | 电子邮件     | user.userprincipalname |
   | name      | user.userprincipalname |
   | groups    | user.assignedroles     |
   | phone     | user.telephonenumber   |
   | title     | user.jobtitle          |
   |           |                        |

1. 若要正确传递“组”SAML 断言，你需要配置应用角色并设置值，以匹配 AppNeta Performance Manager 中设置的角色映射。 在“Azure Active Directory” > “应用注册” >  “所有应用程序”  下，选择“Appneta Performance Manager”。
1. 在左侧窗格中单击“应用角色”。
1. 单击“创建应用角色”。
1. 在“创建应用角色”窗格中，完成以下步骤：
   1. 在“显示名称”中，输入角色的名称。
   1. 在“允许的成员类型”中，选择“用户/组”。
   1. 在“值”中，输入 AppNeta Performance Manager 角色映射中设置的安全组的值。
   1. 在“说明”中，为角色输入说明。
   1. 单击“应用” 。

1. 创建角色后，需要将角色映射到用户和组。 转到“Azure Active Directory” > “企业应用程序” > “Appneta Performance Manger” > “用户和组”。
1. 选择用户或组，然后为该用户或组分配相关的应用角色。
1. 映射应用角色后，转到“Azure Active Directory” > “企业应用程序” > “Appneta Performance Manger” > “单一登录”。
1. 在“使用 SAML 设置单一登录”窗格的“SAML 签名证书”部分中，找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存到计算机上   。

   ![证书下载链接](common/metadataxml.png)

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

在本部分，我们通过授予 B.Simon 访问 AppNeta Performance Manager 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择 AppNeta Performance Manager。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果已按照上述说明设置角色，可从“选择角色”下拉列表中选择角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

   > [!NOTE]
   > 在实践中，你将向应用程序添加组而不是单个用户。

## <a name="configure-appneta-performance-manager-sso"></a>配置 AppNeta Performance Manager SSO

若要在 AppNeta Performance Manager 端配置单一登录，需要将下载的“联合元数据 XML”发送给 [AppNeta Performance Manager 支持团队](mailto:support@appneta.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-appneta-performance-manager-test-user"></a>创建 AppNeta Performance Manager 测试用户

在本部分，你将在 AppNeta Performance Manager 中创建一个名为 B.Simon 的用户。 AppNeta Performance Manager 支持默认已启用的即时用户预配。 此部分不存在任何操作项。 如果 AppNeta Performance Manager 中尚不存在用户，身份验证后会创建一个新用户。

> [!Note]
> 如果需要手动创建用户，请联系 [AppNeta Performance Manager 支持团队](mailto:support@appneta.com)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

- 在 Azure 门户中，选择“测试此应用程序”。 这会重定向到 AppNeta Performance Manager 登录 URL，可从此处启动登录流。

- 直接转到 AppNeta Performance Manager 登录 URL，并从那里启动登录流。

- 你可使用 Microsoft 的“我的应用”。 单击“我的应用”门户中的 AppNeta Performance Manager 磁贴时，会重定向到 AppNeta Performance Manager 登录 URL。 有关“我的应用”门户的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 AppNeta Performance Manager 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。
