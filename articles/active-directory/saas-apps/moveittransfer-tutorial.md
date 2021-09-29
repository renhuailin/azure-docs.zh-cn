---
title: 教程：将 Azure Active Directory 与 MOVEit Transfer - Azure AD 集成集成 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 和 MOVEit Transfer - Azure AD 集成之间的单一登录。
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
ms.openlocfilehash: 0db5d434e2442019fc441b5443c50df16bc4559e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742240"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>教程：将 Azure Active Directory 与 MOVEit Transfer - Azure AD 集成集成

此教程介绍如何将 MOVEit Transfer - Azure AD 集成与 Azure Active Directory (Azure AD) 集成。 将 MOVEit Transfer - Azure AD 集成与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 MOVEit Transfer - Azure AD 集成。
* 让用户使用其 Azure AD 帐户自动登录到 MOVEit Transfer - Azure AD 集成。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 MOVEit Transfer - Azure AD 集成单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* MOVEit Transfer - Azure AD integration 支持 **SP** 发起的 SSO

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>从库中添加 MOVEit Transfer - Azure AD 集成

要配置 MOVEit Transfer - Azure AD 集成与 Azure AD 的集成，需要从库中将 MOVEit Transfer - Azure AD 集成添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“MOVEit Transfer - Azure AD 集成” 。
1. 在结果面板中选择“MOVEit Transfer - Azure AD 集成”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>配置并测试 MOVEit Transfer - Azure AD 集成的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 MOVEit Transfer - Azure AD 集成的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 MOVEit Transfer - Azure AD 集成相关用户之间建立关联。

若要配置并测试 MOVEit Transfer - Azure AD 集成的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 MOVEit Transfer - Azure AD 集成 SSO](#configure-moveit-transfer---azure-ad-integration-sso)** - 在应用程序端配置单一登录。
    1. **[创建 MOVEit Transfer - Azure AD 集成测试用户](#create-moveit-transfer---azure-ad-integration-test-user)** - 在 MOVEit Transfer - Azure AD 集成中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“MOVEit Transfer - Azure AD 集成”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分，如果有 **服务提供程序元数据文件**，请执行以下步骤：

    a. 单击“上传元数据文件”  。

    ![上传元数据文件](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![选择元数据文件](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“基本 SAML 配置”部分：

    ![MOVEit Transfer - Azure AD integration 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    在“登录 URL”文本框中，键入 URL：`https://contoso.com`

    > [!NOTE]
    > “登录 URL”值不是实际值。 请使用实际登录 URL 更新此值。 请联系 [MOVEit Transfer - Azure AD integration 客户端支持团队](https://community.ipswitch.com/s/support)获取这些值。 可以从“服务提供程序元数据 URL”下载“服务提供程序元数据文件”，本教程稍后的“配置 MOVEit Transfer - Azure AD integration 单一登录”部分将介绍此操作。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 MOVEit Transfer - Azure AD integration”部分，根据要求复制相应的 URL。

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

本部分将通过授予 B.Simon 访问 MOVEit Transfer - Azure AD 集成的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“MOVEit Transfer - Azure AD 集成”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>配置 MOVEit Transfer - Azure AD 集成 SSO

1. 以管理员身份登录到 MOVEit Transfer 租户。

2. 在左侧导航窗格上，单击“设置”。 

    ![应用端上的“设置”部分](./media/moveittransfer-tutorial/settings.png)

3. 单击位于“安全策略”->“用户身份验证”下的“单一登录”链接。 

    ![应用端的安全策略](./media/moveittransfer-tutorial/sso.png)

4. 单击元数据 URL 链接以下载元数据文档。

    ![服务提供程序元数据 URL](./media/moveittransfer-tutorial/metadata.png)
    
   * 验证“entityID”是否与“基本 SAML 配置”部分的“标识符”相匹配。
   * 验证“AssertionConsumerService”位置 URL 是否与“基本 SAML 配置”部分的“回复 URL”相匹配。
    
     ![在应用端配置单一登录](./media/moveittransfer-tutorial/xml.png)

5. 单击“添加标识提供程序”按钮以添加新的联合身份提供程序。

    ![添加标识提供者](./media/moveittransfer-tutorial/idp.png)

6. 单击“浏览...”以选择从 Azure 门户中下载的元数据文件，并单击“添加标识提供者”以上传所下载的文件。

    ![SAML 标识提供程序](./media/moveittransfer-tutorial/saml.png)

7. 在“编辑联合身份提供程序设置...”页上，对于“启用”选择“是”，并单击“保存”。

    ![联合标识提供程序设置](./media/moveittransfer-tutorial/save.png)

8. 在“编辑联合标识提供者用户设置”页上，执行以下操作：
    
    ![编辑联合标识提供程序设置](./media/moveittransfer-tutorial/attributes.png)
    
    a. 对于“登录名”，选择“SAML NameID”。
    
    b. 选择“其他”作为“全名”并在“属性名称”文本框中输入以下值：`http://schemas.microsoft.com/identity/claims/displayname`。
    
    c. 选择“其他”作为“电子邮件”并在“属性名称”文本框中输入以下值：`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。
    
    d. 对于“登录时自动创建帐户”，选择“是”。
    
    e. 单击“保存”按钮  。

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>创建 MOVEit Transfer - Azure AD integration 测试用户

本部分的目的是在 MOVEit Transfer - Azure AD 集成中创建名为 Britta Simon 的用户。 MOVEit Transfer - Azure AD 集成支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 尝试访问 MOVEit Transfer - Azure AD 集成期间，如果尚不存在用户，则会创建一个新用户。

>[!NOTE]
>如果需要手动创建用户，则需联系 [MOVEit Transfer - Azure AD 集成客户端支持团队](https://community.ipswitch.com/s/support)。

### <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 MOVEit Transfer - Azure AD 集成登录 URL，可以从那里启动登录流。 

* 直接转到 MOVEit Transfer - Azure AD 集成登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在我的应用中单击“MOVEit Transfer - Azure AD 集成”磁贴时，应会自动登录到设置了 SSO 的 MOVEit Transfer - Azure AD 集成。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 MOVEit Transfer - Azure AD 集成后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。