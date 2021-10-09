---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Genesys Cloud for Azure 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Genesys Cloud for Azure 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: e2d6619f1dce9ad7ef68f07ec614f36d409c6831
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746515"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-genesys-cloud-for-azure"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Genesys Cloud for Azure 的集成

本教程介绍如何将 Genesys Cloud for Azure 与 Azure Active Directory (Azure AD) 相集成。 将 Genesys Cloud for Azure 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Genesys Cloud for Azure。
* 让用户使用其 Azure AD 帐户自动登录到 Genesys Cloud for Azure。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有帐户，可以获取 [免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Genesys Cloud for Azure 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Genesys Cloud for Azure 支持 SP 和 IDP 发起的 SSO。

* Genesys Cloud for Azure 支持[自动用户预配](purecloud-by-genesys-provisioning-tutorial.md)。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-genesys-cloud-for-azure-from-the-gallery"></a>从库中添加 Genesys Cloud for Azure

若要配置 Genesys Cloud for Azure 与 Azure AD 的集成，必须从库中将 Genesys Cloud for Azure 添加到托管 SaaS 应用列表。 为此，请按照下列步骤进行操作：

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 转到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Genesys Cloud for Azure” 。
1. 在结果面板中选择“Genesys Cloud for Azure”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-genesys-cloud-for-azure"></a>配置并测试 Genesys Cloud for Azure 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Genesys Cloud for Azure 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Genesys Cloud for Azure 中的相关用户之间建立链接关系。

若要配置并测试 Genesys Cloud for Azure 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
1. [配置 Genesys Cloud for Azure SSO](#configure-genesys-cloud-for-azure-sso)，在应用程序端配置单一登录设置。
    1. [创建 Genesys Cloud for Azure 测试用户](#create-genesys-cloud-for-azure-test-user)，在 Genesys Cloud for Azure 中创建 B.Simon 的对应用户，并将其链接到该用户在 Azure AD 中对应的身份。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

若要在 Azure 门户中启用 Azure AD SSO，请执行以下步骤：

1. 在 Azure 门户中的“Genesys Cloud for Azure”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤 ：

    a. 在“标识符”框中，输入与你所在区域对应的 URL：
    
    | 标识符 URL |
    |---|
    | https://login.mypurecloud.com/saml |
    | https://login.mypurecloud.de/saml |
    | https://login.mypurecloud.jp/saml |
    | https://login.mypurecloud.ie/saml |
    | https://login.mypurecloud.com.au/saml |
    |

    b. 在“回复 URL”框中，输入与你所在区域对应的 URL：

    | 回复 URL |
    |---|
    | https://login.mypurecloud.com/saml |
    | https://login.mypurecloud.de/saml |
    | https://login.mypurecloud.jp/saml |
    | https://login.mypurecloud.ie/saml |
    | https://login.mypurecloud.com.au/saml |
    |

1. 若要将应用程序配置为 SP 发起的模式，请选择“设置其他 URL”并执行以下步骤：  

    在“登录 URL”框中，输入与你所在区域对应的 URL：
    
    |登录 URL |
    |---|
    | https://login.mypurecloud.com |
    | https://login.mypurecloud.de |
    | https://login.mypurecloud.jp |
    | https://login.mypurecloud.ie |
    | https://login.mypurecloud.com.au |
    |

1. Genesys Cloud for Azure 应用程序需要特定格式的 SAML 断言，这需要你将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表：

    ![image](common/default-attributes.png)

1. 此外，Genesys Cloud for Azure 应用程序还要求在 SAML 响应中传回其他几个属性，如下表中所示。 这些属性也是预先填充的，但可以根据需要查看它们。

    | 名称 | 源属性|
    | ---------------| --------------- |
    | 电子邮件 | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Genesys Cloud for Azure”部分，根据你的需求复制相应的一个或多个 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B. Simon 的测试用户：

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”。   
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名字段”中，按以下格式输入用户名：username@companydomain.extension  。 例如：`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值   。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Genesys Cloud for Azure 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在“应用程序”列表中，选择“Genesys Cloud for Azure”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-genesys-cloud-for-azure-sso"></a>配置 Genesys Cloud for Azure SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Genesys Cloud for Azure。

1. 选择顶部的“管理员”，然后转到“集成”下的“单一登录”    。

    ![屏幕截图显示“PureCloud 管理”窗口，可在其中选择“单一登录”。](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. 切换到“ADFS/Azure AD(Premium)”选项卡，然后执行以下这些步骤  ：

    ![屏幕截图显示了“集成”页面，你可以在其中输入所述值。](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. 选择“浏览”，将从 Azure 门户下载的 base-64 编码证书上传到“ADFS 证书”   。

    b. 在“ADFS 颁发者 URI”框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    c. 在“目标 URI”框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    d. 对于“信赖方标识符”值，请转到 Azure 门户，然后在“Genesys Cloud for Azure”应用程序集成页中，选择“属性”选项卡并复制“应用程序 ID”值   。 然后将其粘贴到“信赖方标识符”框中  。

    ![屏幕截图显示了“属性”窗格，可在其中找到“应用程序 ID”值。](./media/purecloud-by-genesys-tutorial/configuration.png)

    e. 选择“保存”。 

### <a name="create-genesys-cloud-for-azure-test-user"></a>创建 Genesys Cloud for Azure 测试用户

要使 Azure AD 用户能够登录到 Genesys Cloud for Azure，必须在 Genesys Cloud for Azure 中预配这些用户。 在 Genesys Cloud for Azure 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Genesys Cloud for Azure。

1. 选择顶部的“管理员”并转到“人员和权限”下的“人员”    。

    ![屏幕截图显示“PureCloud 管理”窗口，可在其中选择“人员”。](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. 在“人员”页上，选择“添加人员”   。

    ![屏幕截图显示可在其中添加人员的“人员”页。](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. 在“将人员添加到组织”对话框中，执行以下这些步骤  ：

    ![屏幕截图显示了可以在其中输入所述值的页。](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. 在“全名”  框中，输入用户的姓名。 例如：B.simon  .

    b. 在“电子邮件”框中，输入用户的电子邮件  。 例如：b.simon\@contoso.com  。

    c. 选择“创建”  。

> [!NOTE]
> Genesys Cloud for Azure 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](./purecloud-by-genesys-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Genesys Cloud for Azure 登录 URL，可从中启动登录流。  

* 直接转到 Genesys Cloud for Azure 登录 URL，并从中启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”，然后你应会自动登录到为其设置了 SSO 的 Genesys Cloud for Azure。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“Genesys Cloud for Azure”磁贴时，如果该应用程序是在 SP 模式下配置的，则你会重定向到应用程序登录页，从中可以启动登录流；如果它是在 IDP 模式下配置的，则你应会自动登录到为其设置了 SSO 的 Genesys Cloud for Azure。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Genesys Cloud for Azure 后，可以强制实施会话控制，从而实时防止组织的敏感数据遭到外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。