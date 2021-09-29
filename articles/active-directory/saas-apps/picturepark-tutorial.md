---
title: 教程：Azure Active Directory 与 Picturepark 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Picturepark 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/01/2021
ms.author: jeedes
ms.openlocfilehash: 92bc5dd433c5cdc32b2598a35aadcce6be7b1c9c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827998"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>教程：Azure Active Directory 与 Picturepark 集成

本教程介绍如何将 Picturepark 与 Azure Active Directory (Azure AD) 集成。 将 Picturepark 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Picturepark。
* 让用户使用其 Azure AD 帐户自动登录到 Picturepark。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Picturepark 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录的 Picturepark 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Picturepark 支持 SP 发起的 SSO。

## <a name="add-picturepark-from-the-gallery"></a>从库中添加 Picturepark

若要配置 Picturepark 与 Azure AD 的集成，需要从库中将 Picturepark 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Picturepark”。
1. 在结果面板中选择“Picturepark”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-picturepark"></a>配置并测试 Picturepark 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Picturepark 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Picturepark 相关用户之间建立关联。

若要配置并测试 Picturepark 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Picturepark SSO](#configure-picturepark-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Picturepark 测试用户](#create-picturepark-test-user) - 在 Picturepark 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Picturepark”应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式之一键入 URL：

    | 标识符 URL |
    |---|
    |`https://<COMPANY_NAME>.current-picturepark.com`|
    |`https://<COMPANY_NAME>.picturepark.com`|
    |`https://<COMPANY_NAME>.next-picturepark.com`|
    |
    
    b. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<COMPANY_NAME>.picturepark.com` 

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符和登录 URL 更新这些值。 请联系 [Picturepark 客户端支持团队](https://picturepark.com/company/picturepark-customer-support)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

6. 在“SAML 签名证书”部分中，复制 **指纹** 并将其保存在计算机上。

    ![复制指纹值](common/copy-thumbprint.png)

7. 在“设置 Picturepark”部分，根据要求复制相应的 URL。 对于“登录 URL”，请使用模式如下的值：`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ 是 Azure AD 订阅的租户 ID。

    ![复制配置 URL](./media/picturepark-tutorial/configure.png)

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

在本部分，你将通过授予 B.Simon 访问 Picturepark 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Picturepark”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-picturepark-sso&quot;></a>配置 Picturepark SSO

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 Picturepark 公司站点。

2. 在顶部工具栏中，单击“管理工具”，然后单击“管理控制台”。
   
    ![管理控制台](./media/picturepark-tutorial/tools.png &quot;管理控制台")

3. 单击“身份验证”，然后单击“标识提供程序”。
   
    ![身份验证](./media/picturepark-tutorial/identity-provider.png "身份验证")

4. 在“标识提供程序配置”部分中执行以下步骤：
   
    ![标识提供者配置](./media/picturepark-tutorial/add-configuration.png "标识提供者配置")
   
    a. 单击“添加”。
  
    b. 键入配置名称。
   
    c. 选择“设为默认值”。
   
    d. 在“颁发者 URI”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。
   
    e. 在“受信任的证书颁发者指纹”文本框中，粘贴从“SAML 签名证书”部分复制的“指纹”值。 

5. 单击“JoinDefaultUsersGroup”。

6. 若要在“声明”文本框中设置“Emailaddress”属性，请键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` 并单击“保存”。

      ![配置](./media/picturepark-tutorial/claim.png "配置")

### <a name="create-picturepark-test-user"></a>创建 Picturepark 测试用户

要让 Azure AD 用户登录 Picturepark，必须将其预配到 Picturepark 中。 使用 Picturepark 时，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **Picturepark** 租户。

1. 在顶部工具栏中，单击“管理工具”，然后单击“用户”。
   
    ![用户](./media/picturepark-tutorial/user.png "用户")

1. 在“用户概况”选项卡中，单击“新建”。
   
    ![用户管理](./media/picturepark-tutorial/new-user.png "用户管理")

1. 在“创建用户”对话框中，执行以下预配有效的 Azure Active Directory 用户所需的步骤：
   
    ![创建用户](./media/picturepark-tutorial/details.png "创建用户")
   
    a. 在“电子邮件地址”文本框中，键入用户 `BrittaSimon@contoso.com` 的 **电子邮件地址** 。  
   
    b. 在“密码”和“确认密码”文本框中，键入密码（“BrittaSimon”）。 
   
    c. 在“名字”文本框中，键入用户的“名字”（“Britta”）  。 
   
    d. 在“姓氏”文本框中，键入用户的“姓氏”（“Simon”）  。
   
    e. 在“公司”文本框中，键入用户的公司名称。 
   
    f. 在“国家/地区”文本框中，选择用户的 **国家/地区**。
  
    g. 在“邮政编码”文本框中，键入城市的邮政编码。
   
    h. 在“市/县”文本框中，键入用户所在的市/县名称。

    i. 选择 **“语言”**。
   
    j. 单击“创建”。

>[!NOTE]
>可以使用任何其他 Picturepark 用户帐户创建工具或 Picturepark 提供的 API 来预配 Azure AD 用户帐户。
>

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Picturepark 登录 URL，你可以从那里启动登录流。 

* 直接转到 Picturepark 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Picturepark 磁贴时，会重定向到 Picturepark 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Picturepark 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。