---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Cisco Webex Meetings 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Cisco Webex Meetings 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: 0682673b6c18d2ac1694814b2b030297db7bf8db
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124826633"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Cisco Webex Meetings 集成

本教程介绍如何将 Cisco Webex Meetings 与 Azure Active Directory (Azure AD) 集成。 将 Cisco Webex Meetings 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Cisco Webex Meetings。
* 让用户使用其 Azure AD 帐户自动登录到 Cisco Webex Meetings。
* 在一个中心位置（Azure 门户）管理帐户。


## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Cisco Webex Meetings 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Cisco Webex Meetings 支持 **SP 和 IDP** 发起的 SSO。
* Cisco Webex Meetings 支持[自动用户预配和取消预配](cisco-webex-provisioning-tutorial.md)（推荐）。
* Cisco Webex Meetings 支持 **适时** 用户预配。

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>从库中添加 Cisco Webex Meetings

若要配置 Cisco Webex Meetings 与 Azure AD 的集成，需要从库中将 Cisco Webex Meetings 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Cisco Webex Meetings”。
1. 从结果面板中选择“Cisco Webex Meetings”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex-meetings"></a>配置并测试 Cisco Webex Meetings 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Cisco Webex Meetings 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Cisco Webex Meetings 相关用户之间建立链接关系。

若要配置并测试 Cisco Webex Meetings 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
   1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
   1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
   
1. **[配置 Cisco Webex Meetings SSO](#configure-cisco-webex-meetings-sso)** - 在应用程序端配置单一登录设置。
   * **[创建 Cisco Webex Meetings 测试用户](#create-cisco-webex-meetings-test-user)** - 在 Cisco Webex Meetings 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
    
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Cisco Webex Meetings”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，可以通过上传“服务提供程序元数据”文件，在 **IDP** 发起模式下配置应用程序，如下所示：
   1. 单击“上传元数据文件”  。
   1. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 
   1. 成功完成服务提供程序元数据文件的上传后，**标识符** 和 **回复 URL** 值将自动填充在“基本 SAML 配置”部分中。
   
      > [!Note]
      > 你将从“配置 Cisco Webex Meetings SSO”部分获取服务提供程序元数据文件，本教程稍后将对此进行说明。 

1. 如果要在“SP”发起的模式下配置应用程序，请执行以下步骤：  
   1. 在“基本 SAML 配置”部分中，单击编辑/笔形图标。

      ![编辑基本 SAML 配置](common/edit-urls.png)

   1. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<customername>.my.webex.com`

1. Cisco Webex Meetings 应用程序需要特定格式的 SAML 断言，因此，我们需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标以打开“用户属性”对话框  。

   ![图像](common/edit-attribute.png)

1. 除了上述属性外，Cisco Webex Meetings 应用程序还需要将另外几个属性在 SAML 响应中传回。 在“用户属性”对话框的“用户声明”部分执行以下步骤，以添加 SAML 令牌属性，如下表中所示： 

   | 名称 | 源属性|
   | ---------------|  --------- |
   |   firstname    | user.givenname |
   |   姓    | user.surname |
   |   电子邮件       | user.mail |
   |   uid    | user.mail |

   1. 单击“添加新声明”  以打开“管理用户声明”  对话框。
   1. 在“名称”文本框中，键入为该行显示的属性名称。 
   1. 将“命名空间”留空  。
   1. 选择“源”作为“属性”  。
   1. 在“源属性”列表中，从下拉列表中选择为该行显示的属性值。
   1. 单击“ **保存**”。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

   ![证书下载链接](common/metadataxml.png)

1. 在“设置 Cisco Webex Meetings”部分中，根据要求复制相应的 URL。

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

在本部分中，通过授予 B.Simon 访问 Cisco Webex Meetings 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中，选择“Cisco Webex Meetings”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-cisco-webex-meetings-sso"></a>配置 Cisco Webex Meetings SSO

1. 使用管理员凭据登录到 Cisco Webex Meetings。
1. 转到“常用站点设置”并导航到“SSO 配置”。

   ![屏幕截图显示已选择“常用站点设置”和“SSO 配置”的 Cisco Webex 管理。](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

1. 在“Webex 管理”页上，执行以下步骤：

   ![屏幕截图显示“Webex 管理”页以及此步骤中所述的信息。](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

   1. 选择 **SAML 2.0** 作为 **联合身份验证协议**。
   1. 单击“导入 SAML 元数据”链接，以上传从 Azure 门户下载的元数据文件。
   1. 选择“IDP 发起”作为“SSO 配置文件”，然后单击“导出”按钮以下载服务提供商元数据文件，并在 Azure 门户中的“基本 SAML 配置”部分上传该文件。   
   1. 在“AuthContextClassRef”文本框中键入以下值之一：
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`
    
      若要使用 Azure AD 启用 MFA，请输入如下所示的两个值：`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

   1. 选择“自动创建帐户”。
   
      > [!NOTE]
      > 若要启用 **实时** 用户预配，需要选中“自动创建帐户”。 除此之外，还需要在 SAML 响应中传递 SAML 令牌属性。

   1. 单击“ **保存**”。

      > [!NOTE]
      > 此配置仅适用于使用电子邮件格式的 Webex UserID 的客户。
      > 
      > 若要详细了解如何配置 Cisco Webex Meetings，请参阅 [Webex 文档](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog)页。

### <a name="create-cisco-webex-meetings-test-user"></a>创建 Cisco Webex Meetings 测试用户

本部分的目的是在 Cisco Webex Meetings 中创建名为 B.Simon 的用户。 Cisco Webex Meetings 支持默认已启用的 **实时** 预配。 此部分不存在任何操作项。 尝试访问 Cisco Webex Meetings 时，如果 Cisco Webex Meetings 中尚不存在用户，则系统会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Cisco Webex Meetings 登录 URL，可从中启动登录流。  

* 直接转到 Cisco Webex Meetings 登录 URL，并从中启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”，然后你应会自动登录到为其设置了 SSO 的 Cisco Webex Meetings。

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“Cisco Webex Meetings”磁贴时，如果该应用程序是在 SP 模式下配置的，则你会重定向到应用程序登录页来启动登录流；如果它是在 IDP 模式下配置的，则你应会自动登录到为其设置了 SSO 的 Cisco Webex Meetings。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 Cisco Webex Meetings 后，可以强制实施会话控制，实时防止组织的敏感数据遭到外泄和渗透。 会话控制扩展自条件访问。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)