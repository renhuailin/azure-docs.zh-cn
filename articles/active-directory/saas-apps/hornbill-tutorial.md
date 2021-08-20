---
title: 教程：Azure Active Directory 与 Hornbill 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Hornbill 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2021
ms.author: jeedes
ms.openlocfilehash: 2cdc549a5231fed6c6819c0952cb2d7431757852
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688456"
---
# <a name="tutorial-azure-active-directory-integration-with-hornbill"></a>教程：Azure Active Directory 与 Hornbill 的集成

本教程介绍如何将 Hornbill 与 Azure Active Directory (Azure AD) 相集成。 将 Hornbill 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Hornbill。
* 让用户使用其 Azure AD 帐户自动登录到 Hornbill。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Hornbill 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Hornbill 支持 SP 发起的 SSO。
* Hornbill 支持即时用户预配。

## <a name="add-hornbill-from-the-gallery"></a>从库中添加 Hornbill

要配置 Hornbill 与 Azure AD 的集成，需要从库中将 Hornbill 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Hornbill” 。
1. 在结果面板中选择“Hornbill”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-hornbill"></a>配置并测试 Hornbill 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Hornbill 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Hornbill 中的相关用户之间建立链接关系。

若要配置并测试 Hornbill 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Hornbill SSO](#configure-hornbill-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Hornbill 测试用户](#create-hornbill-test-user) - 在 Hornbill 中创建 B.Simon 的对应用户，并将其链接到该用户在 Azure AD 中对应的身份。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Hornbill”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/lib/saml/auth/simplesaml/module.php/saml/sp/metadata.php/saml`

    b. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/` 

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符和登录 URL 更新这些值。 请联系 [Hornbill 客户端支持团队](https://www.hornbill.com/support/?request/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分，你将通过授予 B.Simon 访问 Hornbill 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Hornbill”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-hornbill-sso&quot;></a>配置 Hornbill SSO

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录 Hornbill。

2. 在主页上单击“系统”。

    ![Hornbill 系统](./media/hornbill-tutorial/system.png   &quot;Hornbill 系统")

3. 导航到“安全性”。

    ![Hornbill 安全性](./media/hornbill-tutorial/security.png "Hornbill 安全性")

4. 单击“SSO 配置文件”。

    ![Hornbill 单一登录](./media/hornbill-tutorial/profile.png "Hornbill 单一登录")

5. 在页面右侧，单击“添加徽标”。

    ![Hornbill 添加](./media/hornbill-tutorial/add-logo.png "Hornbill 添加")

6. 在“配置文件详细信息”栏上，单击“导入 SAML Meta 徽标”。 

    ![Hornbill 徽标](./media/hornbill-tutorial/logo.png "Hornbill 徽标")

7. 在弹出页面上的“URL”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”，然后单击“处理”  。

    ![Hornbill 处理](./media/hornbill-tutorial/process.png "Hornbill 处理")

8. 单击“处理”后，值将自动填充到“配置文件详细信息”部分下面。

    ![Hornbill 页面 1](./media/hornbill-tutorial/page.png "Hornbill 页面 1")

    ![Hornbill 页面 2](./media/hornbill-tutorial/services.png "Hornbill 页面 2")

    ![Hornbill 页面 3](./media/hornbill-tutorial/details.png "Hornbill 页面 3")

9. 单击 **“保存更改”** 。

### <a name="create-hornbill-test-user"></a>创建 Hornbill 测试用户

在本部分中，我们会在 Hornbill 中创建一个名为 Britta Simon 的用户。 Hornbill 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Hornbill 中不存在用户，则会在身份验证后创建一个新用户。

> [!Note]
> 如果需要手动创建用户，请联系 [Hornbill 客户端支持团队](https://www.hornbill.com/support/?request/)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Hornbill 登录 URL，可从中启动登录流。 

* 直接转到 Hornbill 登录 URL，并从中启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Hornbill”磁贴时，会重定向到 Hornbill 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Hornbill 后，可以强制实施会话控制，从而实时防止组织的敏感数据遭到外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。