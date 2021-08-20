---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Saba Cloud 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Saba Cloud 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2021
ms.author: jeedes
ms.openlocfilehash: 79f0f24d1328181557b2fec1e867c36ebcde57f0
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112453567"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Saba Cloud 的集成

本教程介绍如何将 Saba Cloud 与 Azure Active Directory (Azure AD) 相集成。 将 Saba Cloud 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Saba Cloud。
* 让用户使用其 Azure AD 帐户自动登录到 Saba Cloud。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Saba Cloud 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Saba Cloud 支持 **SP 和 IDP** 发起的 SSO。
* Saba Cloud 支持 **即时** 用户预配。
* 现在可以为 Saba Cloud 移动应用程序配置 Azure AD 以启用 SSO。 本教程在测试环境中配置并测试 Azure AD SSO。

## <a name="adding-saba-cloud-from-the-gallery"></a>从库中添加 Saba Cloud

若要配置 Saba Cloud 与 Azure AD 的集成，需要从库中将 Saba Cloud 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Saba Cloud**。
1. 在结果面板中选择“Saba Cloud”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>配置并测试 Saba Cloud 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 Saba Cloud 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Saba Cloud 中的相关用户之间建立链接关系。

若要配置并测试 Saba Cloud 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Saba Cloud SSO](#configure-saba-cloud-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Saba Cloud 测试用户](#create-saba-cloud-test-user)** - 在 Saba Cloud 中创建 B.Simon 的对应用户，并将其链接到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。
1. **[测试 Saba Cloud（移动版）的 SSO](#test-sso-for-saba-cloud-mobile)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Saba Cloud”应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”文本框中，键入采用以下模式（你将在步骤 6 的“配置 Saba Cloud SSO”部分获得该值，但它通常采取 `<CUSTOMER_NAME>_sp` 格式）的 URL：`<CUSTOMER_NAME>_sp`

    b. 在“回复 URL”文本框中，键入采用以下模式（ENTITY_ID 引用上一步，通常为 `<CUSTOMER_NAME>_sp`）的 URL：`https://<CUSTOMER_NAME>.sabacloud.com/Saba/saml/SSO/alias/<ENTITY_ID>`
    
    > [!NOTE]
    > 如果错误地指定了回复 URL，可能需要在 Azure AD 的“应用注册”部分（而不是“企业应用程序”部分）调整回复 URL 。 对“基本 SAML 配置”部分进行的更改并不会始终更新回复 URL。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<CUSTOMER_NAME>.sabacloud.com`。

    b. 在“中继状态”文本框中，使用以下模式键入 URL：`IDP_INIT---SAML_SSO_SITE=<SITE_ID> `。如果为微站点配置了 SAML，请使用以下模式键入 URL：`IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`

    > [!NOTE]
    > 这些不是实际值。 请使用“标识符”、“回复 URL”、“登录 URL”和“中继状态”更新这些值。 请联系 [Saba Cloud 客户端支持团队](mailto:support@saba.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。
    > 
    > 如需详细了解如何配置 RelayState，请参阅[适用于微站点的 IdP 和 SP 发起的 SSO](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html)。

1. 在“用户属性和声明”部分中，将唯一用户标识符调整为你的组织打算用作 Saba 用户的主要用户名的任何内容。

   只有在尝试从用户名/密码转换为 SSO 时，才需要执行此步骤。 如果这是没有现有用户的新 Saba Cloud 部署，则可以跳过此步骤。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Saba Cloud”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 Saba Cloud 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中，选择“Saba Cloud”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-saba-cloud-sso"></a>配置 Saba Cloud SSO

1. 若要在 Saba Cloud 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录浏览器扩展” 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 Saba Cloud”会将你定向到 Saba Cloud 应用程序。 在此处提供管理员凭据以登录到 Saba Cloud。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-9。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 Saba Cloud，请在另一个 Web 浏览器窗口中，以管理员身份登录到 Saba Cloud 公司站点。

1. 单击“菜单”图标，单击“管理员”，然后选择“系统管理员”选项卡。  

    ![“系统管理员”的屏幕截图](./media/saba-cloud-tutorial/system.png)

1. 在“配置系统”中选择“SAML SSO 设置”，然后单击“设置 SAML SSO”按钮。   

    ![“配置”的屏幕截图](./media/saba-cloud-tutorial/configure.png)

1. 在弹出窗口中，从下拉列表中选择“微站点”，然后单击“添加并配置”。 

    ![“添加站点”/“微站点”的屏幕截图](./media/saba-cloud-tutorial/microsite.png)

1. 在“配置 IDP”部分，单击“浏览”上传从 Azure 门户下载的“联合元数据 XML”文件。   启用“站点特定的 IDP”复选框，然后单击“导入”。 

    ![“证书导入”的屏幕截图](./media/saba-cloud-tutorial/certificate.png) 

1. 在“配置 SP”部分复制“实体别名”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符(实体 ID)”文本框中。    单击“生成”。

    ![“配置 SP”的屏幕截图](./media/saba-cloud-tutorial/generate-metadata.png) 

1. 在“配置属性”部分，确认填充的字段并单击“保存”。  

    ![“配置属性”的屏幕截图](./media/saba-cloud-tutorial/configure-properties.png) 
    
    可能需要将“最大身份验证期限(以秒为单位)”设置为“7776000”（90 天），以匹配默认 Azure AD 允许登录的最大滚动期限 。 否则，可能会导致错误 `(109) Login failed. Please contact system administrator.`

### <a name="create-saba-cloud-test-user"></a>创建 Saba Cloud 测试用户

在本部分，我们将在 Saba Cloud 中创建名为 Britta Simon 的用户。 Saba Cloud 支持默认已启用的即时用户预配。 此部分不存在任何操作项。 如果某个用户尚不存在于 Saba Cloud 中，会在对其进行身份验证后新建此用户。

> [!NOTE]
> 若要在 Saba Cloud 中启用 SAML 即时用户预配，请参阅[此文档](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Saba Cloud 登录 URL，可从中启动登录流。  

* 直接转到 Saba Cloud 登录 URL，并从中启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”，然后你应会自动登录到为其设置了 SSO 的 Saba Cloud。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“Saba Cloud”磁贴时，如果该应用程序是在 SP 模式下配置的，则你会重定向到应用程序登录页来启动登录流；如果它是在 IDP 模式下配置的，则你应会自动登录到为其设置了 SSO 的 Saba Cloud。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

> [!NOTE]
> 如果未在 Azure AD 中填充登录 URL，则会将应用程序视为 IDP 发起的模式；如果已填充登录 URL，则 Azure AD 始终会将用户重定向到 Saba Cloud 应用程序以执行服务提供商发起的流。

## <a name="test-sso-for-saba-cloud-mobile"></a>测试 Saba Cloud（移动版）的 SSO

1. 打开 Saba Cloud 移动应用程序，在文本框中指定 **站点名称**，然后按 **Enter**。

    ![“站点名称”的屏幕截图。](./media/saba-cloud-tutorial/site-name.png)

1. 输入你的 **电子邮件地址**，然后单击“下一步”。

    ![“电子邮件地址”的屏幕截图。](./media/saba-cloud-tutorial/email-address.png)

1. 最后，在登录成功后，将显示应用程序页。

    ![成功登录的屏幕截图。](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>后续步骤

配置 Saba Cloud 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。