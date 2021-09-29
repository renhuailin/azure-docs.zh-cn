---
title: 教程：Azure Active Directory 与 TOPdesk - Public 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 与 TOPdesk - Public 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 6809e744c2b694f800fc2e542494c9b627098090
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124821273"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>教程：Azure Active Directory 与 TOPdesk - Public 集成

本教程介绍如何将 TOPdesk - Public 与 Azure Active Directory (Azure AD) 集成。 将 TOPdesk - Public 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 TOPdesk - Public。
* 让用户使用其 Azure AD 帐户自动登录到 TOPdesk - Public。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 TOPdesk - Public 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* TOPdesk - Public 支持 SP 发起的 SSO。

## <a name="add-topdesk---public-from-the-gallery"></a>从库添加 TOPdesk - Public

要配置 TOPdesk - Public 与 Azure AD 的集成，需要从库中将 TOPdesk - Public 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“TOPdesk - Public” 。
1. 从结果面板中选择“TOPdesk - Public”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>配置并测试 TOPdesk - Public 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 TOPdesk - Public 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 TOPdesk - Public 中的相关用户之间建立链接关系。

若要配置并测试 TOPdesk - Public 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 TOPdesk - Public SSO](#configure-topdesk---public-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 TOPdesk - Public 测试用户](#create-topdesk---public-test-user)** - 在 TOPdesk - Public 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 TOPdesk - Public 应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4.  在“基本 SAML 配置”部分，如果有 **服务提供程序元数据文件**，请执行以下步骤：

    >[!NOTE]
    >从本教程下文中将介绍的“配置 TOPdesk - Public 单一登录”部分中获取 **服务提供商元数据文件**。

    a. 单击“上传元数据文件”  。
    
    ![上传元数据文件](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![选择元数据文件](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“基本 SAML 配置”部分。  

    d. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<companyname>.topdesk.net`。

    e. 在“标识符 URL”文本框中，填写可从 TOPdesk 配置中检索的 TOPdesk 元数据 URL。 它应使用以下模式：`https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > 如果“标识符”和“回复 URL”值未自动填充，则需要手动输入它们。 对于标识符，请遵循上面提到的模式，你可从本教程下文中将介绍的“配置 TOPdesk - Public 单一登录”部分获取回复 URL。 “登录 URL”值不是真实值，因此你需要将该值更新为实际登录 URL。 请联系 [TOPdesk - Public 客户端支持团队](https://my.topdesk.com/)获取该值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 TOPdesk - Public”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

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

本部分将通过授予 B.Simon 访问 TOPdesk - Public 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“TOPdesk - Public”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-topdesk---public-sso&quot;></a>配置 TOPdesk - Public SSO

1. 以管理员身份登录到你的 **TOPdesk（公共版）** 公司站点。

2. 在 **“TOPdesk”** 菜单中，单击 **“设置”**。
   
    ![设置](./media/topdesk-public-tutorial/menu.png &quot;设置")

3. 单击 **“登录设置”**。
   
    ![登录设置](./media/topdesk-public-tutorial/login.png "登录设置")

4. 展开 **“登录设置”** 菜单，然后单击 **“常规”**。
   
    ![常规设置](./media/topdesk-public-tutorial/general.png "常规设置")

5. 在 **“SAML 登录”** 配置部分的 **“公共”** 部分中，执行以下步骤：
   
    ![技术设置](./media/topdesk-public-tutorial/public.png "技术设置")
   
    a. 单击 **“下载”** 以下载公共元数据文件，然后将其本地保存到计算机上。
   
    b. 打开下载的元数据文件，找到 **AssertionConsumerService** 节点。

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    c. 复制 **AssertionConsumerService** 值，并将此值粘贴到“基本 SAML 配置”部分中的“回复 URL”文本框内。      
   
6. 若要创建证书文件，请执行以下步骤：
    
    ![证书](./media/topdesk-public-tutorial/certificate-file.png "证书")
    
    a. 在 Azure 门户中打开下载的元数据文件。
    
    b. 展开 **xsi:type** 为 **fed:ApplicationServiceType** 的 **RoleDescriptor** 节点。
    
    c. 复制 **X509Certificate** 节点的值。
    
    d. 将复制的 **X509Certificate** 值本地保存到计算机文件中。

7. 在 **“公共”** 部分中，单击 **“添加”**。
    
    ![SAML 登录](./media/topdesk-public-tutorial/add.png "SAML 登录")

8. 在 **“SAML 配置助手”** 对话框页上，执行以下步骤：
    
    ![SAML 配置助手](./media/topdesk-public-tutorial/configuration.png "SAML 配置助手")
    
    a. 若要从 Azure 门户上传已下载的元数据文件，请在“联合元数据”下单击“浏览”。

    b. 若要上载你的证书文件，请在 **“证书 (RSA)”** 下单击 **“浏览”**。

    c. 若要上载从 TOPdesk 支持团队获得的徽标文件，请在 **“徽标图标”** 下单击 **“浏览”**。

    d. 在“用户名属性”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    e. 在 **“显示名”** 文本框中，键入你的配置名称。

    f. 单击“保存”  。

### <a name="create-topdesk---public-test-user"></a>创建 TOPdesk - Public 测试用户

要使 Azure AD 用户能够登录 TOPdesk - Public，必须将这些用户预配到 TOPdesk - Public 中。 对于 TOPdesk - Public，预配是一项手动任务。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：

1. 以管理员身份登录 **TOPdesk - Public** 公司站点。

2. 在顶部菜单中，单击“TOPdesk”\>“新建”\>“支持文件”\>“人员”。
   
    ![Person](./media/topdesk-public-tutorial/files.png "人员")

3. 在“新建用户”对话框中，执行以下步骤：
   
    ![新建人员](./media/topdesk-public-tutorial/new.png "新建人员")
   
    a. 单击“常规”选项卡。

    b. 在“姓氏”文本框中，键入用户的姓氏（如 Simon）
 
    c. 为帐户选择 **“站点”**。
 
    d. 单击“ **保存**”。

> [!NOTE]
> 可以使用任何其他 TOPdesk - Public 用户帐户创建工具或 TOPdesk - Public 提供的 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 TOPdesk - Public 登录 URL，你可以从此处启动登录流。 

* 直接转到 TOPdesk - Public 登录 URL，并从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“TOPdesk - Public”磁贴时，会重定向到 TOPdesk - Public 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 TOPdesk - Public 后，就可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。