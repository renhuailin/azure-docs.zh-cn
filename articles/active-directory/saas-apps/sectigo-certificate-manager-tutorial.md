---
title: 教程：Azure Active Directory 与 Sectigo Certificate Manager 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Sectigo Certificate Manager 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2021
ms.author: jeedes
ms.openlocfilehash: c25784583b74069f4b6462166da5c9cf1038c6d9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760857"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>教程：Azure Active Directory 与 Sectigo Certificate Manager 的集成

本教程介绍如何将 Sectigo Certificate Manager 与 Azure Active Directory (Azure AD) 相集成。 将 Sectigo Certificate Manager 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Sectigo Certificate Manager。
* 让用户使用其 Azure AD 帐户自动登录到 Sectigo Certificate Manager。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Sectigo Certificate Manager 的集成，需准备好以下各项：

* 一个 Azure AD 订阅。 如果还没有 Azure AD 订阅，可在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* Sectigo Certificate Manager 帐户。

> [!NOTE]
> Sectigo 运行 Sectigo Certificate Manager 的多个实例。 Sectigo Certificate Manager 的主实例是 https:\//cert-manager.com，本教程中使用此 URL。  如果你的帐户在另一个实例上，需要相应调整 URL。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中配置和测试 Azure AD 单一登录，并将 Sectigo Certificate Manager 与 Azure AD 集成。

Sectigo Certificate Manager 支持以下功能：

* SP 发起的单一登录。
* IDP 发起的单一登录。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>在 Azure 门户中添加 Sectigo Certificate Manager

若要配置 Sectigo Certificate Manager 与 Azure AD 的集成，需要将库中的 Sectigo Certificate Manager 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 Sectigo Certificate Manager 。
1. 在结果面板中选择“Sectigo Certificate Manager”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-sectigo-certificate-manager"></a>配置并测试 Sectigo Certificate Manager 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Sectigo Certificate Manager 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Sectigo Certificate Manager 中的相关用户之间建立链接关系。

若要配置并测试 Sectigo Certificate Manager 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Sectigo Certificate Manager SSO](#configure-sectigo-certificate-manager-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Sectigo Certificate Manager 测试用户](#create-sectigo-certificate-manager-test-user) - 在 Sectigo Certificate Manager 中创建 B.Simon 的对应用户，并将其链接到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Sectigo Certificate Manager”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分执行以下步骤：

    1. 在“标识符(实体 ID)”框中，为 Sectigo Certificate Manager 主实例输入 https:\//cert-manager.com/shibboleth 。

    1. 在“回复 URL”框中，为 Sectigo Certificate Manager 主实例输入 https:\//cert-manager.com/Shibboleth.sso/SAML2/POST 。
        
    > [!NOTE]
    > 尽管在一般情况下，对于“SP 启动模式”来说“登录 URL”是必需的，但不需要从 Sectigo Certificate Manager 登录。        

1. （可选）在“基本 SAML 配置”部分中，若要配置“IDP 发起的模式”并使测试能够正常进行，请执行以下步骤  ：

    1. 选择“设置其他 URL”  。

    1. 在“中继状态”框中，输入你的 Sectigo Certificate Manager 客户特定的 URL。 对于 Sectigo Certificate Manager 主实例，请输入 https:\//cert-manager.com/customer/\<customerURI\>/idp。

1. 在“用户属性和声明”部分执行以下步骤：

    1. 删除所有“其他声明”。
    
    1. 选择“添加新声明”并添加以下四个声明：
    
        | 名称 | 命名空间 | 源 | 源属性 | 说明 |
        | --- | --- | --- | --- | --- |
        | eduPersonPrincipalName | empty | Attribute | user.userprincipalname | 必须与管理员的 Sectigo Certificate Manager 中的“IdP Person ID”字段相匹配。 |
        | mail | empty | Attribute | user.mail | 必需 |
        | givenName | empty | Attribute | user.givenname | 可选 |
        | sn | empty | Attribute | user.surname | 可选 |

       ![Sectigo Certificate Manager - 添加四个新声明](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. 在“SAML 签名证书”部分，选择“联合元数据 XML”旁边的“下载”  。 将该 XML 文件保存在计算机上。

    ![联合元数据 XML 下载选项](common/metadataxml.png)

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

在本部分，你将通过授予 B.Simon 访问 Sectigo Certificate Manager 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Sectigo Certificate Manager”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-sectigo-certificate-manager-sso"></a>配置 Sectigo Certificate Manager SSO

若要在 Sectigo Certificate Manager 端配置单一登录，请将下载的联合元数据 XML 文件发送到 [Sectigo Certificate Manager 支持团队](https://sectigo.com/support)。 Sectigo Certificate Manager 支持团队使用你发送的信息来确保双方都正确设置 SAML 单一登录连接。

### <a name="create-sectigo-certificate-manager-test-user"></a>创建 Sectigo Certificate Manager 测试用户

在本部分中，在 Sectigo Certificate Manager 中创建名为 Britta Simon 的用户。 与 [Sectigo Certificate Manager 支持团队](https://sectigo.com/support)合作，在 Sectigo Certificate Manager 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，测试 Azure AD 单一登录配置。

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>从 Sectigo Certificate Manager 进行测试（SP 启动的单一登录）

浏览到特定于客户的 URL（对于 Sectigo Certificate Manager 主实例，为 https:\//cert-manager.com/customer/\<customerURI\>/），并选择“或在登录时使用”下面的按钮。  如果配置正确，你将自动登录到 Sectigo Certificate Manager。

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>从 Azure 单一登录配置进行测试（IDP 启动的单一登录）

在“Sectigo Certificate Manager”应用程序集成页上，选择“单一登录”并选择“测试”按钮  。  如果配置正确，你将自动登录到 Sectigo Certificate Manager。

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>使用“我的应用”门户进行测试（IDP 启动的单一登录）

在“我的应用”门户中选择“Sectigo Certificate Manager”。  如果配置正确，你将自动登录到 Sectigo Certificate Manager。 有关“我的应用”门户的详细信息，请参阅[访问和使用“我的应用”门户上的应用](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Sectigo Certificate Manager 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。