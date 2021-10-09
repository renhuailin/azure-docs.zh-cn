---
title: 教程：Azure AD SSO 与 Kintone 集成
description: 了解如何在 Azure Active Directory 和 Kintone 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/22/2021
ms.author: jeedes
ms.openlocfilehash: 5242c8274ea6febb08c5b547d518e9c606a7411d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641455"
---
# <a name="tutorial-azure-ad-sso-integration-with-kintone"></a>教程：Azure AD SSO 与 Kintone 集成

在本教程中，了解如何将 Kintone 与 Azure Active Directory (Azure AD) 集成。 将 Kintone 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Kintone。
* 让用户可使用其 Azure AD 帐户自动登录到 Kintone。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Kintone 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Kintone 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Kintone 支持 SP 发起的 SSO。

## <a name="add-kintone-from-the-gallery"></a>从库中添加 Kintone

若要配置 Kintone 与 Azure AD 的集成，需要从库中将 Kintone 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Kintone” 。
1. 从结果面板中选择“Kintone”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-kintone"></a>配置并测试 Kintone 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Kintone 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Kintone 中的相关用户之间建立关联。

若要配置并测试 Kintone 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Kintone SSO](#configure-kintone-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Kintone 测试用户](#create-kintone-test-user) - 在 Kintone 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Kintone”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式之一键入 URL：
    
   | **Identifier** |
   |---|
   | `https://<companyname>.cybozu.com` |
   | `https://<companyname>.kintone.com` |
    
    b. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.kintone.com` 

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符和登录 URL 更新这些值。 请联系 [Kintone 客户端支持团队](https://www.kintone.com/contact/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Kintone”部分中，根据要求复制相应的 URL  。

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

在本部分，通过授予 B.Simon 访问 Kintone 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Kintone”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name=&quot;configure-kintone-sso&quot;></a>配置 Kintone SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Kintone 公司站点  。

1. 单击“设置”图标  。

    ![设置](./media/kintone-tutorial/icon.png &quot;设置")

1. 单击“用户和系统管理”  。

    ![用户和系统管理](./media/kintone-tutorial/user.png "用户和系统管理")

1. 在“系统管理”\>“安全性”下，单击“登录”。

    ![登录](./media/kintone-tutorial/system.png "登录")

1. 单击“启用 SAML 身份验证”  。

    ![屏幕截图显示选择了“用户和系统管理”。](./media/kintone-tutorial/security.png "SAML 身份验证")

1. 在“SAML 身份验证”部分中，执行以下步骤：

    ![SAML 身份验证](./media/kintone-tutorial/certificate.png "SAML 身份验证")

    a. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    b. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    c. 要上传从 Azure 门户下载的证书文件，请单击“浏览”  。

    d. 单击“保存”  。

### <a name="create-kintone-test-user"></a>创建 Kintone 测试用户

要使 Azure AD 用户能够登录 Kintone，必须将这些用户预配到 Kintone 中。 对于 kintone，预配是一项手动任务。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：

1. 以管理员身份登录到 Kintone 公司站点  。

1. 单击“设置”图标  。

    ![设置](./media/kintone-tutorial/icon.png "设置")

1. 单击“用户和系统管理”  。

    ![用户和系统管理](./media/kintone-tutorial/user.png "用户和系统管理")

1. 在“用户管理”  下，单击“部门和用户”  。

    ![部门和用户](./media/kintone-tutorial/services.png "部门和用户")

1. 单击“新建用户”  。

    ![屏幕截图显示了“用户”部分，其中已选择“添加用户”操作。](./media/kintone-tutorial/status.png "新建用户")

1. 在“新建用户”  部分中，执行以下步骤：

    ![新建用户](./media/kintone-tutorial/details.png "新建用户")

    a. 在相关文本框中键入要预配的有效 Azure AD 帐户的“显示名称”、“登录名”、“新密码”、“确认密码”、“电子邮件地址”和其他详细信息      。

    b. 单击“保存”  。

> [!NOTE]
> 可以使用 Kintone 提供的任何其他 Kintone 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 Kintone 登录 URL，可以从那里启动登录流。 

* 直接转到 Kintone 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Kintone”磁贴时，会重定向到 Kintone 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Kintone 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。