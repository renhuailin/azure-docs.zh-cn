---
title: 教程：Azure Active Directory 与 Panopto 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Panopto 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2021
ms.author: jeedes
ms.openlocfilehash: ce999b7cf06ae9b9a9c9fe591b631d8bb0d14caa
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111569886"
---
# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>教程：Azure Active Directory 与 Panopto 的集成

本教程介绍如何将 Panopto 与 Azure Active Directory (Azure AD) 集成。 将 Panopto 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Panopto。
* 让用户使用其 Azure AD 帐户自动登录到 Panopto。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Panopto 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Panopto 支持 SP 发起的 SSO。

* Panopto 支持“实时”用户预配。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-panopto-from-the-gallery"></a>从库中添加 Panopto

若要配置 Panopto 与 Azure AD 的集成，需要从库中将 Panopto 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Panopto” 。
1. 从结果面板中选择“Panopto”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-panopto"></a>配置并测试 Panopto 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Panopto 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Panopto 中的相关用户之间建立关联。

若要配置并测试 Panopto 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Panopto SSO](#configure-panopto-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Panopto 测试用户](#create-panopto-test-user) - 在 Panopto 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 Panopto 应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<TENANT_NAME>.panopto.com`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Panopto 客户端支持团队](mailto:support@panopto.com)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Panopto”部分，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>创建 Azure AD 测试用户 

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Panopto 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Panopto”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-panopto-sso&quot;></a>配置 Panopto SSO

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 Panopto 公司站点。

2. 在左侧工具栏中，单击 **“系统”**，然后单击 **“标识提供程序”**。
   
    ![系统](./media/panopto-tutorial/toolbar.png &quot;系统")

3. 单击 **“添加提供程序”**。
   
    ![标识提供者](./media/panopto-tutorial/provider.png "标识提供者")
   
4. 在“SAML 提供者”部分执行以下步骤：
   
    ![SaaS 配置](./media/panopto-tutorial/configuration.png "SaaS 配置")
    
    a. 在“提供者类型”列表中，选择“SAML20”。    
    
    b. 在 **“实例名称”** 文本框中，键入该实例的名称。

    c. 在 **“友好描述”** 文本框中，键入友好描述。
    
    d. 在“跳出页面 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    e. 在“颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    f. 打开从 Azure 门户中下载的 base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“公钥”文本框中。

5. 单击“ **保存**”。

### <a name="create-panopto-test-user"></a>创建 Panopto 测试用户

在本部分，我们会在 Panopto 中创建一个名为 Britta Simon 的用户。 Panopto 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 Panopto 中尚不存在用户，身份验证后会创建一个新用户。

>[!NOTE]
>可以使用任何其他 Panopto 用户帐户创建工具或 Panopto 提供的 API 来预配 AAD 用户帐户。
>

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Panopto 登录 URL，你可以从那里启动登录流。 

* 直接转到 Panopto 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Panopto 磁贴时，会重定向到 Panopto 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Panopto 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
