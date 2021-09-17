---
title: 教程：Azure Active Directory 与 Snowflake 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Snowflake 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 3e16dde56b2fd136eb6c76528e1c0ba406219628
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182942"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>教程：Azure Active Directory 与 Snowflake 的集成

本教程将介绍如何将 Snowflake 与 Azure Active Directory (Azure AD) 相集成。 将 Snowflake 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制有权访问 Snowflake 的人员。
* 让用户使用其 Azure AD 帐户自动登录到 Snowflake。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Snowflake 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Snowflake 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD 单一登录。

- Snowflake 支持 **SP 和 IDP** 发起的 SSO
- Snowflake 支持[自动用户预配和取消预配](snowflake-provisioning-tutorial.md)（推荐）

## <a name="adding-snowflake-from-the-gallery"></a>从库中添加 Snowflake

要配置 Snowflake 与 Azure AD 的集成，需要从库中将 Snowflake 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Snowflake” 。
1. 从结果面板中选择“Snowflake”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-snowflake"></a>配置并测试 Snowflake 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Snowflake 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Snowflake 中的相关用户之间建立链接关系。

若要配置和测试 Snowflake 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Snowflake SSO](#configure-snowflake-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Snowflake 测试用户](#create-snowflake-test-user)** - 在 Snowflake 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Snowflake 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤 ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

1. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤  ：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<SNOWFLAKE-URL>.snowflakecomputing.com`。
    
    b. 在“注销 URL”文本框中，使用以下模式键入 URL：`https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Snowflake 客户端支持团队](https://support.snowflake.net/s/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载 **证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Snowflake”部分中，根据要求复制相应 URL  。

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

本部分将通过授予 B.Simon 访问 Snowflake 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Snowflake”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-snowflake-sso"></a>配置 Snowflake SSO

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Snowflake。

1. 通过单击页面右上方的“配置文件”，使用“切换角色”将角色切换为“ACCOUNTADMIN”    。

    > [!NOTE]
    > 这与你在右上角“用户名”下选择的上下文不同。
    
    ![Snowflake 管理员](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

1. 在记事本中打开 **下载的 Base 64 证书**。 复制“-----BEGIN CERTIFICATE-----”和“-----END CERTIFICATE-----”之间的值，并将其粘贴到下面 **证书** 旁边的引号中。 在 **ssoUrl** 中，粘贴从 Azure 门户复制的“登录 URL”值  。 选择“所有查询”  ，然后单击“运行”  。

   ![Snowflake sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```


### <a name="create-snowflake-test-user"></a>创建 Snowflake 测试用户

为了使 Azure AD 用户能够登录到 Snowflake，必须将其预配到 Snowflake 中。 在 Snowflake 中，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 Snowflake。

2. 通过单击页面右上方的“配置文件”，使用“切换角色”将角色切换为“ACCOUNTADMIN”    。  

    ![Snowflake 管理员](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. 通过运行以下 SQL 查询来创建用户，确保“登录名”设置为工作表上的 Azure AD 用户名，如下所示。

    ![Snowflake adminsql](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Snowflake 登录 URL，可以从那里启动登录流。  

* 直接转到 Snowflake 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Snowflake 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Snowflake 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Snowflake。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 Snowflake 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)