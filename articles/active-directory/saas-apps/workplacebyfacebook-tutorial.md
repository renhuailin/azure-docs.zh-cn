---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Workplace by Facebook 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workplace by Facebook 间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: ceb5200f716a00d0c8599ab596b29c727dff486e
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232463"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Workplace by Facebook 集成

本教程介绍如何将 Workplace by Facebook 与 Azure Active Directory (Azure AD) 集成。 将 Workplace by Facebook 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Workplace by Facebook。
* 让用户使用其 Azure AD 帐户自动登录到 Workplace by Facebook。
* 在一个中心位置（Azure 门户）管理帐户。


## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Workplace by Facebook 单一登录 (SSO) 的订阅。

> [!NOTE]
> Facebook 有两种产品：Workplace Standard（免费）和 Workplace Premium（付费）。 任何 Workplace Premium 租户都可以配置 SCIM 和 SSO 集成，对成本或所需的许可证没有其他影响。 SSO 和 SCIM 在 Workplace Standard 实例中不可用。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Workplace by Facebook 支持 SP 发起的 SSO。
* Workplace by Facebook 支持实时预配。
* Workplace by Facebook 支持[自动用户预配](workplacebyfacebook-provisioning-tutorial.md)。
* 现在可以为 Workplace by Facebook 移动应用程序配置 Azure AD 以启用 SSO。 本教程在测试环境中配置并测试 Azure AD SSO。


## <a name="adding-workplace-by-facebook-from-the-gallery"></a>从库添加 Workplace by Facebook

要配置 Workplace by Facebook 与 Azure AD 的集成，需要从库中将 Facebook 的 Workplace 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Workplace by Facebook**。
1. 在结果面板中选择“Workplace by Facebook”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>配置和测试 Workplace by Facebook 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 Workplace by Facebook 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Workplace by Facebook 中的相关用户之间建立链接关系。

若要使用 Workplace by Facebook 配置并测试 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. **[配置 Workplace by Facebook SSO](#configure-workplace-by-facebook-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Workplace by Facebook 测试用户](#create-workplace-by-facebook-test-user)** - 在 Workplace by Facebook 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Workplace by Facebook”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”（在 WorkPlace 中是“收件人 URL”）文本框中，使用以下模式键入 URL：`https://.workplace.com/work/saml.php`

    b. 在“标识符(实体 ID)”（在 WorkPlace 中是“受众 URL”）文本框中，使用以下模式键入 URL：`https://www.workplace.com/company/`

    c. 在“回复 URL”（在 WorkPlace 中是“断言使用者服务”）文本框中，使用以下模式键入 URL：`https://.workplace.com/work/saml.php`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 有关 Workplace 社区的正确值，请参阅 Workplace 公司仪表板的“身份验证”页，本教程稍后将对此进行说明。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Workplace by Facebook”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 Workplace by Facebook 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Workplace by Facebook”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-workplace-by-facebook-sso"></a>配置 Workplace by Facebook SSO

1. 若要在 Workplace by Facebook 中自动执行配置，需要通过单击“安装扩展”来安装 **我的应用安全登录浏览器扩展**。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 Workplace by Facebook”将定向到 Workplace by Facebook 应用程序。 在此处，请提供管理员凭据以登录到 Workplace by Facebook。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-5。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 Workplace by Facebook，请打开新的 Web 浏览器窗口，以管理员身份登录 Workplace by Facebook 公司站点，并执行以下步骤：

    > [!NOTE]
    > 作为 SAML 身份验证过程的一部分，Workplace 可使用查询字符串（最大为 2.5 KB）将参数传递给 Azure AD。

1. 导航到“管理面板” > “安全” > “身份验证”选项卡。

    ![管理面板](./media/workplacebyfacebook-tutorial/security.png)

    a. 选中“单一登录(SSO)”选项。

    b. 选择 SSO 作为新用户的默认选项。
    
    c. 单击“+添加新的 SSO 提供程序”。
    > [!NOTE]
    > 确保也选中“密码登录”复选框。 管理员在执行证书滚动更新时可能需要使用此选项进行登录，以防止自己被锁定。

1. 在“单一登录 (SSO) 设置”弹出窗口中，执行以下步骤：

    ![“身份验证”标签页](./media/workplacebyfacebook-tutorial/single-sign-on-setup.png)

    a. 在 **SSO 提供程序的名称** 中，输入 SSO实例名称，如 Azureadsso。

    b. 在“SAML URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    c. 在“SAML 颁发者 URL 文本框”中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 。

    d. 打开从 Azure 门户下载到记事本中的“证书文件(Base64)”，将内容复制到剪贴板，然后粘贴到“SAML 证书”文本框。

    e. 复制实例的“受众 URL”，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符(实体 ID)”文本框中。

    f. 复制实例的“收件人 URL”，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“登录 URL”文本框中。

    g. 复制实例的“ACS (断言使用者服务) URL”，并将其粘贴到 Azure 门户中“基本 SAML 配置”部分的“回复 URL”文本框中。

    h. 滚动到该部分的底部，单击“测试 SSO”按钮。 此时会出现包含 Azure AD 登录页面的一个弹出窗口。 照常输入凭据进行身份验证。

    **疑难解答：** 确保从 Azure AD 返回的电子邮件地址与用于登录的 Workplace 帐户相同。

    i. 成功完成测试后，滚动到页面底部并单击“保存”按钮。

    j. 现在所有使用 Workplace 的用户都会在 Azure AD 登录页面上进行身份验证。

1. **SAML 注销重定向（可选）**  -

    可以选择配置一个 SAML 注销 URL，该 URL 可用于指向 Azure AD 的注销页面。 启用并配置此设置后，用户将不会再被定向到 Workplace 注销页面。 用户会转而被重定向到在“SAML 注销重定向”设置中添加的 URL。

### <a name="configuring-reauthentication-frequency"></a>配置重新进行身份验证的频率

可将 Workplace 配置为每天、每三天、每周、每两周、每个月提示进行 SAML 检查或从不进行检查。

> [!NOTE]
> 移动应用程序上 SAML 检查的最小值为每周。

还可使用此按钮强制重置所有用户的 SAML：立即要求对所有用户进行 SAML 身份验证。

### <a name="create-workplace-by-facebook-test-user"></a>创建 Workplace by Facebook 测试用户

在本部分，你将在 Workplace by Facebook 中创建名为 B.Simon 的用户。 Workplace by Facebook 支持默认启用的实时预配。

在此部分中无需进行任何操作。 尝试访问 Workplace by Facebook 时，如果 Workplace by Facebook 中尚不存在用户，则系统会创建一个新用户。

>[!Note]
>如需手动创建用户，请联系 [Workplace by Facebook 客户端支持团队](https://www.workplace.com/help/work/)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Workplace by Facebook 登录 URL，可在其中启动登录流。 

* 直接转到 Workplace by Facebook 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Workplace by Facebook”磁贴时，这将重定向到 Workplace by Facebook 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>测试 Workplace by Facebook 的 SSO（移动）

1. 打开 Workplace by Facebook 移动应用程序。 在登录页上，单击“登录”。

    ![登录](./media/workplacebyfacebook-tutorial/test05.png)

2. 输入你的业务电子邮件，然后单击“继续”。

    ![电子邮件](./media/workplacebyfacebook-tutorial/test02.png)

3. 单击“仅一次”。

    ![一次](./media/workplacebyfacebook-tutorial/test04.png)

4. 单击“允许”。

    ![允许](./media/workplacebyfacebook-tutorial/test03.png)

5. 最后，在登录成功后，将显示应用程序主页。    

    ![主页](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="next-steps"></a>后续步骤

配置 Workplace by Facebook 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)