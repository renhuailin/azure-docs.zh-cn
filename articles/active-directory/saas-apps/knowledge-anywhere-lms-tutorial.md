---
title: 教程：Azure Active Directory 与 Knowledge Anywhere LMS 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Knowledge Anywhere LMS 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2021
ms.author: jeedes
ms.openlocfilehash: 4e97c8253f73ab264d49cfa24403586ca8d573bd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124802201"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>教程：将 Knowledge Anywhere LMS 与 Azure Active Directory 集成

本教程介绍如何将 Knowledge Anywhere LMS 与 Azure Active Directory (Azure AD) 集成。 将 Knowledge Anywhere LMS 与 Azure AD 集成时，可以：

* 在 Azure AD 中控制谁有权访问 Knowledge Anywhere LMS。
* 让用户使用其 Azure AD 帐户自动登录到 Knowledge Anywhere LMS。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Knowledge Anywhere LMS 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 
* Knowledge Anywhere LMS 支持 SP 发起的 SSO。
* Knowledge Anywhere LMS 支持实时用户预配。

## <a name="add-knowledge-anywhere-lms-from-the-gallery"></a>从库中添加 Knowledge Anywhere LMS

若要配置 Knowledge Anywhere LMS 与 Azure AD 的集成，需要从库中将 Knowledge Anywhere LMS 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Knowledge Anywhere LMS”   。
1. 从结果面板中选择“Knowledge Anywhere LMS”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-knowledge-anywhere-lms"></a>配置并测试 Knowledge Anywhere LMS 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Knowledge Anywhere LMS 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Knowledge Anywhere LMS 相关用户之间建立链接关系。

若要配置并测试 Knowledge Anywhere LMS 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Knowledge Anywhere LMS SSO](#configure-knowledge-anywhere-lms-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Knowledge Anywhere LMS 测试用户](#create-knowledge-anywhere-lms-test-user) - 在 Knowledge Anywhere LMS 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Knowledge Anywhere LMS”应用程序集成页上，找到“管理”部分，并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    1. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<CLIENT_NAME>.knowledgeanywhere.com/`

    1. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<CLIENT_NAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDP_NAME>`

    > [!NOTE]
    > 这些不是实际值。 本教程稍后将介绍如何使用实际的标识符和回复 URL 来更新这些值。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > 登录 URL 值不是实际值。 使用实际登录 URL 更新此值。 请联系 [Knowledge Anywhere LMS 客户端支持团队](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

   ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Knowledge Anywhere LMS”部分，根据要求复制相应的 URL  。

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 B. Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B. Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`BrittaSimon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Knowledge Anywhere LMS 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中，选择“Knowledge Anywhere LMS”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-knowledge-anywhere-lms-sso"></a>配置 Knowledge Anywhere LMS SSO

1. 若要在 Knowledge Anywhere LMS 中自动完成配置，需要单击“安装扩展”安装“我的应用安全登录浏览器扩展”。  

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“安装 Knowledge Anywhere LMS”  会定向到 Knowledge Anywhere LMS 应用程序。 在这里，提供管理员凭据，登录到 Knowledge Anywhere LMS。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Knowledge Anywhere LMS，请打开新的 Web 浏览器窗口，以管理员身份登录 Knowledge Anywhere LMS 公司站点，并执行以下步骤：

4. 选择“站点”选项卡。 

    ![屏幕截图显示了“站点”选项卡。](./media/knowledge-anywhere-lms-tutorial/site.png)

5. 选择“SAML 设置”选项卡。 

    ![屏幕截图显示“Knowledge Anywhere”页面，其中选择了“SAML 设置”。](./media/knowledge-anywhere-lms-tutorial/settings.png)

6. 单击“新增”。 

    ![屏幕截图显示了“服务提供商设置”中的“新增”按钮。](./media/knowledge-anywhere-lms-tutorial/add-settings.png)

7. 在“添加/更新 SAML 设置”页上执行以下步骤： 

    ![屏幕截图显示了“添加/更新 SAML 设置”页面，你可以在其中进行此处所述更改。](./media/knowledge-anywhere-lms-tutorial/update-settings.png)

    a. 输入符合组织要求的 IDP 名称。 例如 `Azure`。

    b. 在“IDP 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    c. 在“IDP URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    d. 在记事本中打开从 Azure 门户下载的证书文件，复制证书的内容，并将其粘贴到“证书”文本框中。 

    e. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    f. 从“域”下拉列表中选择“主站点”。  

    g. 复制“SP 实体 ID”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中。   

    h. 复制“SP 响应(ACS) URL”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中。   

    i. 单击“保存”  。

### <a name="create-knowledge-anywhere-lms-test-user"></a>创建 Knowledge Anywhere LMS 测试用户

在本部分，我们将在 Knowledge Anywhere LMS 中创建名为 B. Simon 的用户。 Knowledge Anywhere LMS 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Knowledge Anywhere LMS 中尚不存在用户，则身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Knowledge Anywhere LMS 登录 URL，你可以从那里启动登录流。 

* 直接转到 Knowledge Anywhere LMS 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Knowledge Anywhere LMS”磁贴时，会重定向到 Knowledge Anywhere LMS 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Knowledge Anywhere LMS 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。