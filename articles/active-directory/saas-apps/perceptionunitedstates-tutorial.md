---
title: 教程：Azure AD SSO 与 Perception United States (Non-UltiPro) 集成
description: 了解如何在 Azure Active Directory 与 Perception United States (Non-UltiPro) 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/05/2021
ms.author: jeedes
ms.openlocfilehash: 62e963cdf3c0cdb60648e032a0520fdd71a90874
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856219"
---
# <a name="tutorial-azure-ad-sso-integration-with-perception-united-states-non-ultipro"></a>教程：Azure AD SSO 与 Perception United States (Non-UltiPro) 集成

本教程介绍如何将 Perception United States (Non-UltiPro) 与 Azure Active Directory (Azure AD) 集成。 将 Perception United States (Non-UltiPro) 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Perception United States (Non-UltiPro)。
* 让用户使用其 Azure AD 帐户自动登录到 Perception United States (Non-UltiPro)。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 United States (Non-UltiPro) 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Perception United States (Non-UltiPro) 支持 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-perception-united-states-non-ultipro-from-the-gallery"></a>从库中添加 Perception United States (Non-UltiPro)

若要配置 Perception United States (Non-UltiPro)与 Azure AD 的集成，需要从库中将 Perception United States (Non-UltiPro) 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Perception United States (Non-UltiPro)” 。
1. 从结果面板中选择“Perception United States (Non-UltiPro)”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-perception-united-states-non-ultipro"></a>配置并测试 Perception United States (Non-UltiPro) 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Perception United States (Non-UltiPro) 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Perception United States (Non-UltiPro) 中的相关用户之间建立链接关系。

若要配置并测试 Perception United States (Non-UltiPro) 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Perception United States (Non-UltiPro) SSO](#configure-perception-united-states-non-ultipro-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Perception United States (Non-UltiPro) 测试用户](#create-perception-united-states-non-ultipro-test-user)** - 在 Perception United States (Non-UltiPro) 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Perception United States (Non-UltiPro)”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”页上，执行以下步骤：

    a. 在“标识符”文本框中键入 URL：`https://perception.kanjoya.com/sp`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. “Perception United States (Non-UltiPro)”应用程序需要 URI 编码的 <entity_id> 形式的“Azure AD 标识符”值，可从“设置 Perception United States (Non-UltiPro)”部分获取该值。    若要获取 URI 编码值，请使用以下链接： **http://www.url-encode-decode.com/** 。

    d. 获取 URI 编码值之后，请将它与下面所述的“回复 URL”合并： 

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. 将上述值粘贴到“回复 URL”文本框中。 

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Perception United States (Non-UltiPro)”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 Perception United States (Non-UltiPro) 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Perception United States (Non-UltiPro)”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-perception-united-states-non-ultipro-sso"></a>配置 Perception United States (Non-UltiPro) SSO

1. 在另一浏览器窗口中，以管理员身份登录到 Perception United States (Non-UltiPro) 公司站点。

2. 在主工具栏中，单击“帐户设置”  。

    ![屏幕截图显示从主工具栏选择了“帐户设置”。](./media/perceptionunitedstates-tutorial/user.png)

3. 在“帐户设置”  页上，执行以下步骤：

    ![Perception United States (Non-UltiPro) 用户](./media/perceptionunitedstates-tutorial/account.png)

    a. 在“公司名称”文本框中，键入 **公司** 的名称。
    
    b. 在“帐户名”文本框中，键入 **帐户** 的名称。

    c. 在“默认收件人电子邮件”文本框中，键入有效的 **电子邮件**。

    d. 选择“SAML 2.0”作为“SSO 标识提供者”。  

4. 在“SSO 配置”  页上，执行以下步骤：

    ![Perception United States (Non-UltiPro) SSO 配置。](./media/perceptionunitedstates-tutorial/configuration.png)

    a. 选择“电子邮件”作为“SAML NameID 类型”。  

    b. 在“SSO 配置名称”文本框中，键入 **配置** 的名称。
    
    c. 在“标识提供者名称”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。 

    d. 在“SAML 域”文本框中，输入类似于 @contoso.com 的域。

    e. 单击“再次上传”以上传“元数据 XML”文件。  

    f. 单击“更新”  。

### <a name="create-perception-united-states-non-ultipro-test-user"></a>创建 Perception United States (Non-UltiPro) 测试用户

本部分将在 Perception United States (Non-UltiPro) 中创建名为 Britta Simon 的用户。 与 [Perception United States (Non-UltiPro) 支持团队](https://www.ultimatesoftware.com/Contact/ContactUs)协作，在 Perception United States (Non-UltiPro) 平台中添加用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Perception United States (Non-UltiPro)。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的“Perception United States (Non-UltiPro)”磁贴时，应会自动登录到为其设置了 SSO 的 Perception United States (Non-UltiPro)。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Perception United States (Non-UltiPro) 后，即可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。