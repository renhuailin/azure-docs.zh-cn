---
title: 教程：Azure Active Directory 与 TargetProcess 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 TargetProcess 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/22/2021
ms.author: jeedes
ms.openlocfilehash: dfdc9635634ad2fea3fed3301892dffe6cc7c395
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114605578"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>教程：Azure Active Directory 与 TargetProcess 集成

在本教程中，你将了解如何将 TargetProcess 与 Azure Active Directory (Azure AD) 集成。 将 TargetProcess 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 TargetProcess。
* 让用户使用其 Azure AD 帐户自动登录到 TargetProcess。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 TargetProcess 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* TargetProcess 支持 SP 启动的 SSO。
* TargetProcess 支持“实时”用户预配。

## <a name="add-targetprocess-from-the-gallery"></a>从库添加 TargetProcess

要配置 TargetProcess 与 Azure AD 的集成，需要从库中将 TargetProcess 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“TargetProcess” 。
1. 从结果面板中选择“TargetProcess”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-targetprocess"></a>配置并测试 TargetProcess 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 TargetProcess 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 TargetProcess 中的相关用户之间建立关联。

若要配置并测试 TargetProcess 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 TargetProcess SSO](#configure-targetprocess-sso) - 在应用程序端配置单一登录设置。
    1. [创建 TargetProcess 测试用户](#create-targetprocess-test-user) - 在 TargetProcess 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“TargetProcess”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.tpondemand.com/`

    b. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.tpondemand.com/` 

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符和登录 URL 更新这些值。 请联系 [TargetProcess 客户端支持团队](mailto:support@targetprocess.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 TargetProcess”部分中，根据要求复制相应的 URL  。

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

在本部分，你将通过授予 B.Simon 访问 TargetProcess 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“TargetProcess”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-targetprocess-sso"></a>配置 TargetProcess SSO

1. 必须通过单击“安装扩展”来安装“我的应用安全登录浏览器扩展”，才能在 **TargetProcess** 中自动执行配置。  

    ![image](./media/target-process-tutorial/install-extension.png)

1. 将扩展添加到浏览器后，单击“安装 TargetProcess”  会定向到 TargetProcess 应用程序。 随后，提供管理员凭据，以登录到 TargetProcess。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

    **如果要手动配置应用程序，请执行以下步骤：**

1. 以管理员身份登录 TargetProcess 应用程序。

1. 在顶部菜单中，单击“设置”。

    ![设置](./media/target-process-tutorial/menu.png)

1. 单击“设置”选项卡。

    ![设置](./media/target-process-tutorial/profile.png)

1. 单击“单一登录”选项卡。

    ![单击“单一登录”](./media/target-process-tutorial/personal-settings.png)

1. 在“单一登录设置”对话框上，执行以下步骤：

    ![配置单一登录](./media/target-process-tutorial/certificate.png)

    a. 单击“启用单一登录”。

    b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    c. 在记事本中打开下载的证书，复制其内容，并将其粘贴到“证书”文本框中。

    d. 单击“启用 JIT 预配”。

    e. 单击“ **保存**”。

### <a name="create-targetprocess-test-user"></a>创建 TargetProcess 测试用户

在本部分中，我们会在 TargetProcess 中创建一个名为 Britta Simon 的用户。 TargetProcess 支持默认情况下启用的实时用户预配。 此部分不存在任何操作项。 如果 TargetProcess 中尚不存在用户，则会在身份验证后创建一个新用户。

> [!Note]
> 如果需要手动创建用户，请联系 [TargetProcess 支持团队](mailto:support@targetprocess.com)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 TargetProcess 登录 URL，你可在其中启动登录流。 

* 直接转到 TargetProcess 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 TargetProcess 磁贴时，会重定向到 TargetProcess 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 TargetProcess 后，即可强制实施会话控制，从而实时保护组织的敏感数据，使其免遭外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
