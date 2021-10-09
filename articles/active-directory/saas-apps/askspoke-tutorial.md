---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 askSpoke 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 askSpoke 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: 1cf7d63923ddc0fb538ba3bf57b57a94d14862fc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612338"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askspoke"></a>教程：Azure Active Directory 单一登录 (SSO) 与 askSpoke 集成

本教程介绍如何将 askSpoke 与 Azure Active Directory (Azure AD) 集成。 将 askSpoke 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 askSpoke。
* 让用户使用其 Azure AD 帐户自动登录到 askSpoke。
* 在一个中心位置（Azure 门户）管理帐户。


## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 askSpoke 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* askSpoke 支持 SP 和 IDP 发起的 SSO。
* askSpoke 支持实时用户预配。
* askSpoke 支持[自动用户预配](askspoke-provisioning-tutorial.md)。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-askspoke-from-the-gallery"></a>从库中添加 askSpoke

要配置 askSpoke 与 Azure AD 的集成，需要从库中将 askSpoke 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“askSpoke” 。
1. 从结果面板中选择“askSpoke”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-askspoke"></a>配置并测试 askSpoke 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 askSpoke 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 askSpoke 中的相关用户之间建立链接关系。

若要配置并测试 askSpoke 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 askSpoke SSO](#configure-askspoke-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 askSpoke 测试用户](#create-askspoke-test-user)** - 在 askSpoke 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“askSpoke”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.askspoke.com/saml/callback` 

    > [!NOTE]
    > 答复 URL 值不是真实值。 请使用实际回复 URL 更新此值。 请联系 [askSpoke 客户端支持团队](mailto:support@askspoke.com)以获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://askspoke.com/login`

1. askSpoke 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，askSpoke 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 |  源属性|
    | ---------------| --------- |
    | firstName | user.givenname |
    | lastName | user.surname |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 askSpoke”部分中，根据要求复制相应 URL。

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

在本部分中，将通过授予 B.Simon 访问 askSpoke 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“askSpoke”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-askspoke-sso"></a>配置 askSpoke SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 askSpoke 网站。

1. 在左侧导航窗格中单击“设置”选项卡。

    ![askSpoke 设置选项卡](./media/askspoke-tutorial/configure-1.png)

1. 向下滚动到“SSO”并单击“连接” 。

    ![askSpoke 连接](./media/askspoke-tutorial/configure-2.png)

1. 在“启用 SAML 和 SCIM”部分中，执行以下步骤：

    ![askSpoke 启用 SAML 和 SCIM 部分](./media/askspoke-tutorial/configure-3.png)

    1. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    1. 在“颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 。

    1. 在记事本中打开从 Azure 门户下载的“证书(Base64)”文件，复制证书文件的内容，并将其粘贴到“公共证书”文本框中 。

    1. 复制“ACS URL”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中。  。

    1. 单击“测试 SAML 连接”。

### <a name="create-askspoke-test-user"></a>创建 askSpoke 测试用户

本部分将在 askSpoke 中创建一个名为 B.Simon 的用户。 askSpoke 支持默认启用的实时预配。 此部分不存在任何操作项。 尝试访问 askSpoke 时，如果 askSpoke 中尚不存在用户，则系统会创建一个新用户。

askSpoke 还支持自动用户预配；有关如何配置自动用户预配的更多详细信息，请参阅[此处](./askspoke-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这将重定向到 askSpoke 登录 URL，你可以从那里启动登录流。  

* 直接转到 askSpoke 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 askSpoke。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 askSpoke 磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 askSpoke。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 askSpoke 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。