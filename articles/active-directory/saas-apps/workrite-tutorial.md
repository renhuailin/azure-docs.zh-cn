---
title: 教程：Azure AD SSO 与 Workrite 集成
description: 了解如何在 Azure Active Directory 和 Workrite 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: jeedes
ms.openlocfilehash: e088e436778fe90e141c0b216f5932fd51d71043
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129418501"
---
# <a name="tutorial-azure-ad-sso-integration-with-workrite"></a>教程：Azure AD SSO 与 Workrite 集成

本教程介绍如何将 Workrite 与 Azure Active Directory (Azure AD) 集成。 将 Workrite 与 Azure AD 集成后，便可以：

* 在 Azure AD 中控制谁有权访问 Workrite。
* 让用户使用其 Azure AD 帐户自动登录到 Workrite。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Workrite 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Workrite 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Workrite 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-workrite-from-the-gallery"></a>从库中添加 Workrite

要配置 Workrite 与 Azure AD 的集成，需将库中的 Workrite 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Workrite” 。
1. 从结果面板中选择“Workrite”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-workrite"></a>配置并测试 Workrite 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Workrite 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Workrite 相关用户之间建立链接关系。

若要配置并测试 Workrite 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Workrite SSO](#configure-workrite-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Workrite 测试用户](#create-workrite-test-user)** - 在 Workrite 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Workrite”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=<uniqueid>`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Workrite 客户端支持团队](mailto:support@workrite.co.uk)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Workrite”部分中，根据要求复制相应 URL。

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

在本部分，你将通过授予 B.Simon 访问 Workrite 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Workrite”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-workrite-sso"></a>配置 Workrite SSO

若要在 Workrite 端配置单一登录，需要将下载的“证书(Base64)”以及从 Azure 门户复制的相应 URL 发送给 [Workrite 支持团队](mailto:support@workrite.co.uk)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-workrite-test-user"></a>创建 Workrite 测试用户

本部分的目的是在 Workrite 中创建名为 Britta Simon 的用户。

**若要在 Workrite 中创建名为 Britta Simon 的用户，请执行以下步骤：**

1. 以管理员身份登录到 Workrite 公司站点。

2. 在导航窗格中，单击“管理员”。
   
    ![管理员控制](./media/workrite-tutorial/menu.png)

3. 转到“快速链接”，并单击“创建用户”。
   
    ![“创建用户”部分](./media/workrite-tutorial/user.png)

4. 在“创建用户”对话框中执行以下步骤：
   
    ![创建用户对话框](./media/workrite-tutorial/roles.png)
    
    a. 在“电子邮件”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    b. 在“名字”文本框中，键入用户的名字（如 Britta）。

    c. 在“姓氏”文本框中，键入用户的姓氏（如 Simon）。
    
    d. 对于“选择角色”选项，请选择“客户端管理员”。  
    
    e. 单击“ **保存**”。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这将重定向到 Workrite 登录 URL，你可以从那里启动登录流。 

* 直接转到 Workrite 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Workrite”磁贴时，会重定向到 Workrite 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Workrite 后，可以强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。