---
title: 教程：Azure Active Directory 与 RingCentral 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 RingCentral 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: 6f18ebeb71f597bc7988c8f97840ac1a1e38ebb9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124830319"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>教程：将 RingCentral 与 Azure Active Directory 集成

本教程介绍如何将 RingCentral 与 Azure Active Directory (Azure AD) 集成。 将 RingCentral 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 RingCentral。
* 让用户使用其 Azure AD 帐户自动登录到 RingCentral。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 RingCentral 订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* RingCentral 支持 IDP 发起的 SSO。

* RingCentral 支持[自动用户预配](ringcentral-provisioning-tutorial.md)。

## <a name="add-ringcentral-from-the-gallery"></a>从库添加 RingCentral

若要配置 RingCentral 与 Azure AD 的集成，需要从库中将 RingCentral 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“RingCentral” 。
1. 从结果面板中选择“RingCentral”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-ringcentral"></a>配置并测试 RingCentral 的 Azure AD SSO

使用名为 Britta Simon 的测试用户配置和测试 RingCentral 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 RingCentral 相关用户之间建立链接关系。

若要配置并测试 RingCentral 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 RingCentral SSO](#configure-ringcentral-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 RingCentral 测试用户](#create-ringcentral-test-user)** - 在 RingCentral 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“RingCentral”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，如果有 **服务提供程序元数据文件**，请执行以下步骤：

    1. 单击“上传元数据文件”。
    1. 单击“文件夹徽标”来选择元数据文件并单击“上传”。
    1. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“基本 SAML 配置”部分  。

    > [!Note]
    > 请在本教程稍后将介绍的“RingCentral SSO 配置”页面上获取 **服务提供程序元数据文件**。

1. 如果没有 **服务提供程序元数据文件**，请输入以下字段的值：

    a. 在“标识符”文本框中，键入以下 URL 之一：
  
    | 标识符 |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. 在“回复 URL”文本框中，键入以下 URL 之一：

    | 回复 URL |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分中，你将通过授予 B.Simon 访问 RingCentral 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“RingCentral”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-ringcentral-sso"></a>配置 RingCentral SSO

1. 若要在 RingCentral 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展” 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 RingCentral”，此时系统会将你定向到 RingCentral 应用程序。 请在此处提供管理员凭据以登录到 RingCentral。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 RingCentral，请打开新的 Web 浏览器窗口，以管理员身份登录到 RingCentral 公司站点，并执行以下步骤：

1. 在顶部单击“工具”。

    ![显示从 RingCentral 公司站点选择了“工具”的屏幕截图。](./media/ringcentral-tutorial/ringcentral-1.png)

1. 导航到“单一登录”。

    ![显示从“工具”菜单选择了“单一登录”的屏幕截图。](./media/ringcentral-tutorial/ringcentral-2.png)

1. 在“单一登录”页的“SSO 配置”部分，在“步骤 1”中单击“编辑”，执行以下步骤： 

    ![显示“SSO 配置”页的屏幕截图，可在其中选择“编辑”。](./media/ringcentral-tutorial/ringcentral-3.png)

1. 在“设置单一登录”页上，执行以下步骤：

    ![显示“设置单一登录”页的屏幕截图，可在其中上传 IDP 元数据。](./media/ringcentral-tutorial/ringcentral-4.png)

    a. 单击“浏览”，上传从 Azure 门户下载的元数据文件。

    b. 上传元数据以后，值会自动填充在“SSO 常规信息”部分中。

    c. 在“属性映射”部分，针对“将电子邮件属性映射到”选项选择 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` 

    d. 单击“ **保存**”。

    e. 在“步骤 2”中单击“下载”以下载“服务提供程序元数据文件”，并在“基本 SAML 配置”部分上传该文件，以便在 Azure 门户中自动填充“标识符”和“回复 URL”值     。

    ![显示“SSO 配置”页的屏幕截图，可在其中选择“下载”。](./media/ringcentral-tutorial/ringcentral-6.png) 

    f. 在同一页导航到“启用 SSO”部分，执行以下步骤：

    ![显示“启用 SSO”部分的屏幕截图，可在其中完成配置。](./media/ringcentral-tutorial/ringcentral-5.png)

    * 选择“启用 SSO 服务”。

    * 选择“允许用户使用 SSO 或 RingCentral 凭据登录”。

    * 单击“ **保存**”。

### <a name="create-ringcentral-test-user"></a>创建 RingCentral 测试用户

在本部分，请在 RingCentral 中创建一个名为“Britta Simon”的用户。 请与 [RingCentral 客户端支持团队](https://success.ringcentral.com/RCContactSupp)协作，在 RingCentral 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

RingCentral 还支持自动用户预配；有关如何配置自动用户预配的更多详细信息，请参阅[此处](./ringcentral-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应该会自动登录到为其设置了 SSO 的 RingCentral。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“RingCentral”磁贴时，你应该会自动登录到为其设置了 SSO 的 RingCentral。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

在配置 RingCentral 后，即可强制实施会话控制，从而实时防止组织的敏感数据遭到外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。