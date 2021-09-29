---
title: 教程：Azure Active Directory 与 iLMS 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 iLMS 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: 285fea57252c23776c9d7bc3b172889311356909
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768470"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>教程：将 iLMS 与 Azure Active Directory 集成

本教程介绍如何将 iLMS 与 Azure Active Directory (Azure AD) 集成。 将 iLMS 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 iLMS。
* 让用户使用其 Azure AD 帐户自动登录到 iLMS。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 iLMS 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* iLMS 支持 SP 和 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-ilms-from-the-gallery"></a>从库中添加 iLMS

要配置 iLMS 与 Azure AD 的集成，需要从库中将 iLMS 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“iLMS”   。
1. 从结果面板中选择“iLMS”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-ilms"></a>配置并测试 iLMS 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 iLMS 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 iLMS 相关用户之间建立链接关系。

若要配置并测试 iLMS 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 iLMS SSO](#configure-ilms-sso) - 在应用程序端配置单一登录设置。
    1. [创建 iLMS 测试用户](#create-ilms-test-user) - 在 iLMS 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 iLMS 应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”页中执行以下步骤 ：

    a. 在“标识符”文本框中，粘贴从 iLMS 管理门户上“SAML 设置”的“服务提供程序”部分中复制的“标识符”值    。

    b. 在“回复 URL”文本框中，粘贴从 iLMS 管理门户上“SAML 设置”的“服务提供程序”部分中复制的、采用以下模式的“终结点(URL)”值：`https://www.inspiredlms.com/Login/<INSTANCE_NAME>/consumer.aspx`。  

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，粘贴从 iLMS 管理门户上“SAML 设置”的“服务提供程序”部分中复制的“终结点(URL)”值：`https://www.inspiredlms.com/Login/<INSTANCE_NAME>/consumer.aspx`。  

1. 要启用 JIT 预配，iLMS 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标以打开“用户属性”对话框  。

    > [!NOTE]
    > 必须在 iLMS 中启用“创建不可识别的用户帐户”才能映射这些属性。  遵照[此处](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon)的说明获取有关属性配置的思路。

1. 除了上述属性，iLMS 应用程序还要求在 SAML 响应中传递回更多的属性。 在“用户属性”  对话框的“用户声明”  部分执行以下步骤，以便添加 SAML 令牌属性，如下表所示：

    | 名称 | 源属性|
    | --------|------------- |
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“保存”  。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 iLMS”部分，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `Britta Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`BrittaSimon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 iLMS 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“iLMS”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-ilms-sso"></a>配置 iLMS SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 **iLMS 管理门户**。

2. 单击“设置”选项卡下面的“SSO:SAML”打开“SAML 设置”并执行以下步骤：  

    ![屏幕截图显示 ILMS“设置”选项卡，可在其中选择“SSO:SAML”。](./media/ilms-tutorial/settings.png)

3. 展开“服务提供程序”部分，并复制“标识符”和“终结点(URL)”值。   

    ![屏幕截图显示“SAML 设置”，可从其中获取值。](./media/ilms-tutorial/values.png) 

4. 在“标识提供者”部分下面，单击“导入元数据”。  

5. 在“SAML 签名证书”部分中选择从 Azure 门户下载的“联合元数据”文件   。

    ![屏幕截图显示“SAML 设置”，可在其中选择元数据文件。](./media/ilms-tutorial/certificate.png)

6. 如果想要启用 JIT 预配以便为不可识别的用户创建 iLMS 帐户，请执行以下步骤：

    a. 选中“创建不可识别的用户帐户”。 

    ![屏幕截图显示“创建不可识别的用户帐户”选项。](./media/ilms-tutorial/accounts.png)

    b. 将 Azure AD 中的属性映射到 iLMS 中的属性。 在属性列中，指定属性名称或默认值。

    c. 转到“业务规则”选项卡并执行以下步骤： 

    ![屏幕截图显示“业务规则设置”，可在其中输入此步骤中的信息。](./media/ilms-tutorial/rules.png)

    d. 选中“创建不可识别的区域、分支和部门”，创建单一登录时尚不存在的区域、分支和部门。 

    e. 选中“登录期间更新用户配置文件”，指定是否在每次单一登录时都更新用户的配置文件。 

    f. 如果已选中“更新用户配置文件中非必填字段的空白值”选项，登录时空白的可选配置文件字段也会导致用户的 iLMS 配置文件包含这些字段的空白值  。

    g. 选中“发送错误通知电子邮件”，并输入要向其发送错误通知电子邮件的用户显示的电子邮件内容。 

7. 单击“保存”按钮保存设置。 

    ![屏幕截图显示“保存”按钮。](./media/ilms-tutorial/save.png)

### <a name="create-ilms-test-user"></a>创建 iLMS 测试用户

应用程序支持实时用户预配，且进行身份验证后，会在应用程序中自动创建用户。 如果在 iLMS 管理门户中设置 SAML 配置期间已单击“创建不可识别的用户帐户”复选框，则 JIT 会正常工作。 

如果需要手动创建用户，请执行以下步骤：

1. 以管理员身份登录到 iLMS 公司站点。

2. 单击“用户”选项卡下面的“注册用户”打开“注册用户”页    。

   ![屏幕截图显示 ILMS“设置”选项卡，可在其中选择“注册用户”。](./media/ilms-tutorial/user.png)

3. 在“注册用户”页上执行以下步骤  。

    ![屏幕截图显示“注册用户”页，可在其中输入指定信息。](./media/ilms-tutorial/add-user.png)

    a. 在“名字”文本框中键入名字，例如 Britta。 

    b. 在“姓氏”文本框键入姓氏，例如 Simon。 

    c. 在“电子邮件 ID”文本框中，键入用户的电子邮件地址（例如 BrittaSimon@contoso.com）  。

    d. 在“区域”下拉列表中，选择区域的值。 

    e. 在“分支”下拉列表中，选择分支的值。 

    f. 在“部门”下拉列表中，选择部门的值。 

    g. 单击“ **保存**”。

    > [!NOTE]
    > 选中“发送注册电子邮件”复选框可向用户发送注册邮件。 

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 iLMS 登录 URL，你可以从那里启动登录流。  

* 直接转到 iLMS 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 iLMS。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 单击“我的应用”中的 iLMS 磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则会自动登录到为其设置了 SSO 的 iLMS。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 iLMS 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。