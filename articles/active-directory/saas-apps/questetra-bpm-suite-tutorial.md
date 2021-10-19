---
title: 教程：Azure AD SSO 与 Questetra BPM Suite 的集成
description: 了解如何在 Azure Active Directory 和 Questetra BPM Suite 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/06/2021
ms.author: jeedes
ms.openlocfilehash: dd7e2139cb23a57c4c7f163e42ba74c649258a07
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859214"
---
# <a name="tutorial-azure-ad-sso-integration-with-questetra-bpm-suite"></a>教程：Azure AD SSO 与 Questetra BPM Suite 的集成

在本教程中，了解如何将 Questetra BPM Suite 与 Azure Active Directory (Azure AD) 集成。 将 Questetra BPM Suite 与 Azure AD 集成时，可以：

* 在 Azure AD 中控制谁有权访问 Questetra BPM Suite。
* 让用户使用其 Azure AD 帐户自动登录到 Questetra BPM Suite。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Questetra BPM Suite 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Questetra BPM Suite 支持 SP 发起的 SSO。

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>从库中添加 Questetra BPM Suite

要配置 Questetra BPM Suite 与 Azure AD 的集成，需要从库中将 Questetra BPM Suite 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Questetra BPM Suite” 。
1. 从结果面板中选择“Questetra BPM Suite”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-questetra-bpm-suite"></a>配置并测试 Questetra BPM Suite 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Questetra BPM Suite 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Questetra BPM Suite 中的相关用户之间建立链接关系。

若要配置并测试 Questetra BPM Suite 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Questetra BPM Suite SSO](#configure-questetra-bpm-suite-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Questetra BPM Suite 测试用户](#create-questetra-bpm-suite-test-user)** - 在 Questetra BPM Suite 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Questetra BPM Suite”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<subdomain>.questetra.net/`

    b. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.questetra.net/saml/SSO/alias/bpm` 

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符和登录 URL 更新这些值。 可以从 **Questetra BPM Suite** 公司站点的“SP 信息”部分获取这些值（在本教程中的后面部分进行说明），或者联系 [Questetra BPM Suite 客户端支持团队](https://www.questetra.com/contact/)来获取。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Questetra BPM Suite”部分，根据要求复制相应的 URL。 

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

在本部分中，通过授予 B.Simon 访问 Questetra BPM Suite 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Questetra BPM Suite”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-questetra-bpm-suite-sso"></a>配置 Questetra BPM Suite SSO

1. 在其他 Web 浏览器窗口中，以管理员身份登录到 **Questetra BPM Suite** 公司站点。

2. 在顶部菜单中，单击“系统设置”  。 
   
    ![显示从 Questetra BPM Suite 公司站点选择了“系统设置”的屏幕截图。](./media/questetra-bpm-suite-tutorial/settings.png)

3. 若要打开“SingleSignOnSAML”  页，请单击“SSO (SAML)”  。 

    ![显示选择了“SSO (SAML)”的屏幕截图。](./media/questetra-bpm-suite-tutorial/apps.png)

4. 在 **Questetra BPM Suite** 公司站点的“SP 信息”  部分中，执行以下步骤：

    a. 复制“ACS URL”，然后将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“登录 URL”文本框中。   
    
    b. 复制“实体 ID”，然后将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中。   

5. 在 **Questetra BPM Suite** 公司站点上，执行以下步骤： 
   
    ![配置单一登录](./media/questetra-bpm-suite-tutorial/certificate.png)
   
    a. 选择“启用单一登录”  。
   
    b. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。
    
    c. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。
    
    d. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。
    
    e. 在“NameID 格式”  文本框中，键入 `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`。

    f. 在记事本中打开从 Azure 门户下载的 Base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“验证证书”文本框中   。 

    g. 单击“保存”  。

### <a name="create-questetra-bpm-suite-test-user"></a>创建 Questetra BPM Suite 测试用户

本部分的目的是在 Questetra BPM Suite 中创建名为“Britta Simon”的用户。

**若要在 Questetra BPM Suite 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 以管理员身份登录到 Questetra BPM Suite 公司站点。

2. 转到“系统设置”>“用户列表”>“新建用户”  。
 
3. 在“新建用户”对话框中，执行以下步骤： 
   
    ![创建测试用户](./media/questetra-bpm-suite-tutorial/users.png)
   
    a. 在“名称”文本框中，键入用户的 **名称**britta.simon@contoso.com。
   
    b. 在“电子邮件”文本框中，键入用户的 **电子邮件地址**britta.simon@contoso.com。
   
    c. 在“密码”文本框中，键入用户的密码   。
    
    d. 单击“添加新用户”  。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Questetra BPM Suite 登录 URL，你可以从那里启动登录流。 

* 直接转到 Questetra BPM Suite 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的“Questetra BPM Suite”磁贴时，将会重定向到 Questetra BPM Suite 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Questetra BPM Suite 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。