---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 BlogIn 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 BlogIn 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2021
ms.author: jeedes
ms.openlocfilehash: ef77d3001d09364e754a38897c769a41feaa0db3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679477"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blogin"></a>教程：Azure Active Directory 单一登录 (SSO) 与 BlogIn 的集成

本教程介绍如何将 BlogIn 与 Azure Active Directory (Azure AD) 集成。 将 BlogIn 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 BlogIn。
* 让用户使用其 Azure AD 帐户自动登录到 BlogIn。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 BlogIn 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* BlogIn 支持 SP 和 IDP 发起的 SSO。
* BlogIn 支持实时用户预配。
* BlogIn 支持[自动用户预配](blogin-provisioning-tutorial.md)。

## <a name="add-blogin-from-the-gallery"></a>从库中添加 BlogIn

若要配置 BlogIn 与 Azure AD 的集成，需要从库中将 BlogIn 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“BlogIn” 。
1. 从结果面板中选择“BlogIn”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-blogin"></a>配置并测试 BlogIn 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 BlogIn 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 BlogIn 中的相关用户之间建立链接关系。

若要配置并测试 BlogIn 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 BlogIn SSO](#configure-blogin-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 BlogIn 测试用户](#create-blogin-test-user)** - 在 BlogIn 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“BlogIn”应用程序集成页上，找到“管理”部分，并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.blogin.co/`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.blogin.co/sso/saml/callback`

1. 若要在“SP”发起模式下配置应用程序，请单击“设置其他 URL”并执行以下步骤： 

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.blogin.co/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 可以在 BlogIn 的“设置”页上获取这些字段的确切值（“用户身份验证”选项卡 >“配置 SSO 和用户预配”）  。 或者，也可以联系 [BlogIn 客户端支持团队](mailto:support@blogin.co)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. BlogIn 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![图像](common/default-attributes.png)

1. 除了上述属性，BlogIn 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 | 源属性 |
    | ------ | --------- |
    | title |user.jobtitle |
    

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分中，将通过授予 B.Simon 访问 BlogIn 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“BlogIn”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-blogin-sso"></a>配置 BlogIn SSO

若要在 BlogIn 上配置单一登录，请登录到你的 BlogIn 帐户并执行以下步骤：

1. 转到“设置” > “用户身份验证” > “配置 SSO 和用户预配”  。
2. 在下一个屏幕上，将单一登录状态更改为“开启”，并为将显示在登录屏幕上的 SSO 登录按钮选择自定义名称。

3. 如果你在上一部分的最后一步中保存了应用联合元数据 URL，请选择配置方法“元数据 URL”并将应用联合元数据 URL 粘贴到元数据 URL 字段中  。 否则，请将配置方法更改为“手动”，手动填充“标识提供程序 SSO URL (登录 URL)”和“标识提供程序颁发者(实体 ID)”，并上传从 Azure AD 获得的“证书(base64)”   。

4. 使用 SSO 为加入 BlogIn 的新用户选择默认用户角色。

5. 选择“保存更改”。

有关在 BlogIn 上设置 SSO 的更详细说明，请参阅[如何在 BlogIn 上为 Microsoft Azure AD 设置 SSO](https://blogin.co/blog/how-to-set-up-single-sign-on-sso-for-microsoft-azure-active-directory-azure-ad-267/)。 如果你有任何疑问或需要帮助，可随时联系 [BlogIn 支持团队](mailto:support@blogin.co)。

### <a name="create-blogin-test-user"></a>创建 BlogIn 测试用户

本部分将在 BlogIn 中创建一个名为 B.Simon 的用户。 BlogIn 支持默认启用的实时用户设置。 此部分不存在任何操作项。 如果 BlogIn 中尚不存在用户，则会在身份验证后创建一个新用户。

BlogIn 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./blogin-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 BlogIn 登录 URL，你可以从那里启动登录流。  

* 直接转到 BlogIn 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 BlogIn。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“BlogIn”磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 BlogIn。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 BlogIn 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。