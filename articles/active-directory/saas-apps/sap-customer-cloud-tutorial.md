---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 SAP Cloud for Customer 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAP Cloud for Customer 之间配置单一登录。
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
ms.openlocfilehash: ad84982ddb20e56741212502a1aa3e1fccc9a9ae
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746296"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>教程：Azure Active Directory 单一登录 (SSO) 与 SAP Cloud for Customer 的集成

在本教程中，了解如何将 SAP Cloud for Customer 与 Azure Active Directory (Azure AD) 集成。 将 SAP Cloud for Customer 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SAP Cloud for Customer。
* 让用户使用其 Azure AD 帐户自动登录到 SAP Cloud for Customer。
* 在一个中心位置（Azure 门户）管理帐户。


## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 SAP Cloud for Customer 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SAP Cloud for Customer支持 SP 发起的 SSO 

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>从库中添加 SAP Cloud for Customer

要配置 SAP Cloud for Customer 与 Azure AD 的集成，需要从库中将 SAP Cloud for Customer 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **SAP Cloud for Customer**。 
1. 从结果面板中选择“SAP Cloud for Customer”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-for-customer"></a>配置和测试 SAP Cloud for Customer 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 SAP Cloud for Customer 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 SAP Cloud for Customer 中的相关用户之间建立链接关系。

若要配置并测试 SAP Cloud for Customer 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 SAP Cloud for Customer SSO](#configure-sap-cloud-for-customer-sso)** - 在应用程序端配置单一登录。
    1. **[创建 SAP Cloud for Customer 测试用户](#create-sap-cloud-for-customer-test-user)** - 在 SAP Cloud for Customer 中创建 B.Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“SAP Cloud for Customer”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<server name>.crm.ondemand.com` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [SAP Cloud for Customer 客户端支持团队](https://www.sap.com/about/agreements.sap-cloud-services-customers.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. SAP Cloud for Customer 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标以打开“用户属性”对话框  。

    ![显示“用户属性”对话框的屏幕截图，其中选择了“编辑”图标。](common/edit-attribute.png)

1. 在“用户属性和声明”对话框中的“用户属性”部分，执行以下步骤   ：

    a. 单击“编辑图标”，打开“管理用户声明”对话框   。

    ![屏幕截图显示了“用户属性和声明”，其中选择了“编辑”图标。](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. 选择“转换”作为“源”   。

    c. 从“转换”列表中，选择“ExtractMailPrefix()”   。

    d. 从“参数 1”列表中，选择要用于实现的用户属性  。
    例如，如果想要使用 EmployeeID 作为唯一用户标识符并且已在 ExtensionAttribute2 中存储属性值，则选择 user.extensionattribute2。

    e. 单击“ **保存**”。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 SAP Cloud for Customer”部分，根据要求复制相应的 URL  。

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

在本部分中，通过授予 B.Simon 访问 SAP Cloud for Customer 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“SAP Cloud for Customer”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-sap-cloud-for-customer-sso"></a>配置 SAP Cloud for Customer SSO

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 SAP Cloud for Customer 公司站点。

2. 在菜单左侧单击“标识提供者” > “企业标识提供者 > ”“添加”，在弹出窗口中添加标识提供者名称（例如 Azure AD），单击“保存”，然后单击“SAML 2.0 配置”     。

    ![屏幕截图显示了“标识提供者”页，其中突出显示了“添加标识提供者”对话框文本框，并选择了“保存”按钮。](./media/sap-customer-cloud-tutorial/configure01.png)

3. 在“SAML 2.0 配置”  部分，执行以下步骤：

    ![屏幕截图显示了“SAML 2.0 配置”，其中选择了“浏览”按钮。](./media/sap-customer-cloud-tutorial/configure02.png)

    a. 单击“浏览”，上传从 Azure 门户下载的联合身份验证元数据 XML 文件  。

    b. 成功上传 XML 文件后，系统会自动填充以下值，然后即可单击“保存”。 

### <a name="create-sap-cloud-for-customer-test-user"></a>创建 SAP Cloud for Customer 测试用户

为了使 Azure AD 用户能够登录到 SAP Cloud for Customer，必须将其预配到 SAP Cloud for Customer 中。 在SAP Cloud for Customer 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录 SAP Cloud for Customer。

2. 从菜单的左侧，单击“用户和授权” > “用户管理” > 添加用户”  。

    ![屏幕截图显示了“用户管理”页，其中选择了“添加用户”按钮。](./media/sap-customer-cloud-tutorial/configure03.png)

3. 在“添加新用户”  部分，执行以下步骤：

    ![SAP 配置](./media/sap-customer-cloud-tutorial/configure04.png)

    a. 在“名字”文本框中，输入用户的名字，例如 **B**。 

    b. 在“姓氏”文本框中，输入用户的姓氏，例如 Simon   。

    c. 在“电子邮件”文本框中输入用户的电子邮件，例如 `B.Simon@contoso.com`。 

    d. 在“登录名”文本框中，输入用户的名称，例如 B.Simon   。

    e. 根据要求选择“用户类型”。 

    f. 根据要求选择“帐户激活”选项。 

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 SAP Cloud for Customer 登录 URL，可以从那里启动登录流。 

* 直接转到 SAP Cloud for Customer 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 当单击“我的应用”中的 SAP Cloud for Customer 磁贴时，这将重定向到 SAP Cloud for Customer 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 SAP Cloud for Customer 后，即可强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。