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
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: f4c0ab8cb09839d208a1508730d9439a0c22ce60
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111555697"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>教程：Azure Active Directory 与 LoginRadius 集成

本教程介绍如何将 LoginRadius 与 Azure Active Directory (Azure AD) 集成。 将 LoginRadius 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 LoginRadius。
* 让用户使用其 Azure AD 帐户自动登录到 LoginRadius。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 LoginRadius 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 LoginRadius 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* LoginRadius 支持 SP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-loginradius-from-the-gallery"></a>从库中添加 LoginRadius

若要配置 LoginRadius 与 Azure AD 的集成，需要从库中将 LoginRadius 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“LoginRadius”。
1. 从结果面板中选择“LoginRadius”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-loginradius"></a>配置并测试 LoginRadius 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 LoginRadius 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 LoginRadius 中的相关用户之间建立关联。

若要配置并测试 LoginRadius 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 LoginRadius SSO](#configure-loginradius-sso) - 在应用程序端配置单一登录设置。
    1. [创建 LoginRadius 测试用户](#create-loginradius-test-user) - 在 LoginRadius 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“LoginRadius”应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

   1. 在“标识符(实体 ID)”文本框中，输入 URL `https://lr.hub.loginradius.com/`

   1. 在“回复 URL(断言使用者服务 URL)”文本框中，输入 LoginRadius ACS URL `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` 

   1. 在“登录 URL”文本框中，输入 URL `https://secure.loginradius.com/login`

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，选择“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上   。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 LoginRadius”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 LoginRadius 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“LoginRadius”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-loginradius-sso"></a>配置 LoginRadius SSO

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

### <a name="create-loginradius-test-user"></a>创建 LoginRadius 测试用户

1. 登录到 [LoginRadius 管理控制台](https://adminconsole.loginradius.com/login)帐户。

2. 转到 LoginRadius 管理控制台中的团队管理部分。

   ![屏幕截图显示 LoginRadius 管理控制台](./media/loginradius-tutorial/team-management.png)

3. 选择侧栏中的“添加团队成员”以打开窗格。 

4. 在“添加团队成员”窗体中，通过提供用户的详细信息并分配你希望用户拥有的权限，在 LoginRadius 网站中创建名为 Britta Simon 的用户。 若要详细了解基于角色的权限，请参阅 LoginRadius [管理团队成员](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0)文档的[角色访问权限部分](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0)。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用“我的应用”测试 Azure AD 单一登录配置。

1. 在浏览器中，转到 https://accounts.loginradius.com/auth.aspx ，然后选择“Fed SSO 登录”。
2. 输入 LoginRadius 应用名称，然后选择“登录”。
3. 这应该会打开一个弹出窗口，要求你登录到 Azure AD 帐户。
4. 进行身份验证后，弹出窗口将关闭，你将登录到 LoginRadius 管理控制台。

## <a name="next-steps"></a>后续步骤

配置 LoginRadius 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。
