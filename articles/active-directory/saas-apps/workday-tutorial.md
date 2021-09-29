---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Workday 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workday 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: d3e027c8a6de4cdd73197909289a00659a5839e7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124745479"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Workday 集成

本教程介绍如何将 Workday 与 Azure Active Directory (Azure AD) 集成。 将 Workday 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Workday。
* 让用户使用其 Azure AD 帐户自动登录到 Workday。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 Workday 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Workday 支持 **SP** 发起的 SSO。

* 现在可以为 Workday 移动应用程序配置 Azure AD 以启用 SSO。 有关如何配置的详细信息，请访问[此链接](workday-mobile-tutorial.md)。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-workday-from-the-gallery"></a>从库中添加 Workday

若要配置 Workday 与 Azure AD 的集成，需要从库中将 Workday 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Workday”。
1. 从结果面板中选择“Workday”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>配置并测试 Workday 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 Workday 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Workday 相关用户之间建立链接关系。

若要配置并测试 Workday 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
2. **[配置 Workday](#configure-workday)**，以在应用程序端配置 SSO 设置。
    1. **[创建 Workday 测试用户](#create-workday-test-user)** - 在 Workday 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Workday 应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，输入以下字段的值  ：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://impl.workday.com/<tenant>/login-saml2.flex`。

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://impl.workday.com/<tenant>/login-saml.htmld`

    c. 在“注销 URL”文本框中，使用以下模式键入 URL：`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、回复 URL 和注销 URL 来更新这些值。 回复 URL 必须具有一个子域（例如：www、wd2、wd3、wd3-impl、wd5 和 wd5-impl）。
    > 可以使用类似于 `http://www.myworkday.com` 的内容，但不能使用 `http://myworkday.com`。 请联系 [Workday 客户端支持团队](https://www.workday.com/en-us/partners-services/services/support.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Workday 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 Workday 应用程序要求将 **nameidentifier** 与 **user.mail**、**UPN** 等进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射。

    ![屏幕截图显示“用户属性”，并且已选择“编辑”图标。](common/edit-attribute.png)

    > [!NOTE]
    > 此处我们已将名称 ID 与 UPN (user.userprincipalname) 映射为默认值。 需要在 Workday 帐户（电子邮件、UPN 等）中将名称 ID 与实际用户 ID 进行映射，以便成功运行 SSO。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

   ![证书下载链接](common/metadataxml.png)

1. 若要根据要求修改“签名”选项，请单击“编辑”按钮，打开“SAML 签名证书”对话框  。

    ![证书](common/edit-certificate.png) 

    ![SAML 签名证书](./media/workday-tutorial/signing-option.png)

    a. 选择“签名 SAML 响应和断言”作为“签名选项”。

    b. 单击“保存” 

1. 在“设置 Workday”部分，根据要求复制相应的 URL。

   ![复制配置 URL](common/copy-configuration-urls.png)

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

在本部分中，将通过授予 B.Simon 访问 Workday 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Workday”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-workday&quot;></a>配置 Workday

1. 在另一 Web 浏览器窗口中，以管理员身份登录 Workday 公司站点。

1. 在主页左上方的“搜索框”中使用名称“编辑租户设置 - 安全性”进行搜索 。

    ![编辑租户安全](./media/workday-tutorial/search-box.png &quot;编辑租户安全")


1. 在“SAML 设置”部分，单击“导入标识提供者” 。

    ![SAML 设置](./media/workday-tutorial/saml-setup.png "SAML 设置")

1. 在“导入标识提供者”部分，执行以下步骤：

    ![导入标识提供者](./media/workday-tutorial/import-identity-provider.png)

    a. 在文本框中提供“标识提供者名称”，例如 `AzureAD`。

    b. 在“用于环境”文本框中，从下拉列表中选择适当的环境名称。

    c. 单击“选择文件”以上传下载的“联合元数据 XML”文件 。

    d. 单击“确定”，然后单击“完成” 。

1. 单击“完成”后，将在“SAML 身份提供者”中添加一个新行，然后可以为新创建的行添加以下步骤 。

    ![SAML 标识提供者。](./media/workday-tutorial/saml-identity-providers.png "SAML 标识提供者")

    a. 单击“启用 IDP 发起的注销”复选框。

    b. 在“注销响应 URL”文本框中，键入 http://www.workday.com 。

    c. 单击“启用 Workday 发起的注销”复选框。

    d. 在“注销请求 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值 。

    e. 单击“SP 发起”复选框。

    f. 在“服务提供商 ID”文本框中，键入 **http://www.workday.com**。

    g 选择“不削弱 SP 发起的身份验证请求”。

1. 在下图中执行以下步骤。

    ![Workday](./media/workday-tutorial/service-provider.png "SAML 标识提供者")

    a. 在“服务提供商 ID(即将弃用)”文本框中，键入 http://www.workday.com 。

    b. 在“IDP SSO 服务 URL (即将弃用)”文本框中，键入“登录 URL”值 。

    c. 选择“不削弱 SP 发起的身份验证请求(即将弃用)”。

    d. 对于“身份验证请求签名方法”，选择“SHA256” 。

    e. 单击 **“确定”** 。

    > [!NOTE]
    > 请确保正确设置单一登录。 如果使用不正确的设置启用单一登录，则可能无法使用凭据进入应用程序并被锁定。在这种情况下，Workday 提供备份登录 URL，用户可以使用以下格式的普通用户名和密码登录：[Workday URL]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>创建 Workday 测试用户

1. 以管理员身份登录 Workday 公司站点。

1. 单击右上角的“个人资料”，选择“主页”，然后在“应用程序”选项卡中单击“目录”   。 

1. 在“目录”页面中，在视图选项卡中选择“查找辅助角色” 。

    ![查找辅助角色](./media/workday-tutorial/user-directory.png)

1.  在“查找辅助角色”页面上，从结果中选择用户。

1. 在下一页中，选择“作业”>“辅助角色安全性”，“Workday 帐户”作为“名称 ID”值必须与 Azure Active Directory 匹配  。

    ![辅助角色安全性](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> 有关如何创建 Workday 测试用户的详细信息，请联系 [Workday 客户端支持团队](https://www.workday.com/en-us/partners-services/services/support.html)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Workday 登录 URL，可在其中启动登录流。 

* 直接转到 Workday 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Workday”磁贴时，你应会自动登录到为其设置了 SSO 的 Workday。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Workday 后，即可强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制扩展自条件访问。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)