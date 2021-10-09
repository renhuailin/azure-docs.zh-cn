---
title: 教程：Azure Active Directory 与 RStudio Connect SAML 身份验证的集成
description: 了解如何在 Azure Active Directory 与 RStudio Connect SAML 身份验证之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2021
ms.author: jeedes
ms.openlocfilehash: 76550ad5f3a37058d37015e8e2d71ad727b2d5b8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665808"
---
# <a name="tutorial-azure-ad-sso-integration-with-rstudio-connect-saml-authentication"></a>教程：Azure Active Directory 与 RStudio Connect SAML 身份验证的集成

本教程介绍如何将 RStudio Connect SAML 身份验证与 Azure Active Directory (Azure AD) 集成。 将 RStudio Connect SAML 身份验证与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 RStudio Connect SAML 身份验证。
* 让用户使用其 Azure AD 帐户自动登录到 RStudio Connect SAML 身份验证。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 RStudio Connect SAML 身份验证的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* RStudio Connect SAML 身份验证。 提供 [45 天的免费评估](https://www.rstudio.com/products/connect/)。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* RStudio Connect SAML 身份验证支持 SP 和 IDP 发起的 SSO。

* RStudio Connect SAML 身份验证支持实时用户预配。

## <a name="add-rstudio-connect-saml-authentication-from-the-gallery"></a>从库中添加 RStudio Connect SAML 身份验证

若要配置 RStudio Connect SAML 身份验证与 Azure AD 的集成，需将 RStudio Connect SAML 身份验证从库添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“RStudio Connect SAML 身份验证” 。
1. 在结果面板中选择“RStudio Connect SAML 身份验证”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>配置并测试 RStudio Connect SAML 身份验证的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 RStudio Connect SAML 身份验证的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 RStudio Connect SAML 身份验证中的相关用户之间建立链接关系。

若要配置并测试 RStudio Connect SAML 身份验证的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. [配置 RStudio Connect SAML 身份验证 SSO](#configure-rstudio-connect-saml-authentication-sso) - 在应用程序端配置单一登录设置。
    1. [创建 RStudio Connect SAML 身份验证测试用户](#create-rstudio-connect-saml-authentication-test-user) - 在 RStudio Connect SAML 身份验证中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“RStudio Connect SAML 身份验证”应用程序集成页上，找到“管理”部分，然后选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤，将 `<example.com>` 替换为 RStudio Connect SAML 身份验证服务器地址和端口 ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<example.com>/__login__/saml`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<example.com>/__login__/saml/acs`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<example.com>/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 它们由 RStudio Connect SAML 身份验证服务器地址（在上述示例中为 `https://example.com`）确定。 如果遇到问题，请联系 [RStudio Connect SAML 身份验证支持团队](mailto:support@rstudio.com)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. RStudio Connect SAML 身份验证应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 RStudio Connect SAML 身份验证应用程序要求通过 user.mail 对 nameidentifier 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射  。

    ![image](common/edit-attribute.png)

7. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

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

在本部分中，通过授予 B.Simon 访问 RStudio Connect SAML 身份验证的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中，选择“RStudio Connect SAML 身份验证”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>配置 RStudio Connect SAML 身份验证 SSO

若要为 RStudio Connect SAML 身份验证配置单一登录，需要使用上面使用的应用联合元数据 URL 和服务器地址  。 在 `/etc/rstudio-connect.rstudio-connect.gcfg` 处的 RStudio Connect SAML 身份验证配置文件中完成该操作。

以下是一个示例配置文件：

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

将“服务器地址”存储在 `Server.Address` 值中，将“应用联合元数据 URL”存储在 `SAML.IdPMetaData` 值中   。 请注意，此示例配置使用未加密的 HTTP 连接，而 Azure AD 要求使用加密的 HTTPS 连接。 可以在 RStudio Connect SAML 身份验证之前使用[反向代理](https://docs.rstudio.com/connect/admin/proxy/)，也可以将 RStudio Connect SAML 身份验证配置为[直接使用 HTTPS](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS)。 

如果在配置时遇到问题，可阅读 [RStudio Connect SAML 身份验证管理员指南](https://docs.rstudio.com/connect/admin/authentication/saml/)或发送电子邮件至 [RStudio 支持团队](mailto:support@rstudio.com)寻求帮助。

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>创建 RStudio Connect SAML 身份验证测试用户

在本部分，将在 RStudio Connect SAML 身份验证中创建名为 Britta Simon 的用户。 RStudio Connect SAML 身份验证支持默认已启用的实时预配。 此部分不存在任何操作项。 尝试访问 RStudio Connect SAML 身份验证时，如果 RStudio Connect SAML 身份验证中尚不存在用户，则系统会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 RStudio Connect SAML 身份验证登录 URL，可在其中启动登录流。  

* 直接转到 RStudio Connect SAML 身份验证登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 RStudio Connect SAML 身份验证。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“RStudio Connect SAML 身份验证”磁贴后：如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到已设置 SSO 的 RStudio Connect SAML 身份验证。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 RStudio Connect SAML 身份验证后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。