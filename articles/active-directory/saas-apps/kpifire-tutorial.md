---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 kpifire 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 kpifire 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 6656d5e041f5533829ee80c4d2bce8d542a2aaba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592672"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kpifire"></a>教程：Azure Active Directory 单一登录 (SSO) 与 kpifire 集成

在本教程中，了解如何将 kpifire 与 Azure Active Directory (Azure AD) 集成。 将 kpifire 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 kpifire。
* 让用户使用其 Azure AD 帐户自动登录到 kpifire。
* 在一个中心位置（Azure 门户）管理帐户。


## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 kpifire 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* kpifire 支持 IDP 发起的 SSO。
* kpifire 支持[自动用户预配](kpifire-provisioning-tutorial.md)。

## <a name="adding-kpifire-from-the-gallery"></a>从库中添加 kpifire

若要配置 kpifire 与 Azure AD 的集成，需要将库中的 kpifire 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“kpifire” 。
1. 从结果面板中选择“kpifire”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-kpifire"></a>配置并测试 kpifire 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 kpifire 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 kpifire 中的相关用户之间建立链接关系。

若要配置并测试 kpifire 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 kpifire SSO](#configure-kpifire-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 kpifire 测试用户](#create-kpifire-test-user)** - 在 kpifire 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“kpifire”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

     ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.kpifire.com/`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.kpifire.com/api/auth/saml/<UNIQUE_IDENTIFIER>/login`

    c. 单击“设置其他 URL”  。

    d. 在“中继状态”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.kpifire.com/#/metrics` 

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符、回复 URL 和中继状态更新这些值。 请联系 [kpifire 客户端支持团队](mailto:support@kpifire.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 kpifire”部分中，根据要求复制相应 URL。

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

在本部分中，将通过授予 B.Simon 访问 kpifire 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“kpifire”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-kpifire-sso"></a>配置 kpifire SSO

若要在 kpifire 端配置单一登录，需要将下载的“证书(Base64)”以及从 Azure 门户复制的相应 URL 发送给 [kpifire 支持团队](mailto:support@kpifire.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-kpifire-test-user"></a>创建 kpifire 测试用户

本部分将在 kpifire 中创建名为 B.Simon 的用户。 与 [kpifire 支持团队](mailto:support@kpifire.com)协作，在 kpifire 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

kpifire 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./kpifire-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 kpifire

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 kpifire 磁贴时，应会自动登录到为其设置了 SSO 的 kpifire。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 kpifire 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。