---
title: 教程：Azure Active Directory 与 Meraki Dashboard 的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Meraki Dashboard 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/28/2021
ms.author: jeedes
ms.openlocfilehash: 5adf1f5c2c811bdc2b48e03c1e2892746d36da4b
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402866"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>教程：Azure Active Directory 与 Meraki Dashboard 的单一登录 (SSO) 集成

本教程介绍如何将 Meraki Dashboard 与 Azure Active Directory (Azure AD) 集成。 将 Meraki Dashboard 与 Azure AD 集成后，可以：

- 在 Azure AD 中控制谁有权访问 Meraki Dashboard。
- 让用户使用其 Azure AD 帐户自动登录到 Meraki Dashboard。
- 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
- 启用了单一登录 (SSO) 的 Meraki Dashboard 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

- Meraki Dashboard 支持 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-meraki-dashboard-from-the-gallery"></a>从库中添加 Meraki Dashboard

若要配置 Meraki Dashboard 与 Azure AD 的集成，需要从库中将 Meraki Dashboard 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中键入“Meraki Dashboard”  。 
1. 从结果面板中选择“Meraki Dashboard”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>配置并测试 Meraki Dashboard 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Meraki Dashboard 的 Azure AD SSO  。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Meraki Dashboard 中的相关用户之间建立链接关系。

若要配置并测试 Meraki Dashboard 的 Azure AD SSO，请执行以下步骤：若要配置并测试 Mevisio 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
   1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
   1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Meraki Dashboard SSO](#configure-meraki-dashboard-sso)  - 在应用程序端配置单一登录设置。
   1. [创建 Meraki Dashboard 管理员角色](#create-meraki-dashboard-admin-roles) - 在 Meraki Dashboard 中创建 B.Simon 的对应用户，并将其链接到该用户在 Azure AD 中对应的身份。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Meraki Dashboard”应用程序集成页面上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

   在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

   > [!NOTE]
   > 答复 URL 值不是真实值。 使用实际的回复 URL 值更新该值（本教程稍后会介绍）。

1. 单击“保存”按钮  。

1. Meraki Dashboard 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

   ![图像](common/default-attributes.png)

1. 除上述属性以外，Meraki Dashboard 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

   | 名称                                                    | 源属性       |
   | ------------------------------------------------------- | ---------------------- |
   | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
   | `https://dashboard.meraki.com/saml/attributes/role`     | user.assignedroles     |

   > [!NOTE]
   > 若要了解如何在 Azure AD 中配置角色，请参阅[此文](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)。

1. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

   ![编辑 SAML 签名证书](common/edit-certificate.png)

1. 在“SAML 签名证书”部分，复制“指纹值”并将其保存在计算机上。   需要将此值转换为包含冒号，Meraki Dashboard 才能理解它。 例如，如果 Azure 中的指纹为 `C2569F50A4AAEDBB8E`，则需要将其更改为 `C2:56:9F:50:A4:AA:ED:BB:8E`，以便稍后在 Meraki Dashboard 中使用。

   ![复制指纹值](common/copy-thumbprint.png)

1. 在“设置 Meraki Dashboard”  部分中，复制“注销 URL”值并将其保存在计算机上。

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

在本部分中，你将通过授予 B.Simon 访问 Meraki Dashboard 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Meraki Dashboard”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。

   ![用户角色](./media/meraki-dashboard-tutorial/user-role.png)

   > [!NOTE]
   > 将禁用“选择角色”选项，所选用户的默认角色为“用户”。

1. 在“添加分配”对话框中，单击“分配”按钮。 

## <a name="configure-meraki-dashboard-sso"></a>配置 Meraki Dashboard SSO

1. 若要在 Meraki Dashboard 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录”浏览器扩展 。

   ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Meraki Dashboard”会将你定向到 Meraki Dashboard 应用程序。 在此处，请提供管理员凭据以登录到 Meraki Dashboard。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

   ![设置配置](common/setup-sso.png)

3. 若要手动设置 Meraki Dashboard，请在另一个 Web 浏览器窗口中，以管理员身份登录到 Meraki Dashboard 公司站点。

4. 导航到“组织”   ->   “设置”。

   ![Meraki Dashboard“设置”选项卡](./media/meraki-dashboard-tutorial/configure-1.png)

5. 在“身份验证”下，将“SAML SSO”更改为“启用 SAML SSO”。  

   ![Meraki Dashboard“身份验证”](./media/meraki-dashboard-tutorial/configure-2.png)

6. 单击“添加 SAML IdP”  。

   ![Meraki Dashboard“添加 SAML IdP”](./media/meraki-dashboard-tutorial/configure-3.png)

7. 将转换后的指纹值粘贴到“X.590 证书 SHA1 指纹”文本框中，这些值从 Azure 门户复制并已转换为前面部分的步骤 9 中所述的指定格式 。 然后单击“保存”  。 保存后，会显示“使用者 URL”。 复制“使用者 URL”值，将其粘贴到 Azure 门户的“基本 SAML 配置”部分的“回复 URL”文本框中。  

   ![Meraki Dashboard 配置](./media/meraki-dashboard-tutorial/configure-4.png)

### <a name="create-meraki-dashboard-admin-roles"></a>创建 Meraki Dashboard 管理员角色

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Meraki Dashboard。

1. 导航到“组织”   ->   “管理员”。

   ![Meraki Dashboard 管理员](./media/meraki-dashboard-tutorial/user-1.png)

1. 在“SAML 管理员角色”部分中，单击“添加 SAML 角色”按钮。 

   ![Meraki Dashboard“添加 SAML 角色”按钮](./media/meraki-dashboard-tutorial/user-2.png)

1. 输入角色“meraki_full_admin”，将“组织访问权限”标记为“完全”，然后单击“创建角色”。     对 meraki_readonly_admin 重复此过程，这一次将“组织访问权限”标记为“只读”    。

   ![Meraki Dashboard 创建用户](./media/meraki-dashboard-tutorial/user-3.png)

1. 按照以下步骤将 Meraki Dashboard 角色映射到 Azure AD SAML 角色：

   ![应用角色的屏幕截图。](./media/meraki-dashboard-tutorial/app-role.png)

   a. 在 Azure 门户中，单击“应用注册”。

   b. 选择“所有应用程序”，然后单击“Meraki Dashboard”。

   c. 单击“应用角色”，然后单击“创建应用角色”。

   d. 输入“`Meraki Full Admin`”作为“显示名称”。
   
   e. 选择“`Users/Groups`”作为“允许的成员”。

   f. 输入“`meraki_full_admin`”作为“值”。

   g. 输入“`Meraki Full Admin`”作为“说明”。

   h. 单击“ **保存**”。 

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

- 在 Azure 门户中单击“测试此应用程序”后，应该会自动登录到为其设置了 SSO 的 Meraki Dashboard

- 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Meraki Dashboard 磁贴时，应会自动登录到为其设置了 SSO 的 Meraki Dashboard。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Meraki Dashboard 后，可以强制实施会话控制，实时阻止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。