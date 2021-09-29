---
title: 教程：Azure Active Directory 与 M-Files 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 M-Files 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 26ca0b1c4115da14821c1ba8147d571e36104fd6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124790351"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>教程：Azure Active Directory 与 M-Files 集成

本教程介绍如何将 M-Files 与 Azure Active Directory (Azure AD) 进行集成。 将 M-Files 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 M-Files。
* 让用户使用其 Azure AD 帐户自动登录到 M-Files。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 M-Files 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* M-Files 支持 SP 发起的 SSO。

## <a name="add-m-files-from-the-gallery"></a>从库中添加 M-Files

要配置 M-Files 与 Azure AD 的集成，需要从库中将 M-Files 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 M-Files 。
1. 从结果窗格中选择“M-Files”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-m-files"></a>配置并测试 M-Files 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 M-Files 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 M-Files 相关用户之间建立链接关系。

若要配置并测试 M-Files 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 M-Files SSO](#configure-m-files-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 M-Files 测试用户](#create-m-files-test-user)** - 在 M-Files 中创建 B.Simon 的对应用户，并将其链接到该用户在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“M-Files”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [M-Files 客户端支持团队](mailto:support@m-files.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 M-Files”部分中，根据需要复制相应的 URL  。

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

在本部分中，将通过授予 B.Simon 访问 M-Files 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“M-Files”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-m-files-sso"></a>配置 M-Files SSO

1. 若要为应用程序配置 SSO，请联系 [M-Files 支持团队](mailto:support@m-files.com)，并向他们提供下载的元数据。
   
    >[!NOTE]
    >若要为 M-File 桌面应用程序配置 SSO，请执行后续步骤。 如果只希望为 M-Files Web 版本配置 SSO，则不需执行额外的步骤。  

1. 按后续步骤配置 M-File 桌面应用程序，以便通过 Azure AD 启用 SSO。 若要下载 M-Files，请转到 [M-Files 下载](https://www.m-files.com/customers/product-downloads/download-update-links/) 页。

1. 打开“M-Files 桌面设置”窗口。  然后，单击“添加”  。
   
    ![屏幕截图显示了“M-Files 桌面设置”，你可以在其中选择“添加”。](./media/m-files-tutorial/settings.png)

1. 在“文档保管库连接属性”窗口中，执行以下步骤： 
   
    ![屏幕截图显示了“文档保管库连接属性”，你可以在其中输入所述值。](./media/m-files-tutorial/general.png)  

    在“服务器”部分键入如下值：  

    a. 键入 `<tenant-name>.cloudvault.m-files.com` 作为“名称”。 
 
    b. 键入 **4466** 作为“端口号”。 

    c. 选择 **HTTPS** 作为“协议”。 

    d. 在“身份验证”字段中，选择“特定 Windows 用户”。   然后，会出现签名页提示。 插入 Azure AD 凭据。 

    e. 对于“服务器上的保管库”，请选择服务器上的相应保管库。 
 
    f. 单击“确定”。

### <a name="create-m-files-test-user"></a>创建 M-Files 测试用户

本部分的目的是在 M-Files 中创建名为 Britta Simon 的用户。 请与 [M-Files 支持团队](mailto:support@m-files.com)协作，在 M-Files 中添加用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这样将会重定向到 M-Files 登录 URL，可在其中启动登录流。 

* 直接转到 M-Files 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 M-Files 磁贴时，会重定向到 M-Files 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 M-Files 后，可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。