---
title: 教程：Azure Active Directory 与 LoginRadius 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 LoginRadius 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 832c08123904b9fb889231faa86c1308704a2581
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606413"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>教程：Azure Active Directory 与 LoginRadius 集成

本教程介绍如何将 LoginRadius 与 Azure Active Directory (Azure AD) 集成。

将 LoginRadius 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 LoginRadius。
* 可以让用户使用其 Azure AD 帐户自动登录到 LoginRadius（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 LoginRadius 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 启用了 LoginRadius 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* LoginRadius 支持 SP 发起的 SSO 

## <a name="adding-loginradius-from-the-gallery"></a>从库中添加 LoginRadius

若要配置 LoginRadius 与 Azure AD 的集成，需要从库中将 LoginRadius 添加到托管的 SaaS 应用列表。

若要从库中添加 LoginRadius：

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory”图标 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加应新的用程序，请选择“新建应用程序”按钮：

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 LoginRadius，在结果面板中选择“LoginRadius”，然后选择“添加”按钮添加该应用程序  。

    ![结果列表中的 LoginRadius](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 LoginRadius 的 Azure AD 单一登录  。
若要正常使用单一登录，需要在 Azure AD 用户与 LoginRadius 相关用户之间建立链接关系。

若要配置并测试 LoginRadius 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 LoginRadius 单一登录](#configure-loginradius-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 LoginRadius 测试用户](#create-loginradius-test-user)** - 在 LoginRadius 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 LoginRadius 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 LoginRadius 应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在“选择单一登录方法”窗格中，选择“SAML/WS-Fed”模式以启用单一登录 。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，选择“编辑”图标打开“基本 SAML 配置”窗格  。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分：

   ![LoginRadius 域和 URL 单一登录信息](common/sp-identifier.png)

   1. 在“登录 URL”文本框中，输入 URL `https://secure.loginradius.com/login`

   1. 在“标识符(实体 ID)”文本框中，输入 URL `https://lr.hub.loginradius.com/`

   1. 在“回复 URL(断言使用者服务 URL)”文本框中，输入 LoginRadius ACS URL `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` 

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，选择“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上   。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 LoginRadius”部分，根据要求复制相应的 URL。

   ![复制配置 URL](common/copy-configuration-urls.png)

   - 登录 URL

   - Azure AD 标识符

   - 注销 URL

## <a name="configure-loginradius-single-sign-on"></a>配置 LoginRadius 单一登录

在本部分中，将在 LoginRadius 管理控制台中启用 Azure AD 单一登录。

1. 登录到 [LoginRadius 管理控制台](https://adminconsole.loginradius.com/login)帐户。

2. 转到 [LoginRadius 管理控制台](https://secure.loginradius.com/account/team)中的团队管理部分。

3. 选择“单一登录”选项卡，然后选择“Azure AD” ：

   ![屏幕截图显示 LoginRadius 团队管理控制台中的单一登录菜单](./media/loginradius-tutorial/azure-ad.png)
4. 在“Azure AD 设置”页上，完成以下步骤：

   ![屏幕截图显示 LoginRadius Team 管理控制台中的 Azure Active Directory 配置](./media/loginradius-tutorial/single-sign-on.png)

    1. 在“ID 提供者位置”中，输入从 Azure AD 帐户获取的登录终结点。

    1. 在“ID 提供者注销 URL”中，输入从 Azure AD 帐户获取的注销终结点。
 
    1. 在“ID 提供者证书”中，输入从 Azure AD 帐户获取的 Azure AD 证书。 输入包含页眉和页脚的证书值。 示例： `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. 在“服务提供者证书”和“服务提供者证书密钥”中，输入你的证书和密钥 。 

       可以通过在命令行 (Linux/Mac) 上运行以下命令来创建自签名证书：

       - 用于获取 SP 的证书密钥的命令：`openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - 用于获取 SP 的证书的命令：`openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > 请确保输入包含页眉和页脚的证书和证书密钥值：
       > - 证书值示例格式：`-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - 证书密钥值示例格式：`-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. 在“数据映射”部分中，选择字段（SP 字段）并输入相应的 Azure AD 字段（IdP 字段）。

    下面列出了一些用于 Azure AD 的字段名称。

    | 字段    | 配置文件密钥                                                          |
    | --------- | -------------------------------------------------------------------- |
    | 电子邮件     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > “电子邮件”字段是必填字段。 “FirstName”和“LastName”字段映射是可选的 。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中执行以下步骤。

   ![“用户”对话框](common/user-properties.png)

   1. 在“名称”  字段中，输入 BrittaSimon  。
  
   1. 在“用户名”字段中输入 `brittasimon@yourcompanydomain.extension`。 例如，BrittaSimon@contoso.com。

   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  

   1. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 LoginRadius 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“LoginRadius”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“LoginRadius”。

    ![应用程序列表中的 LoginRadius 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 选择“添加用户”按钮，然后在“添加分配”面板中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”面板中，选择“用户”列表中的“Britta Simon”，然后选择屏幕底部的“选择”按钮   。

6. 如果希望在 SAML 断言中使用角色值，请在“选择角色”窗格中，从列表中为用户选择相应的角色。 然后，选择屏幕底部的“选择”按钮。

7. 在“添加分配”窗格中选择“分配”按钮。  

### <a name="create-loginradius-test-user"></a>创建 LoginRadius 测试用户

1. 登录到 [LoginRadius 管理控制台](https://adminconsole.loginradius.com/login)帐户。

2. 转到 LoginRadius 管理控制台中的团队管理部分。

   ![屏幕截图显示 LoginRadius 管理控制台](./media/loginradius-tutorial/team-management.png)
3. 选择侧栏中的“添加团队成员”以打开窗格。 

4. 在“添加团队成员”窗体中，通过提供用户的详细信息并分配你希望用户拥有的权限，在 LoginRadius 网站中创建名为 Britta Simon 的用户。 若要详细了解基于角色的权限，请参阅 LoginRadius [管理团队成员](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0)文档的[角色访问权限部分](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0)。 使用单一登录前，必须先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

1. 在浏览器中，转到 https://accounts.loginradius.com/auth.aspx ，然后选择“Fed SSO 登录”。
2. 输入 LoginRadius 应用名称，然后选择“登录”。
3. 这应该会打开一个弹出窗口，要求你登录到 Azure AD 帐户。
4. 进行身份验证后，弹出窗口将关闭，你将登录到 LoginRadius 管理控制台。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](./tutorial-list.md)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)
