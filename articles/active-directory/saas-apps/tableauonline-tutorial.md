---
title: 教程：Azure Active Directory 与 Tableau Online 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Tableau Online 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 5d3d74c0e29de6ee75f80ab26c982c92501dafa2
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181679"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Tableau Online 集成

本教程介绍如何将 Tableau Online 与 Azure Active Directory (Azure AD) 集成。 将 Tableau Online 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Tableau Online。
* 允许用户使用其 Azure AD 帐户自动登录到 Tableau Online。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Tableau Online 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Tableau Online 支持 **SP** 发起的 SSO
* Tableau Online 支持[自动用户预配和取消预配](tableau-online-provisioning-tutorial.md)（推荐）。

## <a name="adding-tableau-online-from-the-gallery"></a>从库中添加 Tableau Online

要配置 Tableau Online 与 Azure AD 的集成，需要从库中将 Tableau Online 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Tableau Online”   。
1. 从结果面板中选择“Tableau Online”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>配置并测试 Tableau Online 的 Azure AD SSO

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Tableau Online 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Tableau Online 相关用户之间建立链接关系。

若要配置并测试 Tableau Online 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Tableau Online SSO](#configure-tableau-online-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Tableau Online 测试用户](#create-tableau-online-test-user)** - 在 Tableau Online 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Tableau Online”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，键入 URL：`https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. 在“标识符(实体 ID)”文本框中，键入 URL：`https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>` 

    > [!NOTE]
    > 本教程的“设置 Tableau Online”部分会提供 `<entityid>` 值。  实体 ID 值是“设置 Tableau Online”部分提供的“Azure AD 标识符”值。  

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Tableau Online”部分，根据要求复制相应的 URL。 

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

在本部分中，将通过授予 B.Simon 访问 Tableau Online 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Tableau Online”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-tableau-online-sso"></a>配置 Tableau Online SSO

1. 若要在“Tableau Online”中自动执行配置，需要单击“安装扩展”来安装“我的应用安全登录”浏览器扩展 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Tableau Online”会将你定向到 Tableau Online 应用程序。 在此处，提供管理员凭据以登录到 Tableau Online。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Tableau Online，请在另一个 Web 浏览器窗口中，以管理员身份登录到 Tableau Online 公司站点。

1. 转到“设置”  ，然后到“身份验证”  。

    ![屏幕截图显示从“设置”菜单选择了“身份验证”。](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. 若要启用 SAML，请在“身份验证类型”部分下，  依次选中“启用附加的身份验证方法”、“SAML”复选框。  

    ![屏幕截图显示了“身份验证类型”部分，可在其中选择值。](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. 向下滚动到“将元数据文件导入 Tableau Online”部分。   单击“浏览”并导入已从 Azure AD 下载的元数据文件。 然后，单击“应用”  。

   ![屏幕截图显示了可在其中导入元数据文件的部分。](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. 在“匹配断言”  部分中，为 **电子邮件地址**、**名字** 和 **姓氏** 插入相应的标识提供者断言名称。 若要从 Azure AD 中获取此信息，请执行以下操作： 
  
    a. 在 Azure 门户中，转到“Tableau Online”  应用程序集成页。

    b. 在“用户属性和声明”部分，单击编辑图标  。

   ![屏幕截图显示了“用户属性和声明”部分，可在其中选择编辑图标。](./media/tableauonline-tutorial/attributesection.png)

    c. 通过执行以下步骤复制以下属性的命名空间值：givenname、email 和 surname：

   ![屏幕截图显示了 Givenname、Surname 和 Emailaddress 属性。](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. 单击“user.givenname”  值

    e. 复制“命名空间”文本框中的值。 

    ![屏幕截图显示了“管理用户声明”部分，可在其中输入命名空间。](./media/tableauonline-tutorial/attributesection2.png)

    f. 若要复制 email 和 surname 的命名空间值，请重复上述步骤。

    g. 切换到 Tableau Online 应用程序，然后按如下所示设置“用户属性和声明”部分： 

    * 电子邮件：**mail** 或 **userprincipalname**

    * 名字：**givenname**

    * 姓氏：**surname**

    ![屏幕截图显示了“匹配属性”部分，可在其中输入值。](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>创建 Tableau Online 测试用户

在本部分中，会在 Tableau Online 中创建一个名为“Britta Simon”的用户。

1. 在“Tableau Online”  上，单击“设置”  ，然后单击“身份验证”  部分。 向下滚动到“管理用户”部分。  依次单击“添加用户”、“输入电子邮件地址”。  
  
    ![屏幕截图显示了“管理用户”部分，可在其中选择“添加用户”。](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. 选择“添加用户用于(SAML)身份验证”。  在“输入电子邮件地址”文本框中添加 britta.simon\@contoso.com 
  
    ![屏幕截图显示了“添加用户”页，可在其中输入电子邮件地址。](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. 单击“添加用户”。 

### <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Tableau Online 登录 URL，你可以在其中启动登录流。

* 直接转到 Tableau Online 登录 URL，并在其中启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Tableau Online 磁贴时，会重定向到 Tableau Online 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Tableau Online 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)