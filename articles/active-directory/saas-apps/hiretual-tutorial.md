---
title: 教程：Azure AD 与 Hiretual-SSO 的 SSO 集成
description: 了解如何在 Azure Active Directory 与 Hiretual-SSO 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2021
ms.author: jeedes
ms.openlocfilehash: c459db2d895042d9edfa4bada8b730c96b95ed55
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403189"
---
# <a name="tutorial-azure-ad-sso-integration-with-hiretual-sso"></a>教程：Azure AD 与 Hiretual-SSO 的 SSO 集成

本教程介绍如何将 Hiretual-SSO 与 Azure Active Directory (Azure AD) 集成。 将 Hiretual-SSO 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Hiretual-SSO。
* 让用户使用其 Azure AD 帐户自动登录到 Hiretual-SSO。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Hiretual-SSO 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Hiretual-SSO 支持 SP 和 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-hiretual-sso-from-the-gallery"></a>从库中添加 Hiretual-SSO

要配置 Hiretual-SSO 与 Azure AD 的集成，需要从库中将 Hiretual-SSO 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Hiretual-SSO”。
1. 在结果面板中选择“Hiretual-SSO”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-hiretual-sso"></a>配置并测试 Hiretual-SSO 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Hiretual-SSO 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Hiretual-SSO 中的相关用户之间建立关联。

若要配置并测试 Hiretual-SSO 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Hiretual-SSO](#configure-hiretual-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Hiretual-SSO 测试用户](#create-hiretual-sso-test-user) - 在 Hiretual-SSO 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 Hiretual-SSO 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置部分”中执行以下步骤   ：

    a. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://api.hiretual.com/v1/users/saml/login/<teamId>`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://app.hiretual.com/`

    > [!NOTE]
    > 此值不是真实值。 请使用实际回复 URL 更新此值。 请联系 [Hiretual-SSO 客户端支持团队](mailto:support@hiretual.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Hiretual-SSO 应用程序需要特定格式的 SAML 断言，这就需要将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，Hiretual-SSO 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 | 源属性 |
    | ---------- | --------- |
    | firstName | user.givenname |
    | title | user.jobtitle |
    | lastName | user.surname |

1. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user&quot;></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Hiretual-SSO 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Hiretual-SSO”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name=&quot;configure-hiretual-sso&quot;></a>配置 Hiretual-SSO

1. 以管理员身份登录到 Hiretual-SSO 公司站点。

1. 转到“安全性和符合性” > “单一登录” 。

1. 在“SAML2.0 身份验证”页上执行以下步骤：

    ![ SSO 配置的屏幕截图。](./media/hiretual-tutorial/configuration.png &quot;SSO 配置")

    1. 在“SAML2.O SSO URL”文本框中，粘贴从 Azure 门户复制的“用户访问 URL” 。

    1. 从元数据文件复制“实体 ID”值并将其粘贴到“标识提供者颁发者”文本框中 。

    1. 从元数据文件复制“X509 证书”值并将内容粘贴到“证书”文本框中 。

    1. 根据要求手动填充必填属性，然后单击“保存”。

    1. 启用“单一登录连接状态”按钮。

    1. 首先测试单一登录集成，然后启用“管理员 SP 发起的单一登录”按钮。 

    > [!NOTE]
    > 如果单一登录配置有任何错误，或者在连接管理员 SP 发起的单一登录后无法登录 Hiretual-SSO Web 应用/扩展，请联系 [Hiretual-SSO 支持团队](mailto:support@hiretual.com)。
    
### <a name="create-hiretual-sso-test-user"></a>创建 Hiretual-SSO 测试用户

在本部分中，将在 Hiretual-SSO 中创建一个名为 Britta Simon 的用户。 请与 [Hiretual-SSO 支持团队](mailto:support@hiretual.com)协作，将用户添加到 Hiretual-SSO 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Hiretual-SSO 登录 URL，可从中启动登录流。  

* 直接转到 Hiretual-SSO 登录 URL，并从中启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Hiretual-SSO。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“Hiretual-SSO”磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Hiretual-SSO。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Hiretual-SSO 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。