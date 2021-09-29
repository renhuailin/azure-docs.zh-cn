---
title: 教程：Azure Active Directory 与 Dropbox Business 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Dropbox Business 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: 0ec5cbd61a72bdff367acc071d653d03d9459464
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124765469"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>教程：将 Dropbox Business 与 Azure Active Directory 集成

本教程介绍如何将 Dropbox Business 与 Azure Active Directory (Azure AD) 集成。 将 Dropbox Business 与 Azure AD 集成时，可以：

* 在 Azure AD 中控制谁有权访问 Dropbox Business。
* 让用户使用其 Azure AD 帐户自动登录到 Dropbox Business。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Dropbox Business 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

* 本教程在测试环境中配置并测试 Azure AD SSO。 Dropbox Business 支持 SP 发起的 SSO。

* Dropbox Business 支持[自动用户预配和取消预配](dropboxforbusiness-tutorial.md)。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-dropbox-business-from-the-gallery"></a>从库中添加 Dropbox Business

若要配置 Dropbox Business 与 Azure AD 的集成，需要从库中将 Dropbox Business 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Dropbox Business” 。
1. 从结果面板中选择“Dropbox Business”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-dropbox-business"></a>配置并测试 Dropbox Business 的 Azure AD SSO

使用名为 Britta Simon 的测试用户配置和测试 Dropbox Business 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Dropbox Business 相关用户之间建立关联。

若要配置并测试 Dropbox Business 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。    
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[配置 Dropbox Business SSO](#configure-dropbox-business-sso)** - 在应用程序端配置单一登录。
    1. **[创建 Dropbox Business 测试用户](#create-dropbox-business-test-user)** - 在 Dropbox Business 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Dropbox Business”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://www.dropbox.com/sso/<id>` 
    
     b. 在“标识符(实体 ID)”文本框中，键入值：`Dropbox`
    
    c. 在“回复 URL”字段中，输入 `https://www.dropbox.com/saml_login`
    > [!NOTE]
    > Dropbox Sign SSO ID 可在 Dropbox 站点找到：“Dropbox”>“管理控制台”>“设置”>“单一登录”>“SSO 登录 URL”。

1. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Dropbox Business”部分中，根据要求复制相应的 URL。

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

本部分将通过授予 B.Simon 访问 Dropbox Business 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Dropbox Business”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-dropbox-business-sso"></a>配置 Dropbox Business SSO

1. 若要在 Dropbox Business 中自动完成配置，需要单击“安装扩展”安装“我的应用安全登录浏览器扩展” 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“安装 Dropbox Business”会定向到 Dropbox Business 应用程序。 在此处，请提供用于登录到 Dropbox Business 的管理员凭据。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-8。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Dropbox Business，请打开新的 Web 浏览器窗口，转到 Dropbox Business 租户并登录。 然后执行以下步骤：

    ![显示“Dropbox Business 登录”页的屏幕截图。](./media/dropboxforbusiness-tutorial/account.png "配置单一登录")

4. 单击“用户”图标并选择“设置”选项卡。

    ![显示已选择“用户图标”操作和“设置”的屏幕截图。](./media/dropboxforbusiness-tutorial/configure-1.png "配置单一登录")

5. 在左侧导航窗格中，单击“管理控制台”。

    ![显示已选择“管理控制台”的屏幕截图。](./media/dropboxforbusiness-tutorial/configure-2.png "配置单一登录")

6. 在“管理控制台”上，单击左侧导航窗格中的“设置”。

    ![显示已选择“设置”的屏幕截图。](./media/dropboxforbusiness-tutorial/configure-3.png "配置单一登录")

7. 选择“身份验证”部分下的“单一登录”选项 。

    ![显示“身份验证”部分的屏幕截图，其中选择了“单一登录”。](./media/dropboxforbusiness-tutorial/configure-4.png "配置单一登录")

8. 在“单一登录”部分中，执行以下步骤：  

    ![显示“单一登录”配置设置的屏幕截图。](./media/dropboxforbusiness-tutorial/configure-5.png "配置单一登录")

    a. 在“单一登录”的下拉列表中选择“必选”选项 。

    b. 单击“添加单一 URL”，在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户中复制的“登录 URL”值，然后选择“完成”   。

    ![配置单一登录](./media/dropboxforbusiness-tutorial/sso.png "配置单一登录")

    c. 单击“上传证书”，然后浏览至从 Azure 门户中下载的“Base64 编码的证书文件” 。

    d. 单击“复制链接”，并将复制的值粘贴到 Azure 门户上“Dropbox Business 域和 URL”部分的“登录 URL”文本框中  。

    e. 单击“ **保存**”。

### <a name="create-dropbox-business-test-user"></a>创建 Dropbox Business 测试用户

在本部分，你将在 Dropbox Business 中创建名为 B.Simon 的用户。 Dropbox Business 支持默认情况下启用的实时用户预配。 此部分不存在任何操作项。 如果 Dropbox Business 中尚不存在用户，则会在身份验证后创建一个新用户。

>[!Note]
>如需手动创建用户，请联系 [Dropbox Business 客户端支持团队](https://www.dropbox.com/business/contact)

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Dropbox Business 登录 URL，可在其中启动登录流。 

* 直接转到 Dropbox Business 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Dropbox Business 磁贴时，将会重定向到 Dropbox Business 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Dropbox Business 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。