---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Zscaler B2B User Portal 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Zscaler B2B User Portal 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: 0b39947e1ce09a85e5005f3e1d4086d6381868b6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764633"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Zscaler B2B User Portal 的集成

本教程介绍如何将 Zscaler B2B User Portal 与 Azure Active Directory (Azure AD) 集成。 将 Zscaler B2B User Portal 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Zscaler B2B User Portal。
* 让用户使用其 Azure AD 帐户自动登录到 Zscaler B2B User Portal。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Zscaler B2B User Portal 订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Zscaler B2B User Portal 支持 IDP 发起的 SSO。

* Zscaler B2B User Portal 支持实时用户预配。

## <a name="add-zscaler-b2b-user-portal-from-the-gallery"></a>从库中添加 Zscaler B2B User Portal

若要配置 Zscaler B2B User Portal 与 Azure AD 的集成，需要从库中将 Zscaler B2B User Portal 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 Zscaler B2B User Portal。
1. 在结果面板中选择“Zscaler B2B User Portal”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-zscaler-b2b-user-portal"></a>配置并测试 Zscaler B2B User Portal 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Zscaler B2B User Portal 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Zscaler B2B User Portal 相关用户之间建立关联。

若要配置并测试 Zscaler B2B User Portal 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Zscaler B2B User Portal SSO](#configure-zscaler-b2b-user-portal-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Zscaler B2B User Portal 测试用户](#create-zscaler-b2b-user-portal-test-user)** - 在 Zscaler B2B User Portal 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Zscaler B2B User Portal”应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Zscaler B2B User Portal 客户端支持团队](https://help.zscaler.com/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Zscaler B2B User Portal”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 Zscaler B2B User Portal 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中选择“Zscaler B2B User Portal”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-zscaler-b2b-user-portal-sso"></a>配置 Zscaler B2B User Portal SSO

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 Zscaler B2B User Portal 公司站点并执行以下步骤：

1. 在菜单左侧单击“管理”，导航到“身份验证”部分，单击“IdP 配置”    。

    ![Zscaler Private Access Administrator 管理](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. 在右上角，单击“添加 IdP 配置”  。 

    ![Zscaler Private Access Administrator idp](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. 在“添加 IdP 配置”页面上，执行以下步骤：
 
    ![Zscaler Private Access Administrator 选择](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. 在“IdP 元数据文件上传”字段中单击“选择文件”，上传从 Azure AD 下载的元数据文件。

    b. 它会从 Azure AD 读取 **IdP 元数据**，并填充所有字段信息，如下所示。

    ![Zscaler Private Access Administrator 配置](./media/zscaler-b2b-user-tutorial/config.png)

    c. 从“域”字段中选择域。
    
    d. 单击“ **保存**”。

### <a name="create-zscaler-b2b-user-portal-test-user"></a>创建 Zscaler B2B User Portal 测试用户

在本部分，我们将在 Zscaler B2B User Portal 中创建一个名为 Britta Simon 的用户。 Zscaler B2B User Portal 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Zscaler B2B User Portal 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Zscaler B2B User Portal。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Zscaler B2B User Portal”磁贴时，你应会自动登录到为其设置了 SSO 的 Zscaler B2B User Portal。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Zscaler B2B User Portal 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。