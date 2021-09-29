---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 OfficeSpace Software 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 OfficeSpace Software 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: jeedes
ms.openlocfilehash: 236de12c84e185969eb25c7488bb184f50e84098
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124808573"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>教程：Azure Active Directory 单一登录 (SSO) 与 OfficeSpace Software 集成

在本教程中，了解如何将 OfficeSpace Software 与 Azure Active Directory (Azure AD) 集成。 将 OfficeSpace Software 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 OfficeSpace Software。
* 让用户使用其 Azure AD 帐户自动登录到 OfficeSpace Software。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 OfficeSpace Software 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* OfficeSpace Software 支持 SP 发起的 SSO。
* OfficeSpace Software 支持[自动用户预配和取消预配](officespace-software-provisioning-tutorial.md)（推荐）。
* OfficeSpace Software 支持实时用户预配。

## <a name="add-officespace-software-from-the-gallery"></a>从库中添加 OfficeSpace Software

要配置 OfficeSpace Software 与 Azure AD 的集成，需要从库中将 OfficeSpace Software 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“OfficeSpace Software”   。
1. 从结果面板中选择“OfficeSpace Software”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-officespace-software"></a>配置并测试 OfficeSpace Software 的 Azure AD SSO

使用名为 B. Simon 的测试用户配置和测试 OfficeSpace Software 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 OfficeSpace Software 相关用户之间建立链接关系。

若要配置并测试 OfficeSpace Software 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 OfficeSpace Software SSO](#configure-officespace-software-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 OfficeSpace Software 测试用户](#create-officespace-software-test-user)** - 在 OfficeSpace Software 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“OfficeSpace Software”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company name>.officespacesoftware.com/users/sign_in/saml` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`<company name>.officespacesoftware.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [OfficeSpace Software 客户端支持团队](mailto:support@officespacesoftware.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. OfficeSpace Software 应用程序预期特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 OfficeSpace Software 应用程序要求通过 user.mail 对 nameidentifier 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射    。

    ![image](common/edit-attribute.png)

1. 除了上述属性，OfficeSpace Software 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ---------------| --------------- |
    | 电子邮件 | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

1. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

1. 在“SAML 签名证书”部分，复制“指纹值”并将其保存在计算机上。  

    ![复制指纹值](common/copy-thumbprint.png)

1. 在“设置 OfficeSpace Software”部分中，根据需要复制相应的 URL  。

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

在本部分中，通过授予 B.Simon 访问 OfficeSpace Software 的权限，以支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“OfficeSpace Software”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-officespace-software-sso"></a>配置 OfficeSpace Software SSO

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 OfficeSpace Software 租户。

2. 转到“设置”  ，并单击“连接器”  。

    ![屏幕截图显示选择了“连接器”的“设置”下拉列表。](./media/officespace-tutorial/settings.png)

3. 单击“SAML 身份验证”  。

    ![屏幕截图显示选择了“SAML 身份验证”操作的“身份验证”部分。](./media/officespace-tutorial/authentication.png)

4. 在“SAML 身份验证”  部分中，执行以下步骤：

    ![在应用端配置单一登录](./media/officespace-tutorial/configuration.png)

    a. 在“注销提供程序 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    b. 在“客户端 idp 目标 url”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    c. 在“客户端 IDP 证书指纹”文本框中，粘贴从 Azure 门户复制的“指纹”值。   

    d. 单击“保存设置”。 

### <a name="create-officespace-software-test-user"></a>创建 OfficeSpace Software 测试用户

在本部分，我们会在 OfficeSpace Software 中创建一个名为 B.Simon 的用户。 OfficeSpace Software 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 OfficeSpace Software 中尚不存在用户，身份验证后会创建一个新用户。

> [!NOTE]
> 如果需要手动创建用户，则需联系 [OfficeSpace Software 支持团队](mailto:support@officespacesoftware.com)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 OfficeSpace Software 登录 URL，你可以从那里启动登录流。 

* 直接转到 OfficeSpace Software 登录 URL，从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 OfficeSpace Software 磁贴时，将会重定向到 OfficeSpace Software 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 OfficeSpace Software 后，可以强制实施会话控制，实时防止组织的敏感数据遭受外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。