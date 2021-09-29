---
title: 教程：Azure Active Directory 与 Marketo 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Marketo 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 6e87f8b6f11ffc86d8e4f63facb2433b9f40b7c5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124808685"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>教程：Azure Active Directory 与 Marketo 的集成

本教程介绍了如何将 Marketo 与 Azure Active Directory (Azure AD) 进行集成。
将 Marketo 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Marketo。
* 可以让用户使用其 Azure AD 帐户自动登录到 Marketo（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Marketo 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Marketo 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Marketo 支持 IDP 发起的 SSO 

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-marketo-from-the-gallery"></a>从库中添加 Marketo

要配置 Marketo 与 Azure AD 的集成，需要从库中将 Marketo 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Marketo” 。
1. 从结果面板中选择“Marketo”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-marketo"></a>配置并测试 Marketo 的 Azure AD SSO

在本部分中，将基于名为“Britta Simon”的测试用户使用 Marketo 配置和测试 Azure AD 单一登录  。
若要使单一登录有效，需要在 Azure AD 用户与 Marketo 相关用户之间建立链接关系。

若要配置并测试 Marketo 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD SSO。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使用户 Britta Simon 能够使用 Azure AD SSO。
2. **[配置 Marketo SSO](#configure-marketo-sso)** - 在应用程序端配置 SSO 设置。
    1. **[创建 Marketo 测试用户](#create-marketo-test-user)** - 在 Marketo 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Marketo”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“标识符”文本框中键入 URL：`https://saml.marketo.com/sp`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://login.marketo.com/saml/assertion/<munchkinid>`

    c. 在“中继状态”文本框中，使用以下模式键入 URL：`https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > 这些不是实际值。 使用实际回复 URL 和中继状态更新这些值。 请联系 [Marketo 客户端支持团队](https://investors.marketo.com/contactus.cfm)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Marketo”部分中，根据要求复制相应 URL  。

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

在本部分，你将通过授予 B.Simon 访问 Marketo 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Marketo”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-marketo-sso"></a>配置 Marketo SSO

1. 若要在 Marketo 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录浏览器扩展” 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Marketo”，此时会将你定向到 Marketo 应用程序。 在此处，提供管理员凭据以登录到 Marketo。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Marketo，请在另一个 Web 浏览器窗口中，以管理员身份登录到 Marketo 公司站点。

1. 若获取应用程序的 Munchkin ID，请执行以下操作：
   
    a. 使用管理员凭据登录到 Marketo 应用。
   
    b. 单击顶部导航窗格上的“管理员”按钮。 
   
    ![配置单一登录 1](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 导航到“集成”菜单，单击“Munchkin 链接”  。
   
    ![配置单一登录 2](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. 复制屏幕上显示的 Munchkin ID，并在 Azure AD 配置向导中填写回复 URL。
   
    ![配置单一登录 3](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. 若要在应用程序中配置 SSO，请执行以下步骤：
   
    a. 使用管理员凭据登录到 Marketo 应用。
   
    b. 单击顶部导航窗格上的“管理员”按钮。 
   
    ![配置单一登录 4](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 导航到“集成”菜单，然后单击“单一登录”  。
   
    ![配置单一登录 5](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. 若要启用 SAML 设置，请单击“编辑”  按钮。
   
    ![配置单一登录6](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. 已启用  的单一登录设置。
   
    f. 在“颁发者 ID”文本框中粘贴“Azure AD 标识符”   。
   
    g. 在“实体 ID”  文本框中，输入 `http://saml.marketo.com/sp` 作为URL。
   
    h. 对于“名称标识符元素”，选择“用户 ID 位置”  。
   
    ![配置单一登录 7](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > 如果用户标识符不是 UPN 值，则在“属性”选项卡中更改该值。
   
    i. 上传从 Azure AD 配置向导下载的证书。 保存  设置。
   
    j. 编辑“重定向页面”设置。
   
    k. 在“登录 URL”文本框中粘贴“登录 URL”   。
   
    l. 在“注销 URL”文本框中粘贴“注销 URL”   。
   
    m. 在“错误 URL”中，复制 Marketo 实例 URL 并单击“保存”按钮以保存设置    。
   
    ![配置单一登录 8](./media/marketo-tutorial/tutorial_marketo_10.png)

3. 若要为用户启用 SSO，请完成以下操作：
   
    a. 使用管理员凭据登录到 Marketo 应用。
   
    b. 单击顶部导航窗格上的“管理员”按钮。 
   
    ![配置单一登录 9](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 导航到“安全性”菜单，并单击“登录设置”。  
   
    ![配置单一登录 10](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. 选中“需要 SSO”选项并保存设置。  
   
    ![配置单一登录 11](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>创建一个 Marketo 测试用户

在本部分中，会在 Marketo 中创建一个名为 Britta Simon 的用户。 请按照以下步骤在 Marketo 平台中创建用户。

1. 使用管理员凭据登录到 Marketo 应用。

2. 单击顶部导航窗格上的“管理员”按钮。 
   
    ![测试用户 1](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. 导航到“安全性”菜单，单击“用户和角色”  
   
    ![测试用户 2](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. 在“用户”选项卡上单击“邀请新用户”链接 
   
    ![测试用户 3](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. 在“邀请新用户”向导中，填写以下信息
   
    a. 在文本框中输入用户 **电子邮件** 地址
   
    ![测试用户 4](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. 在文本框中输入 **名字**
   
    c. 在文本框中输入 **姓氏**
   
    d. 点击“下一步” 

6. 在“权限”选项卡中，选择“用户角色”并单击“下一步”   
   
    ![测试用户 5](./media/marketo-tutorial/tutorial_marketo_17.png)
7. 单击“发送”按钮以发送用户邀请 
   
    ![测试用户 6](./media/marketo-tutorial/tutorial_marketo_18.png)

8. 用户将收到电子邮件通知，且必须单击链接并更改密码以激活帐户。 

### <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Marketo

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Marketo 磁贴时，应会自动登录到为其设置了 SSO 的 Marketo。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Marketo 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。