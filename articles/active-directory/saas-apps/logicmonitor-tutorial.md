---
title: 教程：Azure AD 与 LogicMonitor 的 SSO 集成
description: 了解如何在 Azure Active Directory 和 LogicMonitor 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2021
ms.author: jeedes
ms.openlocfilehash: 86623285f1fa4d9ca3c8be9d1d628bf3c49a0d6c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607637"
---
# <a name="tutorial-azure-ad-sso-integration-with-logicmonitor"></a>教程：Azure AD 与 LogicMonitor 的 SSO 集成

本教程将介绍如何将 LogicMonitor 与 Azure Active Directory (Azure AD) 集成。 将 LogicMonitor 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 LogicMonitor。
* 让用户使用其 Azure AD 帐户自动登录到 LogicMonitor。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 LogicMonitor 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* LogicMonitor 支持 SP 发起的 SSO。

## <a name="add-logicmonitor-from-the-gallery"></a>从库中添加 LogicMonitor

若要配置 LogicMonitor 与 Azure AD 的集成，需要从库中将 LogicMonitor 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“LogicMonitor” 。
1. 从结果面板中选择“LogicMonitor”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-logicmonitor"></a>配置并测试 LogicMonitor 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 LogicMonitor 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 LogicMonitor 中的相关用户之间建立链接关系。

若要配置并测试 LogicMonitor 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 LogicMonitor SSO](#configure-logicmonitor-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 LogicMonitor 测试用户](#create-logicmonitor-test-user)** - 在 LogicMonitor 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 LogicMonitor 应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<companyname>.logicmonitor.com`
    
    b. 在“回复 URL (断言使用者服务 URL)”文本框中，键入 URL：`https://companyname.logicmonitor.com/santaba/saml/SSO/` 
  
    c. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.logicmonitor.com` 

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符和登录 URL 更新这些值。 请联系 [LogicMonitor 客户端支持团队](https://www.logicmonitor.com/contact/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 LogicMonitor”部分，根据要求复制相应的 URL。

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

本部分将通过授予 B.Simon 访问 LogicMonitor 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“LogicMonitor”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-logicmonitor-sso&quot;></a>配置 LogicMonitor SSO

1. 以管理员身份登录到你的 **LogicMonitor** 公司站点。

2. 在顶部菜单中，单击“设置”。

    ![设置](./media/logicmonitor-tutorial/menu.png &quot;设置")

3. 在左侧导航栏中，单击“单一登录”。

    ![单一登录](./media/logicmonitor-tutorial/roles.png "单一登录")

4. 在 **“单一登录 (SSO) 设置”** 部分中，执行以下步骤：

    ![单一登录设置](./media/logicmonitor-tutorial/metadata.png "单一登录设置")

    a. 选择 **“启用单一登录”**。

    b. 选择 **“只读”** 作为 **“默认角色分配”**。

    c. 在记事本中打开下载的元数据文件，然后将其内容粘贴到 **“标识提供程序元数据”** 文本框中。

    d. 单击 **“保存更改”** 。

### <a name="create-logicmonitor-test-user"></a>创建 LogicMonitor 测试用户

为确保 Azure AD 用户能够登录，必须使用其 Azure Active Directory 用户名将其预配到 LogicMonitor 应用程序。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录到 LogicMonitor 公司站点。

2. 在顶部菜单中，单击 **“设置”**，然后单击 **“角色和用户”**。

    ![角色和用户](./media/logicmonitor-tutorial/user.png "角色和用户")

3. 单击“添加”。

4. 在 **“添加帐户”** 部分中，执行以下步骤：

    ![添加帐户](./media/logicmonitor-tutorial/details.png "添加帐户")

    a. 将要预配的 Azure Active Directory 用户的“用户名”、“电子邮件”、“密码”和“重新键入密码”值键入到相关文本框中   。

    b. 依次选择“角色”、“查看权限”和“状态”  。

    c. 单击“提交”  。

> [!NOTE]
> 可以使用 LogicMonitor 提供的任何其他 LogicMonitor 用户帐户创建工具或 API 来预配 Azure Active Directory 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Dotcom-Monitor 登录 URL，你可以从此处启动登录流。 

* 直接转到 LogicMonitor 登录 URL，并从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 LogicMonitor 磁贴时，应会自动登录到为其设置了 SSO 的 LogicMonitor。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 LogicMonitor 后，就可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。