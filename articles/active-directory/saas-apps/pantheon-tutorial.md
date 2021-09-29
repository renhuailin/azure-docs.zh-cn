---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Pantheon 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Pantheon 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/09/2021
ms.author: jeedes
ms.openlocfilehash: 7cd8244a1f3f8eaba151a7d5957308118a635d37
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778640"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pantheon"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Pantheon 集成

本教程介绍如何将 Pantheon 与 Azure Active Directory (Azure AD) 集成。 将 Pantheon 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Pantheon。
* 让用户使用其 Azure AD 帐户自动登录到 Pantheon。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Pantheon 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Pantheon 支持 **IDP** 发起的 SSO。

## <a name="add-pantheon-from-the-gallery"></a>从库中添加 Pantheon

要配置 Pantheon 与 Azure AD 的集成，需要从库中将 Pantheon 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Pantheon**。
1. 在结果面板中选择“Pantheon”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-pantheon"></a>配置并测试 Pantheon 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 Pantheon 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Pantheon 中的相关用户之间建立链接关系。

若要配置并测试 Pantheon 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Pantheon SSO](#configure-pantheon-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Pantheon 测试用户](#create-pantheon-test-user)** - 在 Pantheon 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Pantheon”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

    a. 在“标识符”文本框中，使用以下模式键入值：`urn:auth0:pantheon:<orgname>-SSO`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Pantheon 客户端支持团队](https://pantheon.io/docs/getting-support/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Pantheon 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 Pantheon 应用程序要求通过 **user.mail** 对 **nameidentifier** 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射。

    ![image](common/edit-attribute.png)

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Pantheon”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 Pantheon 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Pantheon”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-pantheon-sso"></a>配置 Pantheon SSO

若要在 Pantheon 端配置单一登录，需要将下载的证书 (Base64) 和相应的已复制 URL 发送给 [Pantheon 支持团队](https://pantheon.io/docs/getting-support/) 。

> [!Note]
> 还需要提供电子邮件域信息和要启用此连接的日期时间。 可以从[此处](https://pantheon.io/docs/sso-organizations/)找到更多详细信息。

### <a name="create-pantheon-test-user"></a>创建 Pantheon 测试用户

在本部分，你将在 Pantheon 中创建名为 B.Simon 的用户。 请按照以下步骤将用户添加到 Pantheon 中。 

>[!NOTE] 
>要使 SSO 正常工作，需要先在 Pantheon 中创建用户。

1. 使用管理员凭据登录到 Pantheon。

2. 导航到“组织”仪表板页。
 
3. 单击“人员”。

4. 单击“添加用户”  。

5. 输入用户的电子邮件地址。

6. 选择用户的角色。

7. 单击“添加用户”  。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你会自动登录到为其设置了 SSO 的 Pantheon。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Pantheon”磁贴时，你应该会自动登录到为其设置了 SSO 的 Pantheon。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Pantheon 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。