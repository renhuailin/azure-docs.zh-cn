---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Splashtop 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Splashtop 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2021
ms.author: jeedes
ms.openlocfilehash: 8458b3d110d8eaaa9871d178d0ff948778914f2e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785546"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Splashtop 的集成

本教程介绍如何将 Splashtop 与 Azure Active Directory (Azure AD) 集成。 将 Splashtop 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Splashtop。
* 让用户使用其 Azure AD 帐户自动登录到 Splashtop。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Splashtop 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Splashtop 支持 SP 发起的 SSO。
* Splashtop 支持[自动用户预配和取消预配](splashtop-provisioning-tutorial.md)（推荐）。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-splashtop-from-the-gallery"></a>从库中添加 Splashtop

若要配置 Splashtop 与 Azure AD 的集成，需要从库中将 Splashtop 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Splashtop**。 
1. 在结果面板中选择“Splashtop”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-splashtop"></a>配置并测试 Splashtop 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 Splashtop 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Splashtop 中的相关用户之间建立链接关系。

若要配置并测试 Splashtop 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Splashtop SSO](#configure-splashtop-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Splashtop 测试用户](#create-splashtop-test-user)** - 在 Splashtop 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Splashtop”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，执行以下步骤：

    在“登录 URL”文本框中，键入 URL：  `https://my.splashtop.com/login/sso`

1. Splashtop 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 映射到 **user.userprincipalname**。 TicketManager 应用程序要求通过 **user.mail** 对 **nameidentifier** 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射。 

    ![屏幕截图显示“用户属性”，并且已选择“编辑”图标。](common/edit-attribute.png)

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上。    

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Splashtop”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 Splashtop 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Splashtop”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-splashtop-sso"></a>配置 Splashtop SSO

在本部分，需要从 [Splashtop Web 门户](https://my.splashtop.com/login)申请新的 SSO 方法。
1. 在 Splashtop Web 门户中，转到“帐户信息” / “团队”选项卡，向下滚动并找到“单一登录”部分。    然后单击“申请新的 SSO 方法”。 

    ![显示“单一登录”页的屏幕截图，可在其中选择“申请新的 SSO 方法”。](media/splashtop-tutorial/new-method.png)

1. 在申请窗口中指定 **SSO 名称**， 例如“新 Azure”。选择“Azure”作为 IDP 类型，插入从 Azure 门户上的 Splashtop 应用程序复制的“登录 URL”和“Azure AD 标识符”。   

    ![显示“申请 SSO 方法”页的屏幕截图，可在其中输入名称和其他信息。](media/splashtop-tutorial/new-azure.png)

1. 对于证书信息，请右键单击从 Azure 门户上的 Splashtop 应用程序下载的证书文件，使用记事本对其进行编辑，然后复制内容并将其粘贴到“下载证书(Base64)”字段中。 

    ![显示选择了证书文件并使用记事本打开该文件的屏幕截图。](media/splashtop-tutorial/certificate.png)
    ![显示证书文件的内容的屏幕截图。](media/splashtop-tutorial/file.png)
    ![显示“下载证书”文本框的屏幕截图。](media/splashtop-tutorial/azure.png)

1. 就这么简单！ 单击“保存”，然后 Splashtop SSO 验证团队将就验证信息事宜与你取得联系，并激活该 SSO 方法。

### <a name="create-splashtop-test-user"></a>创建 Splashtop 测试用户

1. 激活 SSO 方法后，请检查新建的 SSO 方法，并在“单一登录”部分启用该方法。

    ![显示“单一登录”页的屏幕截图，可在其中启用新方法。](media/splashtop-tutorial/enable.png)

1. 使用新建的 SSO 方法邀请测试用户（例如 `B.Simon@contoso.com`）加入 Splashtop 团队。

    ![显示“邀请用户”页的屏幕截图，可在其中选择新方法。](media/splashtop-tutorial/invite.png)

1. 还可以将现有的 Splashtop 帐户更改为 SSO 帐户，具体请参阅[说明](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-)。

1. 就这么简单！ 可以使用 SSO 帐户登录到 Splashtop Web 门户或 Splashtop 商业应用。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Splashtop 登录 URL，你可以从那里启动登录流。 

* 直接转到 Splashtop 登录 URL，从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Splashtop 磁贴时，将会重定向到 Splashtop 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Splashtop 后，可以强制实施会话控制，从而实时防止组织的敏感数据遭受外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。