---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 BlueJeans for Azure AD 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 BlueJeans for Azure AD 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2021
ms.author: jeedes
ms.openlocfilehash: 9d4931b07c4564f4a04c8ec46f0666e696854aa4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124747880"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>教程：Azure Active Directory 单一登录 (SSO) 与 BlueJeans for Azure AD 集成

本教程介绍如何将 BlueJeans for Azure AD 与 Azure Active Directory (Azure AD) 进行集成。 将 BlueJeans for Azure AD 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 BlueJeans for Azure AD。
* 让用户使用其 Azure AD 帐户自动登录到 BlueJeans for Azure AD。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 BlueJeans for Azure AD 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* BlueJeans for Azure AD 支持 SP 发起的 SSO。

* BlueJeans for Azure AD 支持[自动用户预配](bluejeans-provisioning-tutorial.md)。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-bluejeans-for-azure-ad-from-the-gallery"></a>从库中添加 BlueJeans for Azure AD

若要配置 BlueJeans for Azure AD 与 Azure AD 的集成，需要从库中将 BlueJeans for Azure AD 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“BlueJeans for Azure AD” 。
1. 从结果面板中选择“BlueJeans for Azure AD”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-bluejeans-for-azure-ad"></a>配置并测试 BlueJeans for Azure AD 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 BlueJeans for Azure AD 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 BlueJeans for Azure AD 中的相关用户之间建立链接关系。

若要配置并测试 BlueJeans for Azure AD 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 BlueJeans for Azure AD SSO](#configure-bluejeans-for-azure-ad-sso)** - 在应用程序端配置单一登录。
    1. **[创建 BlueJeans for Azure AD 测试用户](#create-bluejeans-for-azure-ad-test-user)** - 在 BlueJeans for Azure AD 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“BlueJeans for Azure AD”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.bluejeans.com`。

    a. 在“标识符(实体 ID)”文本框中，键入 URL：`http://samlsp.bluejeans.com` 

    a. 在“回复 URL”文本框中键入 URL：`https://bluejeans.com/sso/saml2/`

    > [!NOTE]
    > 登录 URL 值不是真实值。 请使用实际登录 URL 更新此值。 若要获取此值，请与 [BlueJeans for Azure AD 客户端支持团队](https://support.bluejeans.com/contact)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. BlueJeans 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，BlueJeans 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 |  源属性|
    | ---------| --------- |
    | 电话 | user.telephonenumber |
    | title | user.jobtitle |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上   。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 BlueJeans for Azure AD”部分，根据要求复制相应的 URL。

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

在本部分，我们通过授予 B.Simon 访问 BlueJeans for Azure AD 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中选择“BlueJeans for Azure AD”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-bluejeans-for-azure-ad-sso&quot;></a>配置 BlueJeans for Azure AD SSO

1. 在其他 Web 浏览器窗口中，以管理员身份登录 BlueJeans for Azure AD 公司站点。

2. 依次转到“管理”\>“组设置”\>“安全”。

    ![屏幕截图显示浏览器窗口的一部分，其中“管理”选项卡处于选中状态，并选中了“组设置”和“安全”。](./media/bluejeans-tutorial/admin.png &quot;管理员")

3. 在“安全”部分中，执行以下步骤：

    ![SAML 单一登录](./media/bluejeans-tutorial/security.png "SAML 单一登录")

    a. 选择“SAML 单一登录”。

    b. 选择“启用自动预配”。

4. 使用以下步骤继续：

    ![证书路径](./media/bluejeans-tutorial/certificate.png "证书路径")

    a. 单击“选择文件”，上传从 Azure 门户下载的 base-64 编码的证书。

    b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    c. 在“密码更改 URL”文本框中，粘贴从 Azure 门户复制的“更改密码 URL”值。

    d. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值 。

5. 使用以下步骤继续：

    ![保存更改](./media/bluejeans-tutorial/changes.png "保存更改")

    a. 在“用户 ID”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。

    b. 在“电子邮件”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。

    c. 单击“保存更改”。

### <a name="create-bluejeans-for-azure-ad-test-user"></a>创建 BlueJeans for Azure AD 测试用户

本部分的目的是在 BlueJeans for Azure AD 中创建名为“B.Simon”的用户。 BlueJeans for Azure AD 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](bluejeans-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤：

1. 以管理员身份登录到 BlueJeans for Azure AD 公司站点。

2. 转到“管理”\>“管理用户”\>“添加用户”。

    ![屏幕截图显示浏览器窗口的一部分，其中“管理”选项卡处于选中状态，并选中了“管理用户”和“添加用户”。](./media/bluejeans-tutorial/add-user.png "管理员")

    > [!IMPORTANT]
    > “添加用户”选项卡仅在“安全”选项卡中的的“启用自动预配”处于未选中状态时可用。

3. 在“添加用户”部分中，执行以下步骤：

    ![屏幕截图显示“添加用户”部分，可在其中输入此步骤中所述的信息。](./media/bluejeans-tutorial/new-user.png "添加用户")

    a. 在“名字”文本框中，输入用户的名字，例如 B   。

    b. 在“姓氏”文本框中，输入用户的名字，如 Simon   。

    c. 在“选取 BlueJeans for Azure AD 用户名”文本框中，输入用户的用户名，例如 **Brittasimon**

    d. 在“创建密码”文本框中，输入你的密码。

    e. 在“公司”文本框中，输入你的公司。

    f. 在“电子邮件地址”文本框中，输入用户的电子邮件地址，例如 `b.simon@contoso.com`。

    g. 在“创建 BlueJeans for Azure AD 会议 ID”文本框中，输入你的会议 ID。

    h. 在“选取审查方密码”文本框中，输入你的密码。

    i. 单击“继续”。

    ![屏幕截图显示“添加用户”部分，可在其中查看设置和功能，“添加用户”按钮处于选中状态。](./media/bluejeans-tutorial/settings.png "添加用户")

    J. 单击“添加用户”。

> [!NOTE]
> 可使用 BlueJeans for Azure AD 提供的任何其他 BlueJeans for Azure AD 用户帐户创建工具或 API 预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这将重定向到 BlueJeans for Azure AD 登录 URL，你可以在其中启动登录流。 

* 直接转到 BlueJeans for Azure AD 登录 URL，从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 BlueJeans for Azure AD 磁贴时，将会重定向到 BlueJeans for Azure AD 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 BlueJeans for Azure AD 后，即可强制实施会话控制，实时防止组织的敏感数据遭受外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。