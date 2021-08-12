---
title: 教程：Azure Active Directory 与 LearnUpon 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 LearnUpon 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2021
ms.author: jeedes
ms.openlocfilehash: 84da4c02ec4631e9d9f33ac745c3721894cc5f29
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469471"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>教程：Azure Active Directory 与 LearnUpon 的集成

本教程介绍如何将 LearnUpon 与 Azure Active Directory (Azure AD) 相集成。 将 LearnUpon 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 LearnUpon。
* 让用户使用其 Azure AD 帐户自动登录到 LearnUpon。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 LearnUpon 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 LearnUpon 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* LearnUpon 支持 IDP 发起的 SSO。

* LearnUpon 支持即时用户预配。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-learnupon-from-the-gallery"></a>从库中添加 LearnUpon

要配置 LearnUpon 与 Azure AD 的集成，需要从库中将 LearnUpon 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 LearnUpon 。
1. 在结果面板中选择“LearnUpon”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-learnupon"></a>配置并测试 LearnUpon 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 LearnUpon 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 LearnUpon 中的相关用户之间建立链接关系。

若要配置并测试 LearnUpon 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 LearnUpon SSO](#configure-learnupon-sso) - 在应用程序端配置单一登录设置。
    1. [创建 LearnUpon 测试用户](#create-learnupon-test-user) - 在 LearnUpon 中创建 B.Simon 的对应用户，并将其链接到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“LearnUpon”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > 此值不是真实值。 请使用实际回复 URL 更新此值。 若要获取此值，请与 [LearnUpon 客户端支持团队](https://www.learnupon.com/contact/)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页上找到“指纹”   - 这将添加到 LearnUpon SAML 设置。

    ![证书下载链接](common/certificateraw.png)

6. 在“设置 LearnUpon”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 LearnUpon 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“LearnUpon”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-learnupon-sso"></a>配置 LearnUpon SSO

1. 打开另一个浏览器实例并使用管理员帐户登录到 LearnUpon。

1. 单击“设置”选项卡。 

    ![屏幕截图显示了“设置”选项卡。](./media/learnupon-tutorial/settings.png)

1. 单击“单一登录 - SAML”，并单击“常规设置”以配置 SAML 设置。  
   
    ![屏幕截图显示选择了“单一登录 - SAML”，其中选择了“常规设置”。](./media/learnupon-tutorial/general-settings.png) 

1. 在“常规设置”  部分中，执行以下步骤：
   
    ![屏幕截图显示了“常规设置”部分，你可以在其中输入所述值。](./media/learnupon-tutorial/values.png)  
  
    a. 选择“启用”。 

    b. 将“版本”  选择为“2.0”  。

    c. 将“跳过条件”  选择为“否”  。

    d. 在“SAML 令牌 Post 参数名称”文本框中，键入上面指定的 SAML 使用者 URL 的请求 post 参数的名称（例如 **SAMLResponse**），该参数包含要验证和进行身份验证的 SAML 断言。

    e. 在“名称标识符格式”  文本框中，键入相应的值以指明用户标识符（电子邮件）在 SAML 断言中所处的位置，例如 `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`。
  
    f. 在“标识符提供程序位置”  文本框中，键入相应的值，指明当用户在 Azure 门户登录屏幕上单击你已上传的图标时要将用户发送到何处。
  
    g. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    h. 单击“管理指纹”，并上载所下载的证书的指纹。 

1. 单击“用户设置”  ，并执行以下步骤：

     ![屏幕截图显示了“用户设置”部分，你可以在其中输入所述值。](./media/learnupon-tutorial/user-settings.png)  

    a. 在“名字标识符格式”文本框中  ，键入相应的值，指明用户名字在 SAML 断言中所处的位置，例如：`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。
  
    b. 在“姓氏标识符格式”  文本框中，键入相应的值，指明用户姓氏在 SAML 断言中所处的位置，例如：`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`。

### <a name="create-learnupon-test-user"></a>创建 LearnUpon 测试用户

在本部分中，我们会在 LearnUpon 中创建一个名为 Britta Simon 的用户。 LearnUpon 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 LearnUpon 中尚不存在用户，身份验证后会创建一个新用户。 如果需要手动创建用户，则需联系 [LearnUpon 支持团队](https://www.learnupon.com/contact/)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”，然后你应会自动登录到为其设置了 SSO 的 LearnUpon。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“LearnUpon”磁贴时，应会自动登录到为其设置了 SSO 的 LearnUpon。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 LearnUpon 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
