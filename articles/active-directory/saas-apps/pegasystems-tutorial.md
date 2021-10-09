---
title: 教程：Azure AD 与 Pega Systems 的 SSO 集成
description: 本教程介绍如何在 Azure Active Directory 与 Pega Systems 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2021
ms.author: jeedes
ms.openlocfilehash: a0c88213583105a1076f6606f7265025ec445560
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609975"
---
# <a name="tutorial-azure-ad-sso-integration-with-pega-systems"></a>教程：Azure AD 与 Pega Systems 的 SSO 集成

本教程介绍如何将 Pega Systems 与 Azure Active Directory (Azure AD) 集成。 将 Pega Systems 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Pega Systems。
* 让用户可使用其 Azure AD 帐户自动登录到 Pega Systems。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Pega Systems 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD 单一登录。

* Pega Systems 支持 SP 和 IdP 发起的 SSO。

## <a name="add-pega-systems-from-the-gallery"></a>从库中添加 Pega Systems

要配置 Pega Systems 与 Azure AD 的集成，需要从库中将 Pega Systems 添加到托管 SaaS 应用的列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Pega Systems” 。
1. 从结果面板中选择“Pega Systems”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>配置并测试 Pega Systems 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Pega Systems 的 Azure AD SSO。 若要使 SSO 正常运行，需要在 Azure AD 用户与 Pega Systems 相关用户之间建立链接关系。

若要配置并测试 Pega Systems 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Pega Systems SSO](#configure-pega-systems-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Pega Systems 测试用户](#create-pega-systems-test-user)** - 在 Pega Systems 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Pega Systems”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 若要在 IdP 发起的模式下配置应用程序，请在“基本 SAML 配置”对话框中执行以下步骤。

    1. 在“标识符”框中，使用以下模式键入 URL： 

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. 在“回复 URL”框中，用以下模式键入 URL：

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. 若要在 SP 发起的模式下配置应用程序，请选择“设置其他 URL”并完成以下步骤。 

    1. 在“登录 URL”框中，输入登录 URL 值。 

    1. 在“中继状态”框中，输入采用以下模式的 URL：`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > 此处提供的值为占位符。 使用实际的标识符、回复 URL、登录 URL 和中继状态 URL。 可以根据本教程稍后所述，从 Pega 应用程序获取标识符和回复 URL 值。 若要获取中继状态值，请联系 [Pega Systems 支持团队](https://www.pega.com/contact-us)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. Pega Systems 应用程序需要特定格式的 SAML 断言。 若要获取正确格式的 SAML 断言，需将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性。 选择“编辑”  图标以打开“用户属性”  对话框：

    ![用户属性](common/edit-attribute.png)

7. 除上面屏幕截图中所示的属性以外，Pega Systems 应用程序还要求在 SAML 响应中传回其他几个属性。 在“用户属性”对话框的“用户声明”部分，完成以下步骤以添加这些 SAML 令牌属性：  
    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > 这些值特定于组织。 请提供适当的值。

    1. 选择“添加新声明”打开“管理用户声明”对话框：  

    ![选择“添加新声明”](common/new-save-attribute.png)

    ![“管理用户声明”对话框](common/new-attribute-details.png)

    1. 在“名称”框中，键入该行显示的属性名称。 

    1. 将“命名空间”框留空  。

    1. 对于“源”，请选择“属性”。  

    1. 在“源属性”列表中，选择为该行显示的属性值。 

    1. 选择“确定”  。

    1. 选择“保存”。 

8. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，根据要求选择“联合元数据 XML”旁边的“下载”链接，并将证书保存在计算机上：    

    ![证书下载链接](common/metadataxml.png)

9. 在“设置 Pega Systems”部分，根据要求复制相应的 URL。 

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

本部分将通过授予 B.Simon 访问 Pega Systems 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Pega Systems”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-pega-systems-sso"></a>配置 Pega Systems SSO

1. 若要在 **Pega Systems** 端配置单一登录，请在另一个浏览器窗口中使用管理员帐户登录到 Pega 门户。

1. 选择“创建” > “SysAdmin” > “身份验证服务”：

    ![选择“身份验证服务”](./media/pegasystems-tutorial/admin.png)
    
1. 在“创建身份验证服务”屏幕上执行以下步骤。

    ![“创建身份验证服务”屏幕](./media/pegasystems-tutorial/service.png)

    1. 在“类型”列表中，选择“SAML 2.0”。  

    1. 在“名称”框中输入任意名称（例如 **Azure AD SSO**）。

    1. 在“简短说明”框中输入说明。   

    1. 选择“创建并打开”。 
    
1. 在“标识提供者(IdP)信息”部分，选择“导入 IdP 元数据”，然后浏览到已从 Azure 门户下载的元数据文件。   单击“提交”以加载元数据： 

    ![“标识提供者(IdP)信息”部分](./media/pegasystems-tutorial/metadata.png)
    
    导入操作将填充 IdP 数据，如下所示：

    ![导入的 IdP 数据](./media/pegasystems-tutorial/data.png)
    
1. 在“服务提供程序(SP)设置”部分执行以下步骤。

    ![服务提供程序设置](./media/pegasystems-tutorial/settings.png)

    1. 复制“实体标识”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”框中。   

    1. 复制“断言使用者服务(ACS)位置”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”框中。   

    1. 选择  “禁用请求签名”。

1. 选择“保存”。 

### <a name="create-pega-systems-test-user"></a>创建 Pega Systems 测试用户

接下来，需要在 Pega Systems 中创建一个名为 Britta Simon 的用户。 请在 [Pega Systems 支持团队](https://www.pega.com/contact-us)的配合下创建用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Pega Systems 登录 URL，你可在其中启动登录流。  

* 直接转到 Pega Systems 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Pega Systems。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 Pega Systems 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Pega Systems。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Pega Systems 后，可以强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。