---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 CylancePROTECT 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 CylancePROTECT 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 6ed85566f1654e117c2e0871b44fcc14ea096060
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124783798"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>教程：Azure Active Directory 单一登录 (SSO) 与 CylancePROTECT 集成

本教程介绍了如何将 CylancePROTECT 与 Azure Active Directory (Azure AD) 集成。 将 CylancePROTECT 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 CylancePROTECT。
* 让用户使用其 Azure AD 帐户自动登录到 CylancePROTECT。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 CylancePROTECT 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* CylancePROTECT 支持 **IDP** 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-cylanceprotect-from-the-gallery"></a>从库中添加 CylancePROTECT

要配置 CylancePROTECT 与 Azure AD 的集成，需要从库中将 CylancePROTECT 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中键入“CylancePROTECT”。
1. 在结果面板中选择“CylancePROTECT”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-cylanceprotect"></a>配置并测试 CylancePROTECT 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 CylancePROTECT 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 CylancePROTECT 中的相关用户之间建立链接关系。

若要配置并测试 CylancePROTECT 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 CylancePROTECT SSO](#configure-cylanceprotect-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 CylancePROTECT 测试用户](#create-cylanceprotect-test-user)** - 在 CylancePROTECT 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中，在 CylancePROTECT 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“使用 SAML 设置单一登录”页上，输入以下字段的值： 

    a. 在“标识符”文本框中，键入以下 URL 之一：
    
    | 区域 | URL 值 |
    |----------|---------|
    | 亚太东北部 (APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 亚太东南部 (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | 欧洲中部 (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 北美|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 南美洲 (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. 在“回复 URL”文本框中，键入以下 URL 之一：
    
    | 区域 | URL 值 |
    |----------|---------|
    | 亚太东北部 (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 亚太东南部 (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 欧洲中部 (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 北美|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 南美洲 (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. CylancePROTECT 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 CylancePROTECT 应用程序要求通过 **user.mail** 对 **nameidentifier** 进行映射并删除所有其余声明，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射。

    ![image](common/edit-attribute.png)

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 CylancePROTECT”部分中，根据你的需要复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

> [!NOTE]
> 在文本编辑器中打开下载的 Base64 编码的证书，仅复制 **START** 和 **END** 标记之间的文本，并将其粘贴到 Cylance 管理门户中。

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

在本部分中，你将通过授予 B.Simon 访问 CylancePROTECT 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“CylancePROTECT”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-cylanceprotect-sso"></a>配置 CylancePROTECT SSO

若要在 **CylancePROTECT** 端配置单一登录，需要将下载的 **证书 (Base64)** 以及从 Azure 门户复制的相应 URL 发送给 [CylancePROTECT 支持团队](https://www.cylance.com/en-us/resources/support/support-overview.html)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。 有关详细信息，请参阅 Cylance 文档：[https://support.cylance.com/s/](https://support.cylance.com/s/)。

### <a name="create-cylanceprotect-test-user"></a>创建 CylancePROTECT 测试用户

在本部分中，你将在 CylancePROTECT 中创建名为 Britta Simon 的用户。 在控制台管理员的协助下，将用户添加到 CylancePROTECT 平台。 Azure Active Directory 帐户持有者将收到一封电子邮件，并且将单击其中的链接以在激活帐户前确认帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应能够自动登录到为其设置了 SSO 的 CylancePROTECT。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 CylancePROTECT 磁贴时，应当会自动登录到已为其设置了 SSO 的 CylancePROTECT。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 CylancePROTECT 后，可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。