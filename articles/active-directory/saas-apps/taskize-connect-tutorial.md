---
title: 教程：Azure AD 与 Taskize Connect 的 SSO 集成
description: 了解如何在 Azure Active Directory 与 Taskize Connect 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/23/2021
ms.author: jeedes
ms.openlocfilehash: bf6046b92e65a1184fe39657d359e0381aa9f7f4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553143"
---
# <a name="tutorial-azure-ad-sso-integration-with-taskize-connect"></a>教程：Azure AD 与 Taskize Connect 的 SSO 集成

在本教程中，你将了解如何集成 Taskize Connect 与 Azure Active Directory (Azure AD)。 将 Taskize Connect 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 Taskize Connect。
* 让用户使用其 Azure AD 帐户自动登录到 Taskize Connect。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Taskize Connect 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Taskize Connect 支持 SP 和 IDP 发起的 SSO。
* Taskize Connect 支持[自动用户预配](taskize-connect-provisioning-tutorial.md)。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-taskize-connect-from-the-gallery"></a>从库中添加 Taskize Connect

要配置 Taskize Connect 与 Azure AD 的集成，需要从库中将 Taskize Connect 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 Taskize Connect   。
1. 在结果面板中选择 Taskize Connect，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-taskize-connect"></a>配置并测试 Taskize Connect 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Taskize Connect 的 Azure AD SSO  。 要使 SSO 正常工作，需要在 Azure AD 用户与 Taskize Connect 相关用户之间建立链接关系。

若要配置并测试 Taskize Connect 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Taskize Connect SSO](#configure-taskize-connect-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Taskize Connect 测试用户](#create-taskize-connect-test-user)** - 在Taskize Connect 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Taskize Connect 应用程序集成页面上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤 ：

    a. 在“回复 URL”文本框中，键入以下 URL 之一：

    | 回复 URL |
    | ----- |
    |`https://connect.taskize.com/Shibboleth.sso/SAML2/POST`|
    |`https://help.taskize.com/Shibboleth.sso/SAML2/POST`|

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://connect.taskize.com/connect/`

1. Taskize Connect 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/edit-attribute.png)

1. 除了上述属性，Taskize Connect 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ------------------- | -------------------- |    
    | urn:oid:0.9.2342.19200300.100.1.3 | user.userprincipalname |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Taskize Connect”部分，根据要求复制相应的 URL  。

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

在本部分中，你将通过授予 B.Simon 访问 Taskize Connect 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中，选择 Taskize Connect  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-taskize-connect-sso"></a>配置 Taskize Connect SSO

要在 Taskize Connect 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [Taskize Connect 支持团队](mailto:support@taskize.com)   。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-taskize-connect-test-user"></a>创建 Taskize Connect 测试用户

本部分的目的是在 Taskize Connect 中创建名为 B.Simon 的用户。 Taskize Connect 支持实时预配，并且默认启用。 此部分不存在任何操作项。 尝试访问 Taskize Connect 期间，如果尚不存在用户，则会创建一个新用户。

>[!Note]
>如果需要手动创建用户，请联系 [Taskize Connect 支持团队](mailto:support@taskize.com)。

Taskize Connect 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./taskize-connect-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Taskize Connect 登录 URL，可在其中启动登录流。  

* 直接转到 Taskize Connect 登录 URL，从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Taskize Connect。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 单击“我的应用”中的“Taskize Connect”磁贴时，如果该应用程序是在 SP 模式下配置的，则会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则会自动登录到为其设置了 SSO 的 Taskize Connect。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Taskize Connect 后，即可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。