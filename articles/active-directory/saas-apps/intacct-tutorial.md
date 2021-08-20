---
title: 教程：Azure Active Directory 与 Sage Intacct 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Sage Intacct 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 652126519dc5f9b30c9ff94a28b3e6ee78e95f64
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463677"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>教程：将 Sansan Intacct 与 Azure Active Directory 集成

在本教程中，将了解如何将 Sage Intacct 与 Azure Active Directory (Azure AD) 集成。 将 Sage Intacct 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Sage Intacct。
* 让用户使用其 Azure AD 帐户自动登录到 Sage Intacct。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Sage Intacct 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Sage Intacct 支持 IDP 发起的 SSO

## <a name="adding-sage-intacct-from-the-gallery"></a>从库中添加 Sage Intacct

要配置 Sage Intacct 与 Azure AD 的集成，需要从库中将 Sage Intacct 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Sage Intacct” 。
1. 从结果面板中选择“Sage Intacct”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>配置并测试 Sage Intacct 的 Azure AD SSO

使用名为 B. Simon 的测试用户配置和测试 Sage Intacct 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Sage Intacct 相关用户之间建立链接关系。

若要配置和测试 Sage Intacct 的 Azure AD SSO，请完成以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
2. **[配置 Sage Intacct SSO](#configure-sage-intacct-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Sage Intacct 测试用户](#create-sage-intacct-test-user)** - 在 Sage Intacct 中创建 B. Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Sage Intacct”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    在“回复 URL”文本框中，添加以下 URL：  
    `https://www.intacct.com/ia/acct/sso_response.phtml`（选择作为默认值。）  
    `https://www.p-02.intacct.com/ia/acct/sso_response.phtml`  
    `https://www.p-03.intacct.com/ia/acct/sso_response.phtml`  
    `https://www.p-04.intacct.com/ia/acct/sso_response.phtml`  
    `https://www.p-05.intacct.com/ia/acct/sso_response.phtml`  

1. Sage Intacct 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标以打开“用户属性”对话框.

    ![image](common/edit-attribute.png)

1. 除了上述属性，Sage Intacct 应用程序还要求在 SAML 响应中传递回更多的属性。 在“用户属性和声明”对话框中，执行以下步骤来添加 SAML 令牌属性，如下表所示：

    | 属性名称  |  源属性|
    | ---------------| --------------- |
    | 公司名称 | **Sage Intacct 公司 ID** |
    | name | 值应与在“创建 Sage Intact 测试用户部分”中输入的 Sage Intacct“用户 ID”相同，本教程的后半部分会对此进行解释 |

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”列表中，键入或选择为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上   。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Sage Intacct”部分上，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

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

在本部分中，通过授予 B.Simon 访问 Sage Intacct 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Sage Intacct”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name=&quot;configure-sage-intacct-sso&quot;></a>配置 Sage Intacct SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Sage Intacct 公司站点。

1. 单击“公司”选项卡，并单击“公司信息”。

    ![Company](./media/intacct-tutorial/ic790037.png &quot;Company")

1. 单击“安全性”选项卡，并单击“编辑”。

    ![安全性](./media/intacct-tutorial/ic790038.png "安全性")

1. 在“单一登录(SSO)”部分中，执行以下步骤：

    ![单一登录](./media/intacct-tutorial/ic790039.png "单一登录")

    a. 选择“启用单一登录”。

    b. 对于“标识提供者类型”，选择“SAML 2.0”。

    c. 在“颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 。

    d. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    e. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“证书”框中 。

    f. 单击“ **保存**”。

### <a name="create-sage-intacct-test-user"></a>创建 Sage Intacct 测试用户

要设置 Azure AD 用户使其能够登录 Sage Intacct，必须将这些用户预配到 Sage Intacct 中。 对于 Sage Intacct，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 Sage Intacct 租户。

1. 单击“公司”选项卡，并单击“用户”。

    ![用户](./media/intacct-tutorial/ic790041.png "用户")

1. 单击“添加”选项卡。

    ![添加](./media/intacct-tutorial/ic790042.png "添加")

1. 在“用户信息”部分中，执行以下步骤：

    ![屏幕截图显示“用户信息”部分，可在其中输入此步骤中的信息。](./media/intacct-tutorial/ic790043.png "用户信息")

    a. 在“用户信息”部分中输入要预配的 Azure AD 帐户的 **用户 ID**、**姓氏**、**名字**、**电子邮件地址**、**职务** 和 **电话** 。

    > [!NOTE]
    > 确保上述屏幕截图中的“用户 ID”与 Azure 门户中“用户属性”部分的通过“名称”属性映射的“源属性”值是相同的   。

    b. 选择要预配的 Azure AD 帐户的 **管理员特权**。

    c. 单击“ **保存**”。 
    
    d. Azure AD 帐户持有者将收到一封电子邮件，并打开用于在激活帐户前确认其帐户的链接。

1. 单击“单一登录”选项卡并确保下方屏幕截图中的“联合 SSO 用户 ID”与 Azure 门户的“用户属性”部分中通过 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 映射的“源属性”值是相同的   。

    ![屏幕截图显示“用户信息”部分，可在其中输入联合 SSO 用户 ID。](./media/intacct-tutorial/ic790044.png "用户信息")

> [!NOTE]
> 若要预配 Azure AD 用户帐户，可以使用任何其他 Sage Intacct 用户帐户创建工具或 Sage Intacct 提供的 API。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 Sage Intacct

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Sage Intacct 磁贴时，应当会自动登录到你为其设置了 SSO 的 Sage Intacct。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 Sage Intacct 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。
