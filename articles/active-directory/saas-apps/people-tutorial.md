---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 People 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 People 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: e8ff577be6299b65d6923cf26578c42ea35610e2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785964"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>教程：Azure Active Directory 单一登录 (SSO) 与 People 集成

本教程介绍如何将 People 与 Azure Active Directory (Azure AD) 集成。 将 People 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 People。
* 让用户使用其 Azure AD 帐户自动登录到 People。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 People 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* People 支持 SP 发起的 SSO 
* 现在可以为 People 移动应用程序配置 Azure AD 以启用 SSO。 本教程在测试环境中配置并测试 Azure AD SSO。

>[!NOTE]
>此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-people-from-the-gallery"></a>从库中添加 People

要通过配置将 People 集成到 Azure AD 中，需从库将 People 添加到托管式 SaaS 应用的列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“People”   。
1. 从结果面板中选择“People”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-people"></a>配置并测试 People 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 People 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 People 相关用户之间建立链接关系。

若要配置并测试 People 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. **[配置 People SSO](#configure-people-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 People 测试用户](#create-people-test-user)** - 在 People 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“People”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<company name>.peoplehr.net`。

    b. 在“标识符”框中键入 URL `https://www.peoplehr.com`：

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的登录 URL 和回复 URL 更新这些值。 请联系 [People 客户端支持团队](mailto:customerservices@peoplehr.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 People”部分中，根据要求复制相应 URL  。

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

在本部分中，将通过授予 B.Simon 访问 People 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“People”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-people-sso"></a>配置 People SSO

1. 若要在 People 中自动执行配置，需要通过单击“安装扩展”  来安装 **我的应用安全登录浏览器扩展**。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 People”，此时会将你定向到 People 应用程序  。 在此处，提供管理员凭据以登录到 People。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 People，请打开新的 Web 浏览器窗口，以管理员身份登录 People 公司站点，并执行以下步骤：
   
4. 在左侧菜单中，单击“设置”。 

    ![屏幕截图显示选择了“设置”的左侧菜单。](./media/people-tutorial/settings.png)

5. 单击“公司”。 

    ![屏幕截图显示从“设置”菜单选择了“公司”。](./media/people-tutorial/company.png)

6. 在“上传‘单一登录’SAML 元数据文件”中，单击“浏览”以上传已下载的元数据文件   。

    ![配置单一登录](./media/people-tutorial/xml.png)

### <a name="create-people-test-user"></a>创建 People 测试用户

在本部分中，将在 People 中创建名为 B.Simon 的用户。 请与 [People 客户端支持团队](mailto:customerservices@peoplehr.com)协作，在 People 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 People 登录 URL，可从那里启动登录流。 

* 直接转到 People 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 People 磁贴时，会重定向到 People 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="test-sso-for-people-mobile"></a>测试 People（移动）的 SSO

1. 打开 People 移动应用程序。 在登录页上，输入 **电子邮件 ID**，然后单击“单一登录”  。

    ![登录](./media/people-tutorial/test01.png)

2. 输入 **组织用户 ID**，然后单击“下一步”  。

    ![电子邮件](./media/people-tutorial/test02.png)

3. 最后登录成功后，将显示应用程序主页，如下所示：

    ![一次](./media/people-tutorial/test03.png)

## <a name="next-steps"></a>后续步骤

配置 People 后，可以强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。