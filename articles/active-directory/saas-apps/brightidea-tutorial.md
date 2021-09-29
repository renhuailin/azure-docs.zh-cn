---
title: 教程：Azure Active Directory 与 Brightidea 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Brightidea 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2021
ms.author: jeedes
ms.openlocfilehash: eca1b80a8e4eca2834d563a630c7cad9b575b652
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124749968"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>教程：Azure Active Directory 与 Brightidea 的集成

本教程介绍如何将 Brightidea 与 Azure Active Directory (Azure AD) 集成。 将 Brightidea 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Brightidea。
* 让用户使用其 Azure AD 帐户自动登录到 Brightidea。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Brightidea 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Brightidea 支持 SP 和 IDP 发起的 SSO。
* Brightidea 支持实时用户预配。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-brightidea-from-the-gallery"></a>从库中添加 Brightidea

若要配置 Brightidea 与 Azure AD 的集成，需要从库中将 Brightidea 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Brightidea” 。
1. 在结果面板中选择“Brightidea”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-brightidea"></a>配置并测试 Brightidea 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Brightidea 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Brightidea 相关用户之间建立关联。

若要配置并测试 Brightidea 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Brightidea SSO](#configure-brightidea-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Brightidea 测试用户](#create-brightidea-test-user)** - 在 Brightidea 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 Brightidea 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果已获取“服务提供商元数据文件”并想要在“IDP”发起的模式下进行配置，请在“基本 SAML 配置”部分执行以下步骤    ：

    a. 单击“上传元数据文件”  。

    ![上传元数据文件](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![选择元数据文件](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“Brightidea”部分的文本框中：  

    > [!Note]
    > 如果“标识符”和“回复 URL”值未自动填充，请根据要求手动填充这些值。  

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.brightidea.com`

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Brightidea”部分，根据要求复制相应的 URL  。

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

本部分中将通过授予 B.Simon 访问 Brightidea 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中，选择“Brightidea”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-brightidea-sso"></a>配置 Brightidea SSO

1. 在另一个 Web 浏览器窗口中，使用管理员凭据登录到 Brightidea。

2. 若要在 Brightidea 系统中使用 SSO 功能，请导航到“企业设置” -> “身份验证”选项卡。   此时会看到两个子选项卡：“身份验证选择”和“SAML 配置文件”。

    ![屏幕截图显示 Brightidea 站点，其中“身份验证”选项卡处于选中状态。](./media/brightidea-tutorial/authentication.png)

3. 选择“身份验证选择”。  默认只会显示两种标准方法：Brightidea 登录和注册。 添加 SSO 方法后，它会显示在列表中。

    ![屏幕截图显示 Brightidea“身份验证”选项卡，其中“身份验证选择”处于选中状态。](./media/brightidea-tutorial/selection.png)

4. 选择“SAML 配置文件”并执行以下步骤  ：

    ![屏幕截图显示 Brightidea“身份验证”选项卡，其中“SAML 配置文件”处于选中状态，该选项卡提供“下载元数据”和“新增”选项。](./media/brightidea-tutorial/profile.png)

    a. 单击“下载元数据”，然后在 Azure 门户中的“基本 SAML 配置”部分上传。  

    b. 单击“标识提供者设置”下的“新增”按钮并执行以下步骤：  

    ![屏幕截图显示 Brightidea“标识提供者设置”，可在其中输入信息。](./media/brightidea-tutorial/metadata.png)

   * 输入“SAML 配置文件名称”，例如 `Azure Ad SSO` 

   * 对于“上传元数据”，请单击“选择文件”，然后上传从 Azure 门户下载的元数据文件。 

     > [!NOTE]
     > 上传元数据文件后，剩余的字段“单一登录服务”、“标识提供者颁发者”和“上传公钥”将自动填充。 

   * 在“电子邮件”文本框中，输入 `mail` 作为值。 

   * 在“屏幕名称”文本框中，输入 `givenName` 作为值。 

   * 单击 **“保存更改”** 。 

### <a name="create-brightidea-test-user"></a>创建 Brightidea 测试用户

在本部分，我们将在 Brightidea 中创建名为 Britta Simon 的用户。 Brightidea 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Brightidea 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Brightidea 登录 URL，可在其中启动登录流。  

* 直接转到 Brightidea 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你会自动登录到为其设置了 SSO 的 Brightidea。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Brightidea 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Brightidea。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Brightidea 后，可以强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。