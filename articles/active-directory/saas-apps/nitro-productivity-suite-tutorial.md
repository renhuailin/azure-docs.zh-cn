---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Nitro Productivity Suite 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Nitro Productivity Suite 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 85c880cc318b025a9ca70a19eaa8c743c3e243e8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124762429"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Nitro Productivity Suite 的集成

本教程介绍如何将 Nitro Productivity Suite 与 Azure Active Directory (Azure AD) 集成。 将 Nitro Productivity Suite 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Nitro Productivity Suite。
* 让用户使用其 Azure AD 帐户自动登录到 Nitro Productivity Suite。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

要开始，需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个 Nitro Productivity Suite [企业订阅](https://www.gonitro.com/pricing)。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Nitro Productivity Suite 支持 SP 和 IDP 发起的 SSO   。
* Nitro Productivity Suite 支持实时用户预配  。

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>从库中添加 Nitro Productivity Suite

若要配置 Nitro Productivity Suite 与 Azure AD 的集成，需要从库中将 Nitro Productivity Suite 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧窗格中，选择“Azure Active Directory”。
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 Nitro Productivity Suite。  
1. 在结果中选择“Nitro Productivity Suite”，然后添加该应用。  在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>为 Nitro Productivity Suite 配置并测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 Nitro Productivity Suite 的 Azure AD SSO。  若要使 SSO 有效，需要在 Azure AD 用户与 Nitro Productivity Suite 相关用户之间建立关联。

若要配置并测试 Nitro Productivity Suite 的 Azure AD SSO，请完成以下构建基块：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。

    a. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以使用 B.Simon 测试 Azure AD 单一登录。
    
    b. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够使用 Azure AD 单一登录。
    
2. [创建 Nitro Productivity Suite 测试用户](#create-a-nitro-productivity-suite-test-user) - 在 Nitro Productivity Suite 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Nitro Productivity Suite”应用程序集成页上，找到“管理”部分 。 选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“SAML 签名证书”部分中，找到“证书(Base64)”。   选择“下载”以下载证书，并将其保存在计算机上。 

    ![“SAML 签名证书”部分的屏幕截图，其中突出显示了“下载”链接](common/certificatebase64.png)
    
1. 在“设置 Nitro Productivity Suite”  部分中，选择“登录 URL”  旁边的复制图标。
    
    ![“设置 Nitro Productivity Suite”部分的屏幕截图，其中突出显示了 URL 和复制图标](common/copy-configuration-urls.png)
    
1. 在 [Nitro 管理门户](https://admin.gonitro.com/)中的“企业设置”页上找到“单一登录”部分。   选择“设置 SAML SSO”  。

    a. 将上面步骤中的“登录 URL”粘贴到“登录 URL”字段中。  
    
    b. 将前面步骤中的“证书(Base64)”上传到“X509 签名证书”字段中。  
    
    c. 选择“提交”。 
    
    d. 选择“启用单一登录”  。


1. 返回到 [Azure 门户](https://portal.azure.com/)。 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔图标以编辑设置   。

   ![“设置 SAML 单一登录”页的屏幕截图，其中突出显示了铅笔图标](common/edit-urls.png)

1. 若要在“IDP”发起模式下配置应用程序，请在“基本 SAML 配置”部分输入以下字段的值：  

    a. 在“标识符”文本框中，复制并粘贴 [Nitro 管理门户](https://admin.gonitro.com/)中的“SAML 实体 ID”字段   。 它应该有以下模式：`urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. 在“回复 URL”文本框中，复制并粘贴 [Nitro 管理门户](https://admin.gonitro.com/)中的“ACS URL”字段   。 它应该有以下模式：`https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. 若要在“SP”发起模式下配置应用程序，请选择“设置其他 URL”并执行以下步骤：  

    在“登录 URL”文本框中，键入 URL：  `https://sso.gonitro.com/login`

1. 选择“保存”。 

1. Nitro Productivity Suite 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![默认属性的屏幕截图](common/default-attributes.png)

1. 除上述属性以外，Nitro Productivity Suite 应用程序还要求在 SAML 响应中传回其他几个属性。 这些属性已预先填充，但可以根据需要查看它们。
    
    | 名称  |  源属性|
    | ---------------| --------------- |
    | employeeNumber |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。   
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下密码  。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Nitro Productivity Suite 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  
1. 在应用程序列表中选择“Nitro Productivity Suite”。 
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组”   。
1. 选择“添加用户”。 然后，在“添加分配”对话框中选择“用户和组”。  
1. 在“用户和组”对话框中，从用户列表中选择“B.Simon”。   然后选择屏幕底部的“选择”  。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中选择“分配”。

### <a name="create-a-nitro-productivity-suite-test-user"></a>创建 Nitro Productivity Suite 测试用户

Nitro Productivity Suite 支持默认启用的实时用户预配。 无需执行其他操作。 如果 Nitro Productivity Suite 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

1. 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Nitro Productivity Suite 登录 URL，可以从那里启动登录流。  

2. 直接转到 Nitro Productivity Suite 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应该会自动登录到为其设置了 SSO 的 Nitro Productivity Suite 

还可以使用 Microsoft 访问面板在任何模式下测试此应用程序。 在访问面板中单击 Nitro Productivity Suite 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页，以便启动登录流；如果是在 IDP 模式下配置的，则应该会自动登录到为其设置了 SSO 的 Nitro Productivity Suite。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)（访问面板简介）。


## <a name="next-steps"></a>后续步骤

配置 Nitro Productivity Suite 后，可以强制实施会话控制，从而实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。