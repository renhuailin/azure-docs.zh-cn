---
title: 教程：Azure AD SSO 与 Citrix ShareFile 集成
description: 了解如何在 Azure Active Directory 和 Citrix ShareFile 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jeedes
ms.openlocfilehash: 2f876948276d69e859d68bc2cc4a779ccdd78803
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669809"
---
# <a name="tutorial-azure-ad-sso-integration-with-citrix-sharefile"></a>教程：Azure AD SSO 与 Citrix ShareFile 集成

本教程介绍如何将 Citrix ShareFile 与 Azure Active Directory (Azure AD) 集成。 将 Citrix ShareFile 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Citrix ShareFile。
* 让用户使用其 Azure AD 帐户自动登录到 Citrix ShareFile。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Citrix ShareFile 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Citrix ShareFile 支持 SP 发起的 SSO。

## <a name="add-citrix-sharefile-from-the-gallery"></a>从库添加 Citrix ShareFile

要配置 Citrix ShareFile 与 Azure AD 的集成，需要从库中将 Citrix ShareFile 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Citrix ShareFile” 。
1. 从结果面板中选择“Citrix ShareFile”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-citrix-sharefile"></a>配置并测试 Citrix ShareFile 的 Azure AD SSO

在本部分中，基于一个名为 Britta Simon 的测试用户配置和测试 Citrix ShareFile 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Citrix ShareFile 相关用户之间建立链接关系。

若要配置并测试 Citrix ShareFile 的 Azure AD 单一登录，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. [配置 Citrix ShareFile SSO](#configure-citrix-sharefile-sso) - 在应用程序端配置单一登录设置。
    1. **[创建 Citrix ShareFile 测试用户](#create-citrix-sharefile-test-user)** - 在 Citrix ShareFile 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Citrix ShareFile”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作： 

    a. 在“标识符(实体 ID)”文本框中，使用以下模式之一键入 URL：

    | **Identifier** |
    |--------|
    | `https://<tenant-name>.sharefile.com` |
    | `https://<tenant-name>.sharefile.com/saml/info` |
    | `https://<tenant-name>.sharefile1.com/saml/info` |
    | `https://<tenant-name>.sharefile1.eu/saml/info` |
    | `https://<tenant-name>.sharefile.eu/saml/info` |

    b. 在“回复 URL”文本框中，使用以下一种模式键入 URL：
    
    | 回复 URL |
    |-------|
    | `https://<tenant-name>.sharefile.com/saml/acs` |
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>` |
    | `https://<tenant-name>.sharefile.com/saml/<URL path>` |

    c. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<tenant-name>.sharefile.com/saml/login`。

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Citrix ShareFile 客户端支持团队](https://www.citrix.co.in/products/citrix-content-collaboration/support.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Citrix ShareFile”部分，根据要求复制相应 URL。

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

在本部分中，通过授予 B.Simon 访问 Citrix ShareFile 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Citrix ShareFile”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-citrix-sharefile-sso"></a>配置 Citrix ShareFile SSO

1. 若要在 Citrix ShareFile 中自动执行配置，需要通过单击“安装扩展”，安装“我的应用安全登录”浏览器扩展  。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Citrix ShareFile”，此时会将你定向到 Citrix ShareFile 应用程序。 在此处提供管理员凭据以登录到 Citrix ShareFile。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Citrix ShareFile，请在另一个 Web 浏览器窗口中，以管理员身份登录到 Citrix ShareFile 公司站点。

1. 在“仪表板”中，单击“设置”，然后选择“管理设置”  。

    ![管理](./media/sharefile-tutorial/settings.png)

1. 在“管理设置”中，转到“安全性” -> “登录和安全策略” 。
   
    ![帐户管理](./media/sharefile-tutorial/settings-security.png "帐户管理")

1. 在“基本设置”下的“单一登录/SAML 2.0 配置”对话框页上，执行以下步骤：
   
    ![单一登录](./media/sharefile-tutorial/saml-configuration.png "单一登录")
   
    a. 在“启用 SAML”中选择“是” 。

    b. 复制“ShareFile 证书颁发者/实体 ID”值，将其粘贴到 Azure 门户的“基本 SAML 配置”对话框中的“标识符 URL”框中。  
    
    c. 在“IDP 证书颁发者/实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。

    d. 单击“X.509 证书”字段旁边的“更改”，并上传从 Azure 门户下载的证书。
    
    e. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。
    
    f. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    g. 在“可选设置”中，选择“SP 启动的身份验证上下文”作为“用户名和密码”，并选择“完全匹配”   。

5. 单击“保存”。

## <a name="create-citrix-sharefile-test-user"></a>创建 Citrix ShareFile 测试用户

1. 登录到 **Citrix ShareFile** 租户。

2. 单击“人员” -> “管理用户主页” -> “创建新用户” -> “创建员工”   。
   
    ![创建员工](./media/sharefile-tutorial/create-user.png "创建员工")

3. 在“基本信息”部分中执行以下步骤：
   
    ![基本信息](./media/sharefile-tutorial/user-form.png "基本信息")
   
    a. 在“名字”文本框中，输入用户的名字，如 Britta。
   
    b.  在“姓氏”文本框中，输入用户的姓氏，如 Simon。
   
    c. 在“电子邮件地址”文本框中，键入 Britta Simon 的电子邮件地址为“brittasimon\@contoso.com”。

4. 单击“添加用户”  。
  
    >[!NOTE]
    >Azure AD 帐户持有人将收到一封电子邮件，并在帐户激活前按照链接确认其帐户。可使用 Citrix ShareFile 提供的任何其他 Citrix ShareFile 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Citrix ShareFile 登录 URL，你可以在其中启动登录流。

* 直接转到 Citrix ShareFile 登录 URL，并在其中启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Citrix ShareFile 磁贴时，会重定向到 Citrix ShareFile 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Citrix ShareFile 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。