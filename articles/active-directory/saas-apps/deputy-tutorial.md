---
title: 教程：Azure Active Directory 与 Deputy 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Deputy 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: b991ae23f96c0a3ce83c4dac60e3dc075899f018
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124783418"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>教程：Azure Active Directory 与 Deputy 的集成

本教程介绍如何将 Deputy 与 Azure Active Directory (Azure AD) 集成。 将 Deputy 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Deputy。
* 让用户使用其 Azure AD 帐户自动登录到 Deputy。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Deputy 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Deputy 支持 SP 和 IDP 发起的 SSO。
* Deputy 支持实时用户预配。

## <a name="add-deputy-from-the-gallery"></a>从库中添加 Deputy

要配置 Deputy 与 Azure AD 的集成，需要从库中将 Deputy 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Deputy” 。
1. 从结果面板中选择“Deputy”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-deputy"></a>配置并测试 Deputy 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Deputy 的 Azure AD SSO。 若要正常使用 SSO，你需要在 Azure AD 用户与 Deputy 相关用户之间建立链接关系。

若要配置并测试 Deputy 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Deputy SSO](#configure-deputy-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Deputy 测试用户](#create-deputy-test-user)** - 在 Deputy 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Deputy”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    a. 在“标识符”文本框中，使用以下模式之一键入 URL：

    ```http
    https://<subdomain>.<region>.au.deputy.com
    https://<subdomain>.<region>.ent-au.deputy.com
    https://<subdomain>.<region>.na.deputy.com
    https://<subdomain>.<region>.ent-na.deputy.com
    https://<subdomain>.<region>.eu.deputy.com
    https://<subdomain>.<region>.ent-eu.deputy.com
    https://<subdomain>.<region>.as.deputy.com
    https://<subdomain>.<region>.ent-as.deputy.com
    https://<subdomain>.<region>.la.deputy.com
    https://<subdomain>.<region>.ent-la.deputy.com
    https://<subdomain>.<region>.af.deputy.com
    https://<subdomain>.<region>.ent-af.deputy.com
    https://<subdomain>.<region>.an.deputy.com
    https://<subdomain>.<region>.ent-an.deputy.com
    https://<subdomain>.<region>.deputy.com
    ```

    b. 在“回复 URL”文本框中，使用以下模式之一键入 URL：
    
    ```http
    https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.
    https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs
    ```

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<your-subdomain>.<region>.deputy.com`

    >[!NOTE]
    > Deputy 区域后缀是可选的，或者应当使用下列项之一：au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Deputy 客户端支持团队](https://www.deputy.com/call-centers-customer-support-scheduling-software)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Deputy 应用程序需要特定格式的 SAML 断言，这就需要将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![图像](common/default-attributes.png)

1. 除了上述属性之外，Deputy 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 |  源属性|
    | -------------- | --------- |
    | 名字 | user.givenname|
    | 姓氏 | user.surname |

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 Deputy”部分，根据要求复制相应 URL  。

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

本部分中将通过授予 B.Simon 访问 Deputy 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Deputy”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-deputy-sso"></a>配置 Deputy SSO

1. 以管理员身份登录 Deputy 帐户。

1. 在右上角单击你的帐户，然后选择“业务设置”。

    ![业务设置的屏幕截图](./media/deputy-tutorial/business-settings.png)

1. 然后，在“常规”选项卡下，单击“单一登录设置” 。

    ![“单一登录设置”的屏幕截图](./media/deputy-tutorial/general.png)

2. 在此“单一登录”设置页上执行以下步骤。

    ![配置单一登录](./media/deputy-tutorial/configuration.png)
    
    a. 单击“启用单一登录”。
   
    b. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL” 。

    c. 在“标识提供者颁发者”文本框中，粘贴从 Azure 门户复制的“标识符(实体 ID)” 。

    d. 在记事本中打开从 Azure 门户下载的“证书(Base64)”，将内容粘贴到“X.509 证书”文本框中 。

    e. 如果要使用 SSO 登录，请启用“需要单一登录的登录名”。
   
    f. 启用“实时预配”，并在“名字”和“姓氏”字段中，提供在“用户属性和声明”部分中设置的属性的名称，例如 `First name` 和 `Last name`   。

    g. 单击“应用更改”。

### <a name="create-deputy-test-user"></a>创建 Deputy 测试用户

在本部分，我们将在 Deputy 中创建一个名为 Britta Simon 的用户。 Deputy 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Deputy 中尚不存在用户，身份验证后会创建一个新用户。

#### <a name="to-add-the-user-manually-perform-the-following-steps"></a>要手动添加用户，请执行以下步骤：

1. 以管理员身份登录到 Deputy 公司站点。

2. 在顶部的导航窗格中，单击“人员”。 

3. 单击“添加人员”按钮，并单击“添加单个人员” 。
   
    ![添加人员](./media/deputy-tutorial/create-user-1.png "添加人员")

4. 在“常规”选项卡中执行以下步骤以添加用户。
   
    ![新建用户](./media/deputy-tutorial/create-user-2.png "新建用户")

    a. 在“名字”和“姓氏”文本框中，填写字段，如“Britta”和“Simon”   。
   
    b. 在“工作单位”文本框中，键入公司名称  。
   
    c. 单击“保存”按钮。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 TeamzSkill 登录 URL，可在这里启动登录流。  

* 直接转到 TeamzSkill 登录 URL，从此处启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 TeamzSkill 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 TeamzSkill 磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 TeamzSkill。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 Deputy 后，你可以强制实施会话控制，实时保护组织的敏感数据，避免其外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。