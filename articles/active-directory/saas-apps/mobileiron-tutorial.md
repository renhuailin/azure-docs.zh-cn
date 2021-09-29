---
title: 教程：Azure Active Directory 与 MobileIron 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 MobileIron 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: b44e6eae96ac2a0d9a3ce4bb72043b5e24dfe9db
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780350"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>教程：Azure Active Directory 与 MobileIron 集成

 本教程将介绍如何将 MobileIron 与 Azure Active Directory (Azure AD) 集成。 将 MobileIron 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 MobileIron。
* 让用户使用其 Azure AD 帐户自动登录到 MobileIron。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 MobileIron 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* MobileIron 支持 SP 和 IDP 发起的 SSO。

## <a name="add-mobileiron-from-the-gallery"></a>从库添加 MobileIron

要配置 MobileIron 与 Azure AD 的集成，需要从库中将 MobileIron 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧窗格中，选择“Azure Active Directory”。
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“MobileIron” 。
1. 在结果中选择 MobileIron，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>为 MobileIron 配置并测试 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 MobileIron 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 MobileIron 中的相关用户之间建立链接关系。

若要配置并测试 MobileIron 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
     1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. **[配置 MobileIron SSO](#configure-mobileiron-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 MobileIron 测试用户](#create-mobileiron-test-user)** - 在 MobileIron 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分，你将在 Azure 门户中启用 Azure AD SSO。
 
1. 在 Azure 门户中的 MobileIron 应用程序集成页上，找到“管理”部分，然后选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”。 
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔图标以编辑设置   。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://www.MobileIron.com/<key>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

     在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 可从 MobileIron 的管理门户中获取该密钥和主机的值，本教程稍后会做介绍。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

在本部分，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。   
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下密码  。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 MobileIron 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  
1. 在应用程序列表中，选择“MobileIron”。
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组”   。
1. 选择“添加用户”。 然后，在“添加分配”对话框中选择“用户和组”。  
1. 在“用户和组”对话框中，从用户列表中选择“B.Simon”。   然后选择屏幕底部的“选择”  。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中选择“分配”。

## <a name="configure-mobileiron-sso"></a>配置 MobileIron SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 MobileIron 公司站点。

2. 转到“管理员”   > “标识”  ，然后在“有关云 IDP 设置的信息”字段中选择“AAD”选项。  

    ![屏幕截图显示了 MobileIron 站点的“管理”选项卡，其中选择了“标识”。](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. 复制 **密钥** 和 **主机** 的值，并在 Azure 门户的“基本 SAML 配置”  部分中粘贴它们以完成 URL。

    ![屏幕截图显示了“设置 SAML”选项以及密钥和主机值。](./media/MobileIron-tutorial/key.png)

4. 在“从 AAD 导出元数据文件并导入到 MobileIron 云字段”中，单击“选择文件”从 Azure 门户上传已下载的元数据。   上传完成后，单击“完成”。 

    ![配置单一登录管理元数据按钮](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>创建 MobileIron 测试用户

为了使 Azure AD 用户能够登录到 MobileIron，必须将其预配到 MobileIron 中。  
就 MobileIron 来说，预配任务需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 MobileIron 公司站点。

1. 转到“用户”，单击“添加” > “单一用户”。

    ![配置单一登录用户按钮](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. 在“单个用户”  对话框页上，执行以下步骤：

    ![配置单一登录用户添加按钮](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. 在“电子邮件地址”文本框中，输入用户的电子邮件地址，例如  brittasimon@contoso.com。

    b. 在“名字”文本框中，输入用户的名字，例如 Britta  。

    c. 在“姓氏”文本框中，输入用户的姓氏，例如 Simon  。

    d. 单击“完成”  。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 MobileIron 登录 URL，可在其中启动登录流。  

* 直接转到 MobileIron 登录 URL，并从那里启动登录流。

### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 MobileIron。

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 MobileIron 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 MobileIron。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。


## <a name="next-steps"></a>后续步骤

配置 MobileIron 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。