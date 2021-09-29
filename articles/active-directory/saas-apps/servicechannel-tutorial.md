---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 ServiceChannel 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 ServiceChannel 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 21e296764af1e30b0fcf4a7f698a47493148de97
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124801327"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicechannel"></a>教程：Azure Active Directory 单一登录 (SSO) 与 ServiceChannel 集成

本教程介绍了如何将 ServiceChannel 与 Azure Active Directory (Azure AD) 进行集成。 将 ServiceChannel 与 Azure AD 集成后，可以：

- 在 Azure AD 中控制谁有权访问 ServiceChannel。
- 让用户使用其 Azure AD 帐户自动登录到 ServiceChannel。
- 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
- 一个启用了单一登录 (SSO) 的 ServiceChannel 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

- ServiceChannel 支持启用了 IDP 的 SSO
- ServiceChannel 支持恰时用户预配

## <a name="adding-servicechannel-from-the-gallery"></a>从库中添加 ServiceChannel

要配置 ServiceChannel 与 Azure AD 的集成，需要从库中将 ServiceChannel 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“ServiceChannel”。
1. 从结果面板中选择“ServiceChannel”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-servicechannel"></a>配置并测试 ServiceChannel 的 Azure AD SSO

使用名为 B. Simon 的测试用户配置和测试 ServiceChannel 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 ServiceChannel 相关用户之间建立关联。

若要配置并测试 ServiceChannel 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
   1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
   1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 ServiceChannel SSO](#configure-servicechannel-sso)** - 在应用程序端配置单一登录设置。
   1. **[创建 ServiceChannel 测试用户](#create-servicechannel-test-user)** - 在 ServiceChannel 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 ServiceChannel 应用程序集成页上，找到“管理”部分，并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“使用 SAML 设置单一登录”页上，输入以下字段的值： 

   a. 在“标识符”文本框中键入值：`http://adfs.<domain>.com/adfs/service/trust`

   b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<customer domain>.servicechannel.com/saml/acs`

   > [!NOTE]
   > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 此处我们建议在“标识符”中使用字符串的唯一值。 请联系 [ServiceChannel 客户端支持团队](https://servicechannel.zendesk.com/hc/)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 角色声明已预先配置，因此不需配置它，但仍需按照此[文章](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)的说明在 Azure AD 中创建它。 有关声明的详细指导，可以参考[此处的](https://servicechannel.zendesk.com/hc/articles/217514326-Azure-AD-Configuration-Example) ServiceChannel 指南。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上   。

   ![证书下载链接](common/certificatebase64.png)

1. 在“设置 ServiceChannel”部分，根据要求复制相应 URL。

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

在本部分中，通过授予 B.Simon 访问 ServiceChannel 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“ServiceChannel”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果已如上述所示设置角色，可从“选择角色”下拉列表中选择角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-servicechannel-sso"></a>配置 ServiceChannel SSO

若要在“ServiceChannel”端配置单一登录，需要将下载的“证书 (Base64)”以及从 Azure 门户复制的相应 URL 发送给 [ServiceChannel 支持团队](https://servicechannel.zendesk.com/hc/)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-servicechannel-test-user"></a>创建 ServiceChannel 测试用户

应用程序支持实时用户预配，且进行身份验证后，会在应用程序中自动创建用户。 若要进行完全用户预配，请联系 [ServiceChannel 支持团队](https://servicechannel.zendesk.com/hc/)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

- 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 ServiceChannel

- 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 ServiceChannel 磁贴时，应会自动登录到为其设置了 SSO 的 ServiceChannel。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 ServiceChannel 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。