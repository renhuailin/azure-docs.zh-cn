---
title: 教程：Azure Active Directory 与 Clear Review 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Clear Review 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2021
ms.author: jeedes
ms.openlocfilehash: b03b67cd5feb6a49e0b612b8ead6e1ce1c1f3176
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124817596"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>教程：Azure Active Directory 与 Clear Review 的集成

在本教程中，你将了解如何将 Clear Review 与 Azure Active Directory (Azure AD) 集成。 将 Clear Review 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 Clear Review。
* 让用户使用其 Azure AD 帐户自动登录到 Clear Review。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Clear Review 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Clear Review 单一登录的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Clear Review 支持 SP 和 IDP 发起的 SSO。

## <a name="add-clear-review-from-the-gallery"></a>从库添加 Clear Review

若要配置 Clear Review 与 Azure AD 的集成，需要将库中的 Clear Review 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Clear Review” 。
1. 从结果面板中选择“Clear Review”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-clear-review"></a>配置并测试 Clear Review 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Clear Review 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Clear Review 中的相关用户之间建立关联。

若要配置并测试 Clear Review 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Clear Review SSO](#configure-clear-review-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Clear Review 测试用户](#create-clear-review-test-user) - 在 Clear Review 中创建 B.Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Clear Review”应用程序集成页面上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<CUSTOMER_NAME>.clearreview.com/sso/metadata/`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<CUSTOMER_NAME>.clearreview.com/sso/acs/`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<CUSTOMER_NAME>.clearreview.com`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 若要获取这些值，请联系 [Clear Review 客户端支持团队](https://clearreview.com/contact/)。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

6. Clear Review 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 Clear Review 应用程序要求通过 user.mail 对 nameidentifier 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射  。

    ![屏幕截图显示“用户属性”，并且已选择“编辑”图标。](common/edit-attribute.png)

7. 在“用户属性和声明”对话框中，执行以下步骤：

    a. 单击“名称标识符值”右侧的“编辑”图标 。

    ![屏幕截图显示已选择“编辑”图标的“用户属性和声明”。](./media/clearreview-tutorial/attribute-2.png)

    ![屏幕截图显示“管理用户声明”对话框，可在其中输入所述的值。](./media/clearreview-tutorial/attribute-1.png)

    b. 在“源属性”列表中，选择该行的 user.mail 属性值 。

    c. 单击“保存”  。

8. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

9. 在“设置 Clear Review”部分，根据要求复制相应 URL。

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

在本部分，你将通过授予 B.Simon 访问 Clear Review 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Clear Review”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-clear-review-sso"></a>配置 Clear Review SSO

1. 若要在 **Clear Review** 端配置单一登录，请使用管理员凭据打开 **Clear Review** 门户。

2. 从左侧导航窗格中选择“管理”。

    ![屏幕截图显示已选择“管理”的 Clear Review 门户。](./media/clearreview-tutorial/admin.png)

3. 在页面底部的“集成”部分中，单击“单一登录设置”右侧的“更改”按钮  。

    ![屏幕截图显示“单一登录更改”按钮。](./media/clearreview-tutorial/integrations.png)

4. 在“单一登录设置”页面上，执行以下步骤。

    ![屏幕截图显示“单一登录设置”页，可在其中输入此步骤中的信息。](./media/clearreview-tutorial/settings.png)

    a. 在“颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 。

    b. 在“SAML 终结点”文本框中，粘贴从 Azure 门户复制的 **登录 URL** 值。  

    c. 在“SLO 终结点”文本框中，粘贴从 Azure 门户复制的 **注销 URL** 值。  

    d. 在记事本中打开下载的证书，将其内容粘贴到“X.509 证书”文本框中。   

    e. 单击“保存”  。

### <a name="create-clear-review-test-user"></a>创建 Clear Review 测试用户

在本部分中，在 Clear Review 中创建名为 Britta Simon 的用户。 请与 [Clear Review 支持团队](https://clearreview.com/contact/)协作，在 Clear Review 平台中添加用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Clear Review 登录 URL，你可在其中启动登录流。  

* 直接转到 Clear Review 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Clear Review。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“Clear Review”磁贴时，如果该应用程序是在 SP 模式下配置的，则你会重定向到应用程序登录页来启动登录流；如果它是在 IDP 模式下配置的，则你应会自动登录到为其设置了 SSO 的 Clear Review。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Clear Review 后，即可强制实施会话控制，从而实时保护组织的敏感数据以防外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。