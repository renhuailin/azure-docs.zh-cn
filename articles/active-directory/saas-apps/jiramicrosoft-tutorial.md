---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 JIRA SAML SSO by Microsoft 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 JIRA SAML SSO by Microsoft 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: d9b3bf52e6483577b85787372c77623517a5f9cb
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730093"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>教程：Azure Active Directory 单一登录 (SSO) 与 JIRA SAML SSO by Microsoft 集成

本教程介绍如何将 JIRA SAML SSO by Microsoft 与 Azure Active Directory (Azure AD) 集成。 将 JIRA SAML SSO by Microsoft 与 Azure AD 集成时，你可以：

* 在 Azure AD 中控制谁有权访问 JIRA SAML SSO by Microsoft。
* 让用户使用其 Azure AD 帐户自动登录 JIRA SAML SSO by Microsoft。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="description"></a>说明

在 Atlassian JIRA 服务器上使用 Microsoft Azure Active Directory 帐户启用单一登录。 这样，所有组织用户便可使用 Azure AD 凭据登录到 JIRA 应用程序。 此插件使用 SAML 2.0 进行联合身份验证。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 JIRA SAML SSO by Microsoft 的集成，需要以下项：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
- 应在 Windows 64 位版本上安装并配置 JIRA Core 和 Software 6.4 到 8.17.1，或 JIRA Service Desk 3.0 到 4.16.1
- JIRA 服务器已启用 HTTPS
- 请注意，下面部分列出了支持 JIRA 插件版本。
- JIRA 服务器应当可以访问 Internet，尤其是访问用于身份验证的 Azure AD 登录页，还应当可以接收来自 Azure AD 的令牌
- 在 JIRA 中 设置管理员凭据
- 在 JIRA 中禁用 WebSudo
- 在 JIRA 服务器应用程序中创建的测试用户

> [!NOTE]
> 不建议使用 JIRA 生产环境测试本教程中的步骤。 首先在应用程序的开发环境或过渡环境中测试集成，然后使用生产环境。

若要开始操作，需备齐以下项目：

* 除非必要，请勿使用生产环境。
* 启用了 JIRA SAML SSO by Microsoft 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="supported-versions-of-jira"></a>支持的 JIRA 版本

* JIRA Core 和 Software：6.4 到 8.17.1
* JIRA Service Desk 3.0 到 4.16.1
* JIRA 还支持 5.2。 有关更多详细信息，请单击 [Microsoft Azure Active Directory single sign-on for JIRA 5.2](jira52microsoft-tutorial.md)

> [!NOTE]
> 请注意，JIRA 插件还适用于 Ubuntu 版本 16.04 和 Linux。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* JIRA SAML SSO by Microsoft 支持 **SP** 发起的 SSO

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>从库添加 JIRA SAML SSO by Microsoft

若要配置 JIRA SAML SSO by Microsoft 与 Azure AD 的集成，需要从库中将 JIRA SAML SSO by Microsoft 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“JIRA SAML SSO by Microsoft”   。
1. 从结果面板中选择“JIRA SAML SSO by Microsoft”，然后添加应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft"></a>配置和测试 JIRA SAML SSO by Microsoft 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 JIRA SAML SSO by Microsoft 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 JIRA SAML SSO by Microsoft 相关用户之间建立链接关系。

若要配置并测试 JIRA SAML SSO by Microsoft 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 JIRA SAML SSO by Microsoft SSO](#configure-jira-saml-sso-by-microsoft-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 JIRA SAML SSO by Microsoft 测试用户](#create-jira-saml-sso-by-microsoft-test-user)** - 在 JIRA SAML SSO by Microsoft 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“JIRA SAML SSO by Microsoft”应用程序集成页上，找到“管理”部分，然后选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<domain:port>/plugins/servlet/saml/auth`。

    b. 在“标识符”框中，使用以下模式键入 URL：`https://<domain:port>/`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 端口可选，以防止其为命名 URL。 在配置 JIRA 插件的过程中，将接收这些值，这将在教程的后面部分进行说明。

1. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分中，通过授予 B.Simon 访问 JIRA SAML SSO by Microsoft 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“JIRA SAML SSO by Microsoft”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>配置 JIRA SAML SSO by Microsoft SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 JIRA 实例。

2. 将鼠标悬停在小齿轮上，并单击“外接程序”  。

    ![屏幕截图显示“设置”菜单中处于选中状态的“加载项”。](./media/jiramicrosoft-tutorial/addon1.png)

3. 从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=56506)下载插件。 使用“上传加载项”菜单手动上传由 Microsoft 提供的插件。 [Microsoft 服务协议](https://www.microsoft.com/servicesagreement/)涵盖了插件下载。

    ![屏幕截图显示“管理加载项”，其中标注了“上传加载项”链接。](./media/jiramicrosoft-tutorial/addon12.png)

4. 要运行 JIRA 反向代理方案或负载均衡器方案，请执行以下步骤：

    > [!NOTE]
    > 应先按照以下说明配置服务器，然后安装插件。

    a. 在 JIRA 服务器应用程序的 **server.xml** 文件中的 **连接器** 端口中添加以下属性。

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![屏幕截图显示编辑器中添加了新行的 server.xml 文件。](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. 根据代理/负载均衡器，在 **系统设置** 中更改 **基本 URL**。

    ![屏幕截图显示“管理设置”，可在其中更改“基本 URL”。](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. 插件安装后，它会显示在“管理加载项”部分的“用户已安装”加载项部分 。 单击“配置”  配置新的插件。

    ![屏幕截图显示“Jira 的 Azure AD SAML 单一登录”部分，其中“配置”处于选中状态。](./media/jiramicrosoft-tutorial/addon14.png)

6. 在配置页上执行下列步骤：

    ![屏幕截图显示“Jira 的 Microsoft Azure Active Directory 单一登录”配置页。](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > 请确保一个应用仅映射一个证书，以免在解析元数据时出错。 如果有多个证书，则管理员会在解析元数据时收到错误。

    a. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的 **应用联合元数据 URL** 值，然后单击“解析”按钮。 它将读取 IdP 元数据 URL，并填充所有字段信息。

    b. 复制“标识符”、“回 URL”和“登录 URL”值，并将其分别粘贴到 Azure 门户中，“JIRA SAML SSO by Microsoft 域和 URL”部分下的“标识符、回复 URL 和登录 URL”文本框内    。

    c. 在“登录按钮名”中键入组织希望用户在登录屏幕上看到的按钮名称  。
    
    d. 在 **登录按钮说明** 中，键入你的组织希望用户在登录屏幕上看到的按钮的说明。

    e. 在“SAML 用户 ID 位置”中，选择“用户 ID 位于 Subject 语句的 NameIdentifier 元素之中”或“用户 ID 位于 Attribute 元素之中”    。  此 ID 必须为 JIRA 用户 ID。 如果用户 ID 不匹配，系统将不允许用户登录。

    > [!Note]
    > 默认 SAML 用户 ID 位置是名称标识符。 可将其更改为属性选项，并输入适当的属性名称。

    f. 如果选择“用户 ID 位于属性元素之中”选项，则请在“属性名称”文本框内键入应该出现用户 ID 的属性名称 。

    g. 如果正在使用 Azure AD 的联合域（如 ADFS 等），请单击“启用主领域发现”选项，并配置“域名” 。

    h. 如果是基于 ADFS 的登录，请在“域名”中键入域名。

    i. 当用户从 JIRA 注销时，如果要从 Azure AD 注销，请选择“启用单一注销”  。
    
    j. 如果希望仅通过 Azure AD 凭据登录，请选中“强制 Azure 登录”  复选框。
    
    > [!Note]
    > 若要在启用“强制 Azure 登录”时在登录页上启用管理员登录的默认登录表单，请在浏览器 URL 中添加查询参数。
    > `https://<domain:port>/login.jsp?force_azure_login=false`

    k. 单击“保存”按钮保存设置。

    > [!NOTE]
    > 有关安装和故障排除的详细信息，请访问 [MS JIRA SSO 连接器管理员指南](./ms-confluence-jira-plugin-adminguide.md)， 还可以参阅[常见问题解答](./ms-confluence-jira-plugin-adminguide.md)以获得帮助。

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>创建 JIRA SAML SSO by Microsoft 测试用户

为了使 Azure AD 用户能够登录 JIRA 本地服务器，必须将其预配到 JIRA SAML SSO by Microsoft。 对于 JIRA SAML SSO by Microsoft，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 JIRA 本地服务器。

2. 将鼠标悬停在小齿轮上，并单击“用户管理”  。

    ![屏幕截图显示“设置”菜单中处于选中状态的“用户管理”。](./media/jiramicrosoft-tutorial/user1.png)

3. 重定向到“管理员访问权限”页后，输入密码，  并单击“确认”按钮。

    ![屏幕截图显示“管理员访问权限”页，可在其中输入凭据。](./media/jiramicrosoft-tutorial/user2.png)

4. 在“用户管理”  选项卡部分，单击“创建用户”  。

    ![屏幕截图显示“用户管理”选项卡，可在其中“创建用户”。](./media/jiramicrosoft-tutorial/user3.png) 

5. 在“新建用户”对话框页中，执行以下步骤：

    ![屏幕截图显示“新建用户”对话框，可在其中输入此步骤中的信息。](./media/jiramicrosoft-tutorial/user4.png) 

    a. 在“电子邮件地址”文本框中，键入用户的电子邮件地址（例如 B.simon@contoso.com）。

    b. 在“全名”文本框中，键入用户（例如 B.Simon）的全名。

    c. 在“用户名”文本框中，键入用户的电子邮件地址（例如 B.simon@contoso.com）。

    d. 在“密码”文本框中，键入用户的密码。

    e. 单击“创建用户”  。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 JIRA SAML SSO by Microsoft 登录 URL，可以从那里启动登录流。 

* 直接转到 JIRA SAML SSO by Microsoft 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 当单击“我的应用”中 JIRA SAML SSO by Microsoft 磁贴时，这将通过 Microsoft 登录 URL 重定向到 JIRA SAML SSO。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 JIRA SAML SSO by Microsoft 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)
