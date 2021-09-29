---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Sugar CRM 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Sugar CRM 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 5af4f18d3fd5e91ed48f8bdd603906b2f700c358
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800662"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Sugar CRM 集成

本教程介绍如何将 Sugar CRM 与 Azure Active Directory (Azure AD) 集成。 将 Sugar CRM 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Sugar CRM。
* 让用户使用其 Azure AD 帐户自动登录到 Sugar CRM。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Sugar CRM 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Sugar CRM 支持 **SP** 发起的 SSO

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-sugar-crm-from-the-gallery"></a>从库中添加 Sugar CRM

若要配置 Sugar CRM 与 Azure AD 的集成，需要从库中将 Sugar CRM 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Sugar CRM”   。
1. 从结果面板中选择“Sugar CRM”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-sugar-crm"></a>配置并测试 Sugar CRM 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Sugar CRM 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Sugar CRM 相关用户之间建立链接关系。

若要配置并测试 Sugar CRM 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Sugar CRM SSO](#configure-sugar-crm-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Sugar CRM 测试用户](#create-sugar-crm-test-user)** - 在 Sugar CRM 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Sugar CRM”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： 

    - `https://<companyname>.sugarondemand.com`
    - `https://<companyname>.trial.sugarcrm`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL： 

    - `https://<companyname>.sugarondemand.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.eu/<companyname>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的登录 URL 和回复 URL 更新这些值。 请联系 [Sugar CRM 客户端支持团队](https://support.sugarcrm.com/)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Sugar CRM”部分中，根据要求复制相应的 URL。 

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

在本部分中，通过授予 B.Simon 访问 Sugar CRM 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Sugar CRM”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name=&quot;configure-sugar-crm-sso&quot;></a>配置 Sugar CRM SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Sugar CRM 公司站点。

1. 转到“管理员”。 

    ![管理员](./media/sugarcrm-tutorial/ic795888.png &quot;管理员")

1. 在“管理”  部分中，单击“密码管理”  。

    ![显示可在其中选择“密码管理”的“管理”部分的屏幕截图。](./media/sugarcrm-tutorial/ic795889.png "管理")

1. 选择“启用 SAML 身份验证”  。

    ![显示用于选择 SAML 身份验证的选项的屏幕截图。](./media/sugarcrm-tutorial/ic795890.png "管理")

1. 在“SAML 身份验证”  部分中，执行以下步骤：

    ![SAML 身份验证](./media/sugarcrm-tutorial/save.png "SAML 身份验证")  

    a. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。
  
    b. 在“SLO URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。
  
    c. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将整个证书粘贴到“X.509 证书”文本框中。 
  
    d. 单击“ **保存**”。

### <a name="create-sugar-crm-test-user"></a>创建 Sugar CRM 测试用户

要使 Azure AD 用户能够登录到 Sugar CRM，必须将其预配到 Sugar CRM 中。 对于 Sugar CRM，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 **Sugar CRM** 公司站点。

1. 转到“管理员”。 

    ![管理员](./media/sugarcrm-tutorial/ic795888.png "管理员")

1. 在“管理”  部分中，单击“用户管理”  。

    ![显示可在其中选择“用户管理”的“管理”部分的屏幕截图。](./media/sugarcrm-tutorial/ic795893.png "管理")

1. 转到“用户”\>“创建新用户”  。

    ![创建新用户](./media/sugarcrm-tutorial/ic795894.png "创建新用户")

1. 在“用户配置文件”  选项卡上，执行以下步骤：

    ![显示可在其中输入所述值的“用户个人资料”选项卡的屏幕截图。](./media/sugarcrm-tutorial/ic795895.png "新建用户")

    * 在相关文本框中键入有效 Azure Active Directory 用户的用户名、姓氏和电子邮件地址。   
  
1. 对于“状态”  ，选择“活动”  。

1. 在“密码”选项卡中，执行以下步骤：

    ![显示可在其中输入所述值的“密码”选项卡的屏幕截图。](./media/sugarcrm-tutorial/ic795896.png "新建用户")

    a. 在相关文本框中键入密码。

    b. 单击“ **保存**”。

> [!NOTE]
> 可以使用 Sugar CRM 提供的任何其他 Sugar CRM 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Sugar CRM 登录 URL，可以从那里启动登录流。 

* 直接转到 Sugar CRM 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Sugar CRM 磁贴时，会重定向到 Sugar CRM 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Sugar CRM 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。