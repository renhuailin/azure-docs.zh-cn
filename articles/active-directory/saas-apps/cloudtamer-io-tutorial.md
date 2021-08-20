---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 cloudtamer.io 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 cloudtamer.io 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/21/2021
ms.author: jeedes
ms.openlocfilehash: 407a6284c46eb6eef4057d98cef0f5e86a38bcea
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601824"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudtamerio"></a>教程：Azure Active Directory 单一登录 (SSO) 与 cloudtamer.io 集成

在本教程中，你将了解如何将 cloudtamer.io 与 Azure Active Directory (Azure AD) 集成。 将 cloudtamer.io 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 cloudtamer.io。
* 让用户使用其 Azure AD 帐户自动登录到 cloudtamer.io。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 cloudtamer.io 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* cloudtamer.io 支持 SP 和 IDP 发起的 SSO。
* cloudtamer.io 支持“实时”用户预配。


## <a name="adding-cloudtamerio-from-the-gallery"></a>从库中添加 cloudtamer.io

若要配置 cloudtamer.io 与 Azure AD 的集成，需要从库中将 cloudtamer.io 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“cloudtamer.io” 。
1. 从结果面板中选择“cloudtamer.io”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-cloudtamerio"></a>配置并测试 cloudtamer.io 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 cloudtamer.io 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 cloudtamer.io 中的相关用户之间建立关联。

若要配置并测试 cloudtamer.io 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 cloudtamer.io SSO](#configure-cloudtamerio-sso) - 在应用程序端配置单一登录设置。
    1. [创建 cloudtamer.io 测试用户](#create-cloudtamerio-test-user) - 在 cloudtamer.io 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="begin-cloudtamerio-sso-configuration"></a>开始 cloudtamer.io SSO 配置

1. 以管理员身份登录 cloudtamer.io 网站。

1. 单击右上角的“+”加号图标，然后选择“IDMS” 。

    ![创建 IDMS 的屏幕截图。](./media/cloudtamer-io-tutorial/idms-creation.png)

1. 让此屏幕保持打开状态，并将此屏幕中的值复制到 Azure AD 配置中。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“cloudtamer.io”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”文本框中，将 cloudtamer.io 中的“标识提供者颁发者(实体 ID)”粘贴到此框中 。

    b. 在“回复 URL”文本框中，将 cloudtamer.io 中的“服务提供者 ACS URL”粘贴到此框中 。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<CUSTOMERDOMAIN>.<EXTENSION>/login`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 cloudtamer.io”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 B.Simon 访问 cloudtamer.io 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“cloudtamer.io”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-cloudtamerio-sso"></a>配置 cloudtamer.io SSO

1. 在“添加 IDMS”页上，执行下列步骤：

    ![“添加 IDMS”的屏幕截图。](./media/cloudtamer-io-tutorial/configuration.png)

    a. 从下拉列表中选择“SAML 2.0”作为“IDMS 类型” 。

    b. 在“IDMS 名称”中，提供一个用户可以从“登录”屏幕识别的名称。

    c. 在“标识提供者颁发者(实体 ID)”文本框中，粘贴从 Azure 门户复制的“标识符”值 。

    d. 在记事本中打开从 Azure 门户下载的“联合元数据 XML”，并将内容粘贴到“标识提供者元数据”文本框中 。

    e. 复制“服务提供者颁发者(实体 ID)”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中 。

    f. 复制“服务提供者 ACS URI”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中 。

    g. 在“断言映射”下，输入以下值：

    | 字段 | 值 |
    |-----------|-------|
    | 名字 | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | 姓氏 | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | 电子邮件 | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
    |  用户名 | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
    |

1. 单击“创建 IDMS”。


### <a name="create-cloudtamerio-test-user"></a>创建 cloudtamer.io 测试用户

在本部分中，我们将在 cloudtamer.io 中创建名为 Britta Simon 的用户。 cloudtamer.io 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 cloudtamer.io 中不存在用户，则会在身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 cloudtamer.io 登录 URL，你可在其中启动登录流。  

* 直接转到 cloudtamer.io 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”，然后应会自动登录到为其设置了 SSO 的 cloudtamer.io 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 cloudtamer.io 磁贴时，如果该应用程序是在 SP 模式下配置的，则你会被重定向到应用程序登录页来启动登录流；如果它是在 IDP 模式下配置的，则你应会自动登录到为其设置了 SSO 的 cloudtamer.io。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 cloudtamer.io 后，即可强制实施会话控制，从而实时保护组织的敏感数据，使其免遭外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。


