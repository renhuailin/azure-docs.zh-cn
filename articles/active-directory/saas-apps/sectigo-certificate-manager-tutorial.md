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
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: c589db84b6221aa23868b3b49aea84f33623619f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673859"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>教程：Azure Active Directory 与 Sectigo Certificate Manager 的集成

本教程介绍如何将 Sectigo Certificate Manager（也称为 SCM）与 Azure Active Directory (Azure AD) 集成。

将 Sectigo Certificate Manager 与 Azure AD 集成可提供以下优势：

* 可以使用 Azure AD 控制有权访问 Sectigo Certificate Manager 的人员。
* 用户可以使用其 Azure AD 帐户自动登录到 Sectigo Certificate Manager（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

有关服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 的应用程序](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Sectigo Certificate Manager 的集成，需准备好以下各项：

* 一个 Azure AD 订阅。 如果还没有 Azure AD 订阅，可在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* Sectigo Certificate Manager 帐户。

> [!NOTE]
> Sectigo 运行 Sectigo Certificate Manager 的多个实例。 Sectigo Certificate Manager 的主实例是 https:\//cert-manager.com，本教程中使用此 URL。  如果你的帐户在另一个实例上，需要相应调整 URL。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中配置和测试 Azure AD 单一登录，并将 Sectigo Certificate Manager 与 Azure AD 集成。

Sectigo Certificate Manager 支持以下功能：

* **SP 发起的单一登录**
* **IDP 发起的单一登录**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>在 Azure 门户中添加 Sectigo Certificate Manager

若要将 Sectigo Certificate Manager 与 Azure AD 集成，必须将 Sectigo Certificate Manager 添加到托管 SaaS 应用列表。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在左侧菜单中，选择“Azure Active Directory”  。

    ![“Azure Active Directory”选项](common/select-azuread.png)

1. 选择“企业应用程序” > “所有应用程序”   。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 若要添加应用程序，请选择“新建应用程序”  。

    ![“新建应用程序”选项](common/add-new-app.png)

1. 在搜索框中，输入“Sectigo Certificate Manager”  。 在搜索结果中，选择“Sectigo Certificate Manager”  ，然后选择“添加”  。

    ![结果列表中的 Sectigo Certificate Manager](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于一个名为 Britta Simon 的测试用户配置和测试 Sectigo Certificate Manager 的 Azure AD 单一登录  。 若要运行单一登录，必须在 Azure AD 用户与 Sectigo Certificate Manager 中的相关用户之间建立链接关系。

若要配置和测试 Sectigo Certificate Manager 的 Azure AD 单一登录，必须完成以下构建基块：

| 任务 | 说明 |
| --- | --- |
| **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** | 使用户能够使用此功能。 |
| **[配置 Sectigo Certificate Manager 单一登录](#configure-sectigo-certificate-manager-single-sign-on)** | 在应用程序中配置单一登录设置。 |
| **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** | 测试名为 Britta Simon 的用户的 Azure AD 单一登录。 |
| **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** | 使 Britta Simon 能够使用 Azure AD 单一登录。 |
| **[创建 Sectigo Certificate Manager 测试用户](#create-a-sectigo-certificate-manager-test-user)** | 在 Sectigo Certificate Manager 中创建一个与 Azure AD 中的 Britta Simon 相对应的关联用户。 |
| **[测试单一登录](#test-single-sign-on)** | 验证配置是否正常工作。 |

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中使用 Sectigo Certificate Manager 配置 Azure AD 单一登录。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Sectigo Certificate Manager”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录选项](common/select-sso.png)

1. 在“选择单一登录方法”窗格中，选择“SAML”或“SAML/WS-Fed”模式以启用单一登录    。

    ![单一登录选择模式](common/select-saml-option.png)

1. 在“设置 SAML 单一登录”窗格中，选择“编辑”（铅笔图标）以打开“基本 SAML 配置”窗格    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分完成以下步骤：

    1. 在“标识符(实体 ID)”框中，为 Sectigo Certificate Manager 主实例输入 https:\//cert-manager.com/shibboleth 。

    1. 在“回复 URL”框中，为 Sectigo Certificate Manager 主实例输入 https:\//cert-manager.com/Shibboleth.sso/SAML2/POST 。
        
    > [!NOTE]
    > 尽管在一般情况下，对于“SP 启动模式”来说“登录 URL”是必需的，但不需要从 Sectigo Certificate Manager 登录。        

1. （可选）在“基本 SAML 配置”部分中，若要配置“IDP 启动模式”并允许运行“测试”，请完成以下步骤：

    1. 选择“设置其他 URL”  。

    1. 在“中继状态”框中，输入你的 Sectigo Certificate Manager 客户特定的 URL。 对于 Sectigo Certificate Manager 主实例，请输入 https:\//cert-manager.com/customer/\<customerURI\>/idp。

    ![Sectigo Certificate Manager 域和 URL 单一登录信息](common/idp-relay.png)

1. 在“用户属性和声明”部分中，完成以下步骤：

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

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>配置 Sectigo Certificate Manager 单一登录

若要在 Sectigo Certificate Manager 端配置单一登录，请将下载的联合元数据 XML 文件发送到 [Sectigo Certificate Manager 支持团队](https://sectigo.com/support)。 Sectigo Certificate Manager 支持团队使用你发送的信息来确保双方都正确设置 SAML 单一登录连接。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

在本部分中，会在 Azure 门户中创建名为“Britta Simon”的测试用户。

1. 在 Azure 门户中，选择“Azure Active Directory” > “用户” > “所有用户”。   

    ![“用户”和“所有用户”选项](common/users.png)

1. 选择“新建用户”。 

    ![“新建用户”选项](common/new-user.png)

1. 在“用户”窗格中完成以下步骤： 

    1. 在“姓名”  框中，输入 **BrittaSimon** 。
  
    1. 在“用户名”框中，输入 brittasimon\@\<your-company-domain>.\<extension\> 。 例如，brittasimon\@contoso.com  。

    1. 选中“显示密码”复选框  。 记录“密码”框中显示的值。

    1. 选择“创建”  。

    ![“用户”窗格](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，授予 Britta Simon 访问 Sectigo Certificate Manager 的权限，以便该用户可使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序” > “Sectigo Certificate Manager”    。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Sectigo Certificate Manager”  。

    ![应用程序列表中的 Sectigo Certificate Manager](common/all-applications.png)

1. 在菜单中选择“用户和组”  。

    ![“用户和组”选项](common/users-groups-blade.png)

1. 选择“添加用户”。  然后，在“添加分配”窗格中选择“用户和组”。  

    ![“添加分配”窗格](common/add-assign-user.png)

1. 在“用户和组”窗格中，在用户列表中选择“Britta Simon”   。 选择“选择”。 

1. 如果希望在 SAML 断言中使用角色值，请在“选择角色”窗格中，从列表中为用户选择相关的角色  。 选择“选择”。 

1. 在“添加分配”窗格中选择“分配”   。

### <a name="create-a-sectigo-certificate-manager-test-user"></a>创建 Sectigo Certificate Manager 测试用户

在本部分中，在 Sectigo Certificate Manager 中创建名为 Britta Simon 的用户。 与 [Sectigo Certificate Manager 支持团队](https://sectigo.com/support)合作，在 Sectigo Certificate Manager 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，测试 Azure AD 单一登录配置。

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>从 Sectigo Certificate Manager 进行测试（SP 启动的单一登录）

浏览到特定于客户的 URL（对于 Sectigo Certificate Manager 主实例，为 https:\//cert-manager.com/customer/\<customerURI\>/），并选择“或在登录时使用”下面的按钮。  如果配置正确，你将自动登录到 Sectigo Certificate Manager。

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>从 Azure 单一登录配置进行测试（IDP 启动的单一登录）

在“Sectigo Certificate Manager”应用程序集成页上，选择“单一登录”并选择“测试”按钮  。  如果配置正确，你将自动登录到 Sectigo Certificate Manager。

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>使用“我的应用”门户进行测试（IDP 启动的单一登录）

在“我的应用”门户中选择“Sectigo Certificate Manager”。  如果配置正确，你将自动登录到 Sectigo Certificate Manager。 有关“我的应用”门户的详细信息，请参阅[访问和使用“我的应用”门户上的应用](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请查看下列文章：

- [用于将 SaaS 应用与 Azure Active Directory 集成的教程列表](./tutorial-list.md)
- [单一登录到 Azure Active Directory 中的应用程序](../manage-apps/what-is-single-sign-on.md)
- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)