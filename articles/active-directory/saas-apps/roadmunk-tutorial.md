---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Roadmunk 的集成
description: 了解如何在 Azure Active Directory 和 Roadmunk 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: f0c139ac775cf0761e1866853c959d7e98b20cf3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124821764"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Roadmunk 的集成

本教程介绍如何将 Roadmunk 与 Azure Active Directory (Azure AD) 集成。 将 Roadmunk 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Roadmunk。
* 让用户使用其 Azure AD 帐户自动登录 Roadmunk。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个启用了单一登录 (SSO) 的 Roadmunk 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

Roadmunk 支持由服务提供商 (SP) 和标识提供者 (IDP) 启动的 SSO 。

## <a name="add-roadmunk-from-the-gallery"></a>从库中添加 Roadmunk

若要将 Roadmunk 集成到 Azure AD，请从库中将 Roadmunk 添加到托管 SaaS 应用列表：

1. 使用工作或学校帐户或者个人 Microsoft 帐户登录到 Azure 门户。
1. 在左窗格中选择“Azure Active Directory”。
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 Roadmunk 。
1. 在结果中选择 Roadmunk，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>配置并测试 Roadmunk 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Roadmunk 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Roadmunk 中的相关用户之间建立链接关系。

以下概述了如何使用 Roadmunk 配置和测试 Azure AD SSO：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。
    1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以使用 B.Simon 用户测试 Azure AD SSO。
    1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够使用 Azure AD SSO。
1. [配置 Roadmunk SSO](#configure-roadmunk-sso)，以在应用程序端配置 SSO 设置。
    1. [创建 Roadmunk 测试用户](#create-roadmunk-test-user)，以将 Roadmunk 中 B.Simon 用户的对应用户链接到该用户的 Azure AD 表示形式。
1. [测试 SSO](#test-sso)，确保配置正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO：

1. 在 Azure 门户中的 Roadmunk 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形图标以编辑设置   。

   ![屏幕截图显示了基本 SAML 配置的“编辑”图标。](common/edit-urls.png)

1. 如果已获取 SP 元数据文件并想要在 IDP 发起的模式下进行配置，请在“基本 SAML 配置”部分执行以下步骤：

    a. 选择“上传元数据文件”。

    ![屏幕截图显示了“上传元数据文件”的链接。](common/upload-metadata.png)

    b. 选择文件夹图标，以选择在“配置 Roadmunk SSO”过程的步骤 4 中下载的元数据文件。 然后，选择“上传”。

    ![屏幕截图显示了如何选择元数据文件。](common/browse-upload-metadata.png)

    上传元数据文件后，“基本 SAML 配置”部分中的“标识符”和“回复 URL”值将自动填充  。

    ![屏幕截图显示了“基本 SAML 配置”部分。 突出显示了“标识符”字段和“回复 URL”字段。](common/idp-intiated.png)

    > [!Note]
    > 如果未自动填充“标识符”和“回复 URL”，请手动输入值 。

1. 如果要在 SP 发起的模式下配置应用程序，选择“设置其他 URL”。 在“登录 URL”字段中，键入 `https://login.roadmunk.com`

    ![屏幕截图显示了在何处设置 SP 发起模式的登录 URL。](common/metadata-upload-additional-signon.png)

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“联合元数据 XML”  。 选择“下载”以下载证书，并将其保存在计算机上。

    ![屏幕截图显示了 SAML 签名证书的下载链接。](common/metadataxml.png)

1. 在“设置 Roadmunk”部分，复制所需的 URL。

    ![屏幕截图显示了在何处复制配置 URL。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，将在 Azure 门户中创建一个测试用户。 将该用户命名为 B.Simon。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。
1. 选择窗口顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，输入 `B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Roadmunk 的权限，允许其使用 Azure SSO。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。 
1. 在应用程序列表中，选择 Roadmunk。
1. 在应用的概述页中，找到“管理”部分，然后选择“用户和组” 。
1. 选择“添加用户”。  然后，在“添加分配”对话框中选择“用户和组”   。
1. 在“用户和组”对话框中的“用户”列表内，选择“B.Simon”  。 在对话框底部，选择“选择”。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉菜单中选择该角色。 如果尚未为此应用设置任何角色，则选择“默认访问权限”角色。
1. 在“添加分配”对话框中选择“分配”。

## <a name="configure-roadmunk-sso"></a>配置 Roadmunk SSO

1. 以管理员身份登录到 Roadmunk 网站。

1. 在页面底部，选择用户图标，然后选择“帐户设置”。

    ![屏幕截图显示了在何处选择“用户帐户”选项。](./media/roadmunk-tutorial/account.png)

1. 请转到“公司” > “身份验证设置” 。

1. 在“身份验证设置”页上，执行以下步骤：

    ![屏幕截图显示了“身份验证设置”页。](./media/roadmunk-tutorial/saml-sso.png)

    a. 打开“SAML 单一登录(SSO)”。

    b. 在“步骤 1”部分，上传元数据 XML 文件或提供元数据的 URL。

    c. 在“步骤 2”部分，下载 Roadmunk 元数据文件，然后将其保存到计算机。

    d. 如果要使用 SSO 登录，请在“步骤 3”部分中，选择“仅强制执行 SAML 单一登录” 。

    e. 选择“保存”。 


### <a name="create-roadmunk-test-user"></a>创建 Roadmunk 测试用户

1. 以管理员身份登录到 Roadmunk 网站。

1. 选择页面底部的用户图标，然后选择“帐户设置”。

    ![屏幕截图显示了如何打开测试用户的“帐户设置”。](./media/roadmunk-tutorial/account.png)

1. 打开“用户”选项卡，然后选择“邀请用户” 。

    ![屏幕截图显示了“用户”选项卡。突出显示了“邀请用户”按钮。 打开的窗口中突出显示了“电子邮件”和“角色”字段。](./media/roadmunk-tutorial/create-user.png)

1. 在显示的窗体中，填写所需信息，然后选择“邀请”。


## <a name="test-sso"></a>测试 SSO 

在本部分，我们将使用访问面板测试 Azure AD SSO 配置。

在“我的应用”门户中，选择 Roadmunk 磁贴时，应当会自动登录到为其设置了 SSO 的 Roadmunk 帐户。 有关详细信息，请参阅[从“我的应用”门户登录和启动应用](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Roadmunk 后，可强制实施会话控制。 会话控制可实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 

了解如何[通过使用 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。