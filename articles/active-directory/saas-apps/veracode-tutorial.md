---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Veracode 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Veracode 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 38f822ad8c88f38a1e6b5b5dce4d3d51a629eee7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124825358"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Veracode 集成

本教程介绍如何将 Veracode 与 Azure Active Directory (Azure AD) 集成。 将 Veracode 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Veracode。
* 让用户使用其 Azure AD 帐户自动登录到 Veracode。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Veracode 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 Veracode 支持由标识提供者发起的 SSO 和实时用户预配。

## <a name="add-veracode-from-the-gallery"></a>从库中添加 Veracode

若要配置 Veracode 与 Azure AD 的集成，请从库中将 Veracode 添加到托管 SaaS 应用程序列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Veracode”。
1. 从结果面板中选择“Veracode”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>配置并测试 Veracode 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Veracode 的 Azure AD SSO。 若要运行 SSO，必须在 Azure AD 用户与 Veracode 中相关用户之间建立链接。

若要配置并测试 Veracode 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
1. **[配置 Veracode SSO](#configure-veracode-sso)** 以在应用程序端配置单一登录设置。
    * **[创建 Veracode 测试用户](#create-veracode-test-user)** 以在 Veracode 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Veracode 应用程序集成页上，找到“管理”部分 。 选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在基本 SAML 配置部分，应用程序进行了预配置，且已通过 Azure 预填充了必要的 URL。 选择“保存”。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，找到“证书(Base64)”  。 选择“下载”以下载证书，并将其保存在计算机上。 

    ![“SAML 签名证书”部分的屏幕截图，其中突出显示了“下载”链接](common/certificatebase64.png)

1. Veracode 需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![“用户属性和声明”部分的屏幕截图](common/default-attributes.png)

1. Veracode 还要求在 SAML 响应中传回其他几个属性。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ---------------| --------------- |
    | 名 |User.givenname |
    | 姓 |User.surname |
    | 电子邮件 |User.mail |

1. 在“设置 Veracode”部分中，根据要求复制相应的 URL。

    ![“设置 Veracode”部分的屏幕截图，其中突出显示了配置 URL](common/copy-configuration-urls.png)

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

在本部分中，将通过授予 B.Simon 访问 Veracode 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Veracode”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-veracode-sso&quot;></a>配置 Veracode SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Veracode 公司站点。

1. 在顶部菜单中，选择“设置” > “管理员” 。
   
    ![“Veracode 管理”的屏幕截图，突出显示了“设置”图标和“管理员”](./media/veracode-tutorial/admin.png &quot;管理")

1. 选择“SAML”选项卡。

1. 在“组织 SAML 设置”部分中执行以下步骤：

    ![“组织 SAML 设置”部分的屏幕截图](./media/veracode-tutorial/saml.png "管理")

    a.  对于“证书颁发者”，粘贴从 Azure 门户复制的“Azure AD 标识符”值。

    b. 对于“断言签名证书”，请选择“选择文件”从 Azure 门户上传已下载的证书。

    c. 对于“自动注册”，选择“启用自动注册”。

1. 在“自助注册设置”部分，请执行以下步骤，并选择“保存”：

    ![“自动注册设置”部分的屏幕截图，突出显示了各种选项](./media/veracode-tutorial/save.png "管理")

    a. 对于“新用户激活”，选择“不需要激活”。

    b. 对于“用户数据更新”，选择“首选项 Veracode 用户数据”。

    c. 对于“SAML 属性的详细信息”，选择以下选项：
      * **用户角色**
      * **“策略管理员”**
      * “审阅者”
      * “安全主管”
      * **主管**
      * “提交者”
      * **创建者**
      * “所有扫描类型”
      * “团队成员身份”
      * “默认团队”

### <a name="create-veracode-test-user"></a>创建 Veracode 测试用户

本部分将在 Veracode 中创建一个名为 B.Simon 的用户。 Veracode 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Veracode 中尚不存在用户，则会在进行身份验证后创建一个新用户。

> [!NOTE]
> 可以使用 Veracode 提供的任何其他 Veracode 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Veracode。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Veracode 磁贴时，应当会自动登录到为其设置了 SSO 的 Veracode。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Veracode 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。