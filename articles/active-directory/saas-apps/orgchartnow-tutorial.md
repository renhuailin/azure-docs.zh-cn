---
title: 教程：Azure Active Directory 与 OrgChart Now 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 OrgChart Now 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: e6366ba21935e2551a43e6ee9de79ca74f87a7fe
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778772"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>教程：Azure Active Directory 与 OrgChart Now 集成

在本教程中，你将了解如何将 OrgChart Now 与 Azure Active Directory (Azure AD) 集成。 将 OrgChart Now 与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 OrgChart Now。
* 让用户使用其 Azure AD 帐户自动登录到 OrgChart Now。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 OrgChart Now 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* OrgChart Now 支持 SP 和 IDP 发起的 SSO 。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-orgchart-now-from-the-gallery"></a>从库中添加 OrgChart Now

要配置 OrgChart Now 与 Azure AD 的集成，需要从库中将 OrgChart Now 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“OrgChart Now” 。
1. 从结果面板中选择“OrgChart Now”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-orgchart-now"></a>配置并测试 OrgChart Now 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 OrgChart Now 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 OrgChart Now 中的相关用户之间建立关联。

若要配置并测试 OrgChart Now 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 OrgChart Now SSO](#configure-orgchart-now-sso) - 在应用程序端配置单一登录设置。
    1. [创建 OrgChart Now 测试用户](#create-orgchart-now-test-user) - 在 OrgChart Now 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO 

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“OrgChart Now”应用程序集成页面上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    在“标识符”文本框中键入 URL：`https://sso2.orgchartnow.com`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` 是从“设置 OrgChart Now”部分（本教程稍后会介绍）复制的“Azure AD 标识符” 。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 OrgChart Now”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B. Simon 访问 OrgChart Now 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“OrgChart Now”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-orgchart-now-sso"></a>配置 OrgChart Now SSO

若要在 **OrgChart Now** 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [OrgChart Now 支持团队](mailto:ocnsupport@officeworksoftware.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-orgchart-now-test-user"></a>创建 OrgChart Now 测试用户

若要让 Azure AD 用户登录 OrgChart Now，必须将其预配到 OrgChart Now 中。 

1. OrgChart Now 支持在默认情况下启用的实时预配。 尝试访问 OrgChart Now 期间，如果尚不存在用户，则会创建一个新用户。 仅当 SSO 请求来自已识别的 IDP，并且在用户列表中未找到 SAML 断言中的电子邮件时，实时用户预配功能才会创建 **只读** 用户。 对于此自动预配功能，需要在 OrgChart Now 中创建标题为“常规”的访问组。 请遵循以下步骤创建访问组：

    a. 单击 UI 右上角的 **齿轮** 图标后，转到“管理组”选项。

      ![OrgChart Now 组](./media/orgchartnow-tutorial/groups.png)   

    b. 选择“添加”图标，将组命名为“常规”，然后单击“确定”。 

      ![OrgChart Now 添加](./media/orgchartnow-tutorial/general.png)

    c. 选择常规用户或只读用户能够访问的文件夹：
    
      ![OrgChart Now 文件夹](./media/orgchartnow-tutorial/folders.png)

    d. **锁定** 文件夹，以便只有管理员用户可以修改它们。 然后按“确定”。

      ![OrgChart Now 锁定](./media/orgchartnow-tutorial/lock.png)

2. 若要创建 **管理员** 用户和 **读/写** 用户，必须手动创建用户，以通过 SSO 获取对其特权级别的访问。 若要预配用户帐户，请执行以下步骤：

    a. 以安全管理员身份登录到 OrgChart Now。

    b. 单击右上角的“设置”，然后导航到“管理用户”。

      ![OrgChart Now 设置](./media/orgchartnow-tutorial/settings.png)

    c. 单击“添加”并执行以下步骤：

      ![OrgChart Now 管理](./media/orgchartnow-tutorial/manage-users.png)

    1. 在“用户 ID”文本框中输入用户 ID，例如 **brittasimon\@contoso.com**。

    1. 在“电子邮件地址”文本框中，输入用户的电子邮件地址，例如 brittasimon\@contoso.com 。

    1. 单击“添加”  。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 OrgChart Now 登录 URL，你可以从那里启动登录流。  

* 直接转到 OrgChart Now 登录 URL，并从中启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 OrgChart Now。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“OrgChart Now”磁贴时，如果该应用程序是在 SP 模式下配置的，则你会重定向到应用程序登录页来启动登录流；如果它是在 IDP 模式下配置的，则你应会自动登录到为其设置了 SSO 的 OrgChart Now。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 OrgChart Now 后，即可强制实施会话控制，从而实时保护组织的敏感数据以防外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。