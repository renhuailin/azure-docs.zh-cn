---
title: 教程：Azure AD SSO 与 Small Improvements 的集成
description: 了解如何在 Azure Active Directory 和 Small Improvements 之间配置单一登录。
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
ms.openlocfilehash: d1bf361190e92dd687c4c1f41a0c0a544b86c01a
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129419169"
---
# <a name="tutorial-azure-ad-sso-integration-with-small-improvements"></a>教程：Azure AD SSO 与 Small Improvements 的集成

本教程介绍如何将 Small Improvements 与 Azure Active Directory (Azure AD) 相集成。 将 Small Improvements 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Small Improvements。
* 让用户能够使用其 Azure AD 帐户自动登录到 Small Improvements。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Small Improvements 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Small Improvements 支持 SP 发起的 SSO。

## <a name="add-small-improvements-from-the-gallery"></a>从库中添加 Small Improvements

要配置 Small Improvements 与 Azure AD 的集成，需要从库中将 Small Improvements 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Small Improvements” 。
1. 在结果面板中选择“Small Improvements”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-small-improvements"></a>配置并测试 Small Improvements 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Small Improvements 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Small Improvements 中的相关用户之间建立链接关系。

若要配置并测试 Small Improvements 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Small Improvements SSO](#configure-small-improvements-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Small Improvements 测试用户](#create-small-improvements-test-user) - 在 Small Improvements 中创建 B.Simon 的对应用户，并将其链接到该用户在 Azure AD 中对应的身份。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Small Improvements”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<subdomain>.small-improvements.com`

    b. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.small-improvements.com` 

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符和登录 URL 更新这些值。 请联系 [Small Improvements 客户端支持团队](mailto:support@small-improvements.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Small Improvements”部分，根据要求复制相应 URL  。

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

在本部分，你将通过授予 B.Simon 访问 Small Improvements 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Small Improvements”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-small-improvements-sso"></a>配置 Small Improvements SSO

1. 在另一浏览器窗口中，以管理员身份登录 Small Improvements 公司站点。

1. 在主仪表板页中，单击左侧的“管理”  按钮。

    ![显示选择了“管理”按钮的屏幕截图。](./media/smallimprovements-tutorial/admin.png) 

1. 在“集成”部分中，单击“SAML SSO”按钮。

    ![显示在“集成”中选择了“SAML SSO”图标的屏幕截图。](./media/smallimprovements-tutorial/menu.png) 

1. 在“SSO 设置”页上，执行以下步骤：

    ![显示“SSO 设置”页的屏幕截图，可在其中输入所述值。](./media/smallimprovements-tutorial/certificate.png)  

    a. 在“HTTP 终结点”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    b. 在记事本中打开下载的证书，复制其内容，并将其粘贴到“x509 证书”文本框中。  

    c. 如果想要让 SSO 和登录表单身份验证选项可供用户使用，请选中“也启用通过登录名/密码访问”  选项。  

    d. 在“SAML 提示”  文本框中输入用于命名“SSO 登录”按钮的相应值。  

    e. 单击“保存”  。

### <a name="create-small-improvements-test-user"></a>创建 Small Improvements 测试用户

为使 Azure AD 用户可登录 Small Improvements，必须将用户预配到 Small Improvements 中。 使用 Small Improvements 时，需手动预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 Small Improvements 公司站点。

1. 从主页转到左侧菜单，单击“管理”  。

1. 从“用户管理”部分单击“用户目录”  按钮。

    ![显示从“管理概述”中选择了“用户目录”的屏幕截图。](./media/smallimprovements-tutorial/user.png) 

1. 单击“添加用户”  。

    ![显示“添加用户”按钮的屏幕截图。](./media/smallimprovements-tutorial/add-user.png) 

1. 在“添加用户”对话框中，执行以下步骤  ： 

    ![显示“添加用户”对话框的屏幕截图，可在其中输入所述值。](./media/smallimprovements-tutorial/create-user.png)

    a. 输入用户的名字，如 Britta   。

    b. 输入用户的姓氏，如 Simon   。

    c. 输入用户的“电子邮件地址”，如 brittasimon@contoso.com。

    d. 还可以选择在“发送通知电子邮件”  框中输入个人消息。 如果不希望发送通知，请取消选中此复选框。

    e. 单击“创建用户”  。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Small Improvements 登录 URL，可从中启动登录流。 

* 直接转到 Small Improvements 登录 URL，并从中启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Small Improvements”磁贴时，会重定向到 Small Improvements 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Small Improvements 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。