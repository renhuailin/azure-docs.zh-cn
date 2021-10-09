---
title: 教程：Azure AD 与 Canvas 的 SSO 集成
description: 了解如何在 Azure Active Directory 与 Canvas 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2021
ms.author: jeedes
ms.openlocfilehash: b91f6c186be774110b52fab75b742dcb73925f03
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622155"
---
# <a name="tutorial-azure-ad-sso-integration-with-canvas"></a>教程：Azure AD 与 Canvas 的 SSO 集成

本教程介绍如何将 Canvas 与 Azure Active Directory (Azure AD) 集成。 将 Canvas 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Canvas。
* 让用户使用其 Azure AD 帐户自动登录到 Canvas。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：
 
* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Canvas 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Canvas 支持 SP 发起的 SSO。

## <a name="add-canvas-from-the-gallery"></a>从库中添加 Canvas

若要配置 Canvas 与 Azure AD 的集成，需要从库中将 Canvas 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Canvas” 。
1. 从结果面板中选择 Canvas，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>配置并测试 Canvas 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Canvas 的 Azure AD SSO。 若要使 SSO 正常运行，需要在 Azure AD 用户与 Canvas 中的相关用户之间建立链接关系。

若要配置并测试 Canvas 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Canvas SSO](#configure-canvas-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Canvas 测试用户](#create-canvas-test-user)** - 在 Canvas 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Canvas”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    b. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.instructure.com` 

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符和登录 URL 更新这些值。 请联系 [Canvas 客户端支持团队](https://community.canvaslms.com/community/help)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

6. 在“SAML 签名证书”部分中，复制 **指纹** 并将其保存在计算机上。

    ![复制指纹值](common/copy-thumbprint.png)

7. 在“设置 Canvas”部分，根据要求复制相应 URL  。

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

本部分将通过授予 B.Simon 访问 Canvas 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Canvas”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-canvas-sso&quot;></a>配置 Canvas SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Canvas 公司站点。

2. 转到“课程” **\>“托管帐户”\>** “Microsoft”。

    ![画布](./media/canvas-lms-tutorial/course.png &quot;画布")

3. 在左侧导航窗格中，选择“身份验证”，并单击“添加新 SAML 配置”。  

    ![身份验证](./media/canvas-lms-tutorial/tools.png "身份验证")

4. 在“当前集成”页上，执行以下步骤：

    ![当前集成](./media/canvas-lms-tutorial/save.png "当前集成")

    a. 在“IdP 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure Ad 标识符”值   。

    b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    d. 在“更改密码链接”  文本框中，粘贴从 Azure 门户复制的“更改密码 URL”  值。

    e. 在“证书指纹”  文本框中，粘贴从 Azure 门户复制的证书“指纹”  值。

    f. 从“登录名属性”列表中，选择 **NameID**。 

    g. 从“标识符格式”列表中，选择 **emailAddress**。 

    h. 单击“保存身份验证设置”  。

### <a name="create-canvas-test-user"></a>创建 Canvas 测试用户

为了使 Azure AD 用户能够登录到 Canvas，必须将其预配到 Canvas 中。 对于 Canvas，需要手动执行用户预配。

**若要预配用户帐户，请执行以下步骤：**

1. 登录“Canvas”  租户。

2. 转到“课程” **\>“托管帐户”\>** “Microsoft”。

   ![画布](./media/canvas-lms-tutorial/course.png "画布")

3. 单击“用户”  。

   ![屏幕截图显示已选择“用户”的 Canvas 菜单。](./media/canvas-lms-tutorial/user.png "用户")

4. 单击“添加新用户”。 

   ![屏幕截图显示“添加新用户”按钮。](./media/canvas-lms-tutorial/add-user.png "用户")

5. 在“添加新用户”对话框页上，执行以下步骤：

   ![添加用户](./media/canvas-lms-tutorial/name.png "添加用户")

   a. 在“全名”  文本框中输入用户的姓名，例如 BrittaSimon  。

   b. 在“电子邮件”文本框中，输入用户的电子邮件，例如 brittasimon\@contoso.com   。

   c. 在“登录名”  文本框中，输入用户的 Azure AD 电子邮件地址（例如 brittasimon\@contoso.com  ）。

   d. 选择“通过电子邮件告知用户此帐户创建操作”。 

   e. 单击“添加用户”  。

> [!NOTE]
> 可以使用 Canvas 提供的任何其他 Canvas 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Canvas 登录 URL，可从那里启动登录流。 

* 直接转到 Canvas 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Canvas 磁贴时，应会自动登录到为其设置了 SSO 的 Canvas。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Canvas 后，可以强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。