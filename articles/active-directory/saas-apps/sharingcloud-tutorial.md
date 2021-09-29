---
title: Tutorial:Azure Active Directory 单一登录 (SSO) 与 SharingCloud 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Instant Suite 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/10/2021
ms.author: jeedes
ms.openlocfilehash: d42394cec8653266f8f9e53ee9ec48f517d83186
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124741198"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharingcloud"></a>Tutorial:Azure Active Directory 单一登录 (SSO) 与 SharingCloud 的集成

本教程介绍如何将 SharingCloud 与 Azure Active Directory (Azure AD) 集成。 将 SharingCloud 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SharingCloud。
* 让用户使用其 Azure AD 帐户自动登录到 SharingCloud。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Sapient 单一登录 (SSO) 的订阅。


## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SharingCloud 支持 SP 和 IDP 发起的 SSO
* SharingCloud 支持实时用户预配

## <a name="adding-sharingcloud-from-the-gallery"></a>从库中添加 SharingCloud

要配置 SharingCloud 与 Azure AD 的集成，需要从库中将 SharingCloud 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“SharingCloud” 。
1. 从结果面板中选择“SharingCloud”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-sharingcloud"></a>配置并测试 SharingCloud 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 SharingCloud 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 SharingCloud 中的相关用户之间建立关联。

若要配置并测试 SharingCloud 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 SharingCloud SSO](#configure-sharingcloud-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 SharingCloud 测试用户](#create-sharingcloud-test-user)** - 在 SharingCloud 中创建 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 SharingCloud 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    通过 SharingCloud 提供的 XML 文件上传元数据文件。 请联系 [SharingCloud 客户端支持团队](mailto:support@sharingcloud.com)获取该文件。

    ![该屏幕截图显示了“基本 SAML 配置”用户界面，其中突出显示了“上传元数据文件”链接。](common/upload-metadata.png)
    
    选择提供的元数据文件，然后单击“上传”。

    ![该屏幕截图显示了提供的元数据文件用户界面，其中突出显示了“选择文件”图标和“上传”按钮。](common/browse-upload-metadata.png)

1. SharingCloud 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![屏幕截图显示了“用户特性”用户界面，其中突出显示了“编辑”图标。](common/edit_attribute.png)

1. 除上述属性以外，SharingCloud 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ---------------| --------- |
    | urn:sharingcloud:sso:firstname | user.givenname |
    | urn:sharingcloud:sso:lastname | user.surname |
    | urn:sharingcloud:sso:email | user.mail |

1. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，根据要求单击“复制”图标，以通过给定的选项复制“联合元数据 URL”   。

    ![要复制的元数据 URL](common/copy_metadataurl.png)

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

本部分将通过授予 B.Simon 访问 SharingCloud 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“SharingCloud”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-sharingcloud-sso"></a>配置 SharingCloud SSO

若要在 **SharingCloud** 端配置单一登录，需将从 Azure 门户复制的“联合元数据 URL”发送给 [SharingCloud 支持团队](mailto:support@sharingcloud.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-sharingcloud-test-user"></a>创建 SharingCloud 测试用户

本部分将在 SharingCloud 中创建一个名为 Britta Simon 的用户。 SharingCloud 支持默认已启用的适时用户预配。 此部分不存在任何操作项。 如果 SharingCloud 中不存在用户，则会在身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 SharingCloud 登录 URL，可以在其中启动登录流。  

* 直接转到 SharingCloud 登录 URL，从此处启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 SharingCloud 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 SharingCloud 磁贴时，如果是在 SP 模式下配置的，则会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 SharingCloud。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 SharingCloud 后，即可强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。