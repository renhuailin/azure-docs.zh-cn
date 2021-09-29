---
title: 教程：Azure AD SSO 与 Change Process Management 集成
description: 了解如何在 Azure Active Directory 与 Change Process Management 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: 97445fd55288539620a5a506a714a2ee095cf77f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734417"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-change-process-management"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Change Process Management 的集成

本教程介绍如何将 Change Process Management 与 Azure Active Directory (Azure AD) 集成。 将 Change Process Management 与 Azure AD 集成后，可以：

* 使用 Azure AD 来控制谁有权访问 Change Process Management。
* 允许用户使用其 Azure AD 帐户自动登录到 Change Process Management。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用程序与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 中的应用程序](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Change Process Management 订阅。

## <a name="tutorial-description"></a>教程说明

本教程在测试环境中配置并测试 Azure AD SSO。

Change Process Management 支持 IDP 发起的 SSO。

配置 Change Process Management 后，即可强制实施会话控制，从而实时保护组织的敏感数据以防外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-change-process-management-from-the-gallery"></a>从库中添加 Change Process Management

若要配置 Change Process Management 与 Azure AD 的集成，需要从库中将 Change Process Management 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左窗格中选择“Azure Active Directory”。
1. 转到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，输入“Change Process Management” 。
1. 在结果面板中选择“Change Process Management”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-change-process-management"></a>配置并测试 Change Process Management 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Change Process Management 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Change Process Management 相关用户之间建立关联。

若要配置并测试 Change Process Management 的 Azure AD SSO，请执行以下大致步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以测试 Azure AD 单一登录。
    1. **[向测试用户授予访问权限](#grant-access-to-the-test-user)** ，使用户能够使用 Azure AD 单一登录。
1. 在应用程序端[配置 Change Process Management SSO](#configure-change-process-management-sso)。
    1. [创建 Change Process Management 测试用户](#create-a-change-process-management-test-user)，对应于该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Change Process Management”应用程序集成页上，找到“管理”部分，然后选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔按钮以编辑设置： 

   ![“基本 SAML 配置”对应的铅笔按钮](common/edit-urls.png)

1. 在“设置 SAML 单一登录”页面上，执行以下步骤：

    a. 在“标识符”框中，输入采用以下模式的 URL：`https://<hostname>:8443/`

    b. 在“回复 URL”框中，输入采用以下模式的 URL：`https://<hostname>:8443/changepilot/saml/sso`

    > [!NOTE]
    > 前面的“标识符”和“回复 URL”值不是应使用的实际值 。 要获取实际值，请联系 [Change Process Management 支持团队](mailto:support@realtech-us.com)。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“设置 SAML 单一登录”页面上的“SAML 签名证书”部分，选择“证书(Base64)”对应的“下载”链接，以下载证书并将其保存到计算机上：   ：

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Change Process Management”部分中，根据要求复制相应的一个或多个 URL：

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B. Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中选择“Azure Active Directory”。 选择“用户”，然后选择“所有用户” 。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中，完成以下步骤：
   1. 在“名称”框中，输入 **B.Simon**。  
   1. 在“用户名”框中，输入 \<username>@\<companydomain>.\<extension>。 例如，`B.Simon@contoso.com`。
   1. 选择“显示密码”，然后记下“密码”框中显示的值。 
   1. 选择“创建”  。

### <a name="grant-access-to-the-test-user"></a>向测试用户授予访问权限

在本部分中，你将通过授予 B.Simon 用户访问 Change Process Management 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Change Process Management”。
1. 在应用概览页上，在“管理”部分中选择“用户和组”： 

   ![选择“用户和组”](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![选择“添加用户”](common/add-assign-user.png)

1. 在“用户和组”对话框中的“用户”列表内选择“B.Simon”，然后单击屏幕底部的“选择”按钮。    
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮 。
1. 在“添加分配”对话框中选择“分配”。 

## <a name="configure-change-process-management-sso"></a>配置 Change Process Management SSO

若要在 Change Process Management 端配置单一登录，需要将下载的 Base64 证书以及从 Azure 门户复制的相应 URL 发送给 [Change Process Management 支持团队](mailto:support@realtech-us.com)。 支持人员将在两端配置正确的 SAML SSO 连接。

### <a name="create-a-change-process-management-test-user"></a>创建 Change Process Management 测试用户
 与 [Change Process Management 支持团队](mailto:support@realtech-us.com)协作，将名为 B.Simon 的用户添加到 Change Process Management 中。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用访问面板测试 Azure AD SSO 配置。

在访问面板中选择“Change Process Management”磁贴时，应会自动登录到设置了 SSO 的 Change Process Management 实例。 有关访问面板的详细信息，请参阅[访问面板简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程](./tutorial-list.md)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)

- [在 Azure AD 中试用 Change Process Management](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Change Process Management](/cloud-app-security/proxy-intro-aad)