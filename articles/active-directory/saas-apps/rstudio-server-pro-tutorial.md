---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 RStudio Server Pro 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 RStudio Server Pro 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 06474832f6b65aa4d68293ef9226eec1467abe79
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124753059"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rstudio-server-pro"></a>教程：Azure Active Directory 单一登录 (SSO) 与 RStudio Server Pro 的集成

本教程介绍如何将 RStudio Server Pro (RSP) 与 Azure Active Directory (Azure AD) 集成。 将 RSP 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 RSP。
* 让用户使用其 Azure AD 帐户自动登录到 RSP。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* RSP（版本 >= 1.4）安装。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* RSP 支持 SP 和 IDP 发起的 SSO

## <a name="adding-rstudio-server-pro-from-the-gallery"></a>从库中添加 RStudio Server Pro

若要配置 RSP 与 Azure AD 的集成，需要从库中将 RStudio Server Pro SAML 身份验证添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“RStudio Server Pro SAML 身份验证” 。
1. 在结果面板中选择“RStudio Server Pro SAML 身份验证”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-rstudio-server-pro"></a>为 RStudio Server Pro 配置并测试 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 RSP 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 RSP 中的相关用户之间建立链接关系。

若要配置并测试 RStudio Server Pro SAML 身份验证的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 RStudio Server Pro SSO](#configure-rstudio-server-pro-sso) - 在应用程序端配置单一登录设置。
    1. [创建 RStudio Server Pro 测试用户](#create-rstudio-server-pro-test-user) - 在 RStudio Server Pro 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“RStudio Server Pro SAML 身份验证”应用程序集成页上，找到“管理”部分，然后选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<RSP-SERVER>/<PATH>/saml/metadata`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<RSP-SERVER>/<PATH>/saml/acs`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<RSP-SERVER>/<PATH>/`

    > [!NOTE]
    > 这些不是实际值。 将这些值更新为你的 RSP 安装的实际 URI。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分中，通过授予 B.Simon 访问 RStudio Server Pro SAML 身份验证的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“RStudio Server Pro SAML 身份验证”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-rstudio-server-pro-sso"></a>配置 RStudio Server Pro SSO

1. 将 RSP 配置文件 `/etc/rstudio/rserver.conf` 更新为以下内容：

    ```
    auth-saml=1
    auth-saml-metadata-url=<federation-metadata-URI>
    auth-saml-sp-name-id-format=emailaddress
    auth-saml-sp-attribute-username=NameID
    auth-saml-sp-base-uri=<RSP-Server-URI>
    ```

2. 通过运行以下内容重启 RSP：

    ```
    sudo rstudio-server restart
    ```

### <a name="create-rstudio-server-pro-test-user"></a>创建 RStudio Server Pro 测试用户

必须在服务器上对要使用 RSP 的所有用户进行预配。 可以使用 `useradd` 和 `adduser` 命令创建用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 RStudio Server Pro SAML 身份验证登录 URL，可在其中启动登录流。  

* 直接转到 RStudio Server Pro SAML 身份验证登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应该会自动登录到为其设置了 SSO 的 RStudio Server Pro SAML 身份验证 

还可以使用 Microsoft 访问面板在任何模式下测试此应用程序。 在访问面板中单击 RStudio Server Pro SAML 身份验证磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页，以便启动登录流；而如果是在 IDP 模式下配置的，则应该会自动登录到为其设置了 SSO 的 RStudio Server Pro SAML 身份验证。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)（访问面板简介）。

## <a name="next-steps"></a>后续步骤

配置 RStudio Server Pro SAML 身份验证后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。