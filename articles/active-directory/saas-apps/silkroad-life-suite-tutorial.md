---
title: 教程：Azure Active Directory 与 SilkRoad Life Suite 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 SilkRoad Life Suite 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2021
ms.author: jeedes
ms.openlocfilehash: ca117d87a3a2e712a6d23847ea4891063bb38bd9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124775828"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>教程：Azure Active Directory 与 SilkRoad Life Suite 集成

本教程介绍如何将 SilkRoad Life Suite 与 Azure Active Directory (Azure AD) 集成。 将 SilkRoad Life Suite 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SilkRoad Life Suite。
* 让用户使用其 Azure AD 帐户自动登录到 SilkRoad Life Suite。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 SilkRoad Life Suite 的集成，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 SilkRoad Life Suite 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SilkRoad Life Suite 支持 SP 发起的 SSO。

## <a name="add-silkroad-life-suite-from-the-gallery"></a>从库中添加 SilkRoad Life Suite

要配置 SilkRoad Life Suite 与 Azure AD 的集成，需要从库中将 SilkRoad Life Suite 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“SilkRoad Life Suite” 。
1. 从结果面板中选择“SilkRoad Life Suite”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-silkroad-life-suite"></a>配置并测试 SilkRoad Life Suite 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 SilkRoad Life Suite 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 SilkRoad Life Suite 中的相关用户之间建立关联。

若要配置并测试 SilkRoad Life Suite 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 SilkRoad Life Suite SSO](#configure-silkroad-life-suite-sso) - 在应用程序端配置单一登录设置。
    1. [创建 SilkRoad Life Suite 测试用户](#create-silkroad-life-suite-test-user) - 在 SilkRoad Life Suite 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 SilkRoad Life Suite 应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分，如果有 **服务提供程序元数据文件**，请执行以下步骤：

    > [!NOTE]
    > 你将获取本教程中稍后介绍的 **服务提供程序元数据文件**。

    a. 单击“上传元数据文件”  。

    ![屏幕截图显示了“基本 SAML 配置”，其中包含“上传元数据文件”链接。](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![屏幕截图显示了可在其中选择并上传文件的对话框。](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“基本 SAML 配置”部分。

    > [!Note]
    > 如果“标识符”和“回复 URL”值未自动填充，请根据需求手动填充这些值。

    d. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/`。

5. 在“基本 SAML 配置”  部分中，如果你没有 **服务提供程序元数据文件**，请执行以下步骤：

    a. 在“标识符”框中，使用以下模式之一键入 URL：

    | 标识符 URL |
    |-----|
    |`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/SP`|
    |`https://<SUBDOMAIN>.silkroad.com/Authentication/SP`|
    

    b. 在“回复 URL”文本框中，使用以下模式之一键入 URL：

    | 回复 URL |
    |-----|
    |`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/`|
    |`https://<SUBDOMAIN>.silkroad.com/Authentication/`|

    c. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/`。

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [SilkRoad Life Suite 客户端支持团队](https://www.silkroad.com/locations/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 SilkRoad Life Suite”  部分中，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 SilkRoad Life Suite 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“SilkRoad Life Suite”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-silkroad-life-suite-sso"></a>配置 SilkRoad Life Suite SSO

1. 以管理员身份登录到你的 SilkRoad 公司站点。

    > [!NOTE]
    > 若要获取对 SilkRoad Life Suite 身份验证应用程序的访问权限以配置 Microsoft Azure AD 的联合身份验证，请联系 SilkRoad 支持或 SilkRoad 服务代表。

1. 转到“服务提供商”  ，并单击“联合身份验证详细信息”  。

    ![屏幕截图显示从“服务提供商”中选择的“联合身份验证详细信息”。](./media/silkroad-life-suite-tutorial/details.png)

1. 单击“下载联合元数据”  ，并在计算机上保存该元数据文件。 在 Azure 门户的“基本 SAML 配置”部分中，使用所下载的联合元数据作为 **服务提供程序元数据文件**。

    ![屏幕截图显示“下载联合元数据”链接。](./media/silkroad-life-suite-tutorial/metadata.png)

1. 在 **SilkRoad** 应用程序中，单击“身份验证源”  。

    ![屏幕截图显示选择的“身份验证源”。](./media/silkroad-life-suite-tutorial/sources.png) 

1. 单击“添加身份验证源”  。

    ![屏幕截图显示“添加身份验证源”链接。](./media/silkroad-life-suite-tutorial/add-source.png)

1. 在“添加身份验证源”  部分中，执行以下步骤：

    ![屏幕截图显示包含已选择的“使用文件数据创建标识提供者”按钮的“添加身份验证源”。](./media/silkroad-life-suite-tutorial/metadata-file.png)
  
    a. 在“选项 2 - 元数据文件”  下，单击“浏览”  上传从 Azure 门户下载的元数据文件。
  
    b. 单击“使用文件数据创建标识提供者”  。

1. 在“身份验证源”  部分中，单击“编辑”  。

    ![屏幕截图显示包含已选择的“编辑”选项的“身份验证源”。](./media/silkroad-life-suite-tutorial/edit-source.png)

1. 在“编辑身份验证源”  对话框中，执行以下步骤：

    ![屏幕截图显示“编辑身份验证源”对话框，你可以在其中输入所述的值。](./media/silkroad-life-suite-tutorial/authentication.png)

    a. 对于“启用”  ，请选择“是”  。

    b. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。  

    c. 在“IdP 说明”  文本框中，键入配置说明（例如：**Azure AD SSO**）。

    d. 在“元数据文件”  文本框中，上传从 Azure 门户下载的 **元数据** 文件。
  
    e. 在“IdP 名称”  文本框中，键入特定于配置的名称（例如：*Azure SP*）。
  
    f. 在“注销服务 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    g. 在“登录服务 URL”  文本框中，粘贴从 Azure 门户复制的“登录 URL”  值。

    h. 单击“保存”  。

1. 禁用所有其他身份验证源。

    ![屏幕截图显示“身份验证源”，可在其中禁用其他源。 ](./media/silkroad-life-suite-tutorial/manage-source.png)

### <a name="create-silkroad-life-suite-test-user"></a>创建 SilkRoad Life Suite 测试用户

在本部分中，将在 SilkRoad Life Suite 中创建一个名为 Britta Simon 的用户。 与 [SilkRoad Life Suite 客户端支持团队](https://www.silkroad.com/locations/)协作，在 SilkRoad Life Suite 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 SilkRoad Life Suite 登录 URL，你可以从那里启动登录流。 

* 直接转到 SilkRoad Life Suite 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 SilkRoad Life Suite 磁贴时，将会重定向到 SilkRoad Life Suite 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 SilkRoad Life Suite 后，可以强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。