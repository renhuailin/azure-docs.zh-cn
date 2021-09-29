---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Adobe Creative Cloud 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Adobe Creative Cloud 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 5ba23a1bc5740eece8aa5e48d4f6cd81fab7b87e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803438"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Adobe Creative Cloud 集成

本教程介绍了如何将 Adobe Creative Cloud 与 Azure Active Directory (Azure AD) 集成。 将 Adobe Creative Cloud 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Adobe Creative Cloud。
* 让用户使用其 Azure AD 帐户自动登录到 Adobe Creative Cloud。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Adobe Creative Cloud 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Adobe Creative Cloud 支持 **SP** 发起的 SSO

## <a name="add-adobe-creative-cloud-from-the-gallery"></a>从库中添加 Adobe Creative Cloud

要配置 Adobe Creative Cloud 与 Azure AD 的集成，需要从库中将 Adobe Creative Cloud 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Adobe Creative Cloud” 。
1. 从结果面板中选择“Adobe Creative Cloud”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>配置和测试 Adobe Creative Cloud 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Adobe Creative Cloud 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Adobe Creative Cloud 中的相关用户之间建立链接关系。

若要配置并测试 Adobe Creative Cloud 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Adobe Creative Cloud SSO](#configure-adobe-creative-cloud-sso)** - 在应用程序端配置单一登录。
    1. **[创建 Adobe Creative Cloud 测试用户](#create-adobe-creative-cloud-test-user)** - 在 Adobe Creative Cloud 中创建 B.Simon 的对应用户，将其链接到其 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Adobe Creative Cloud”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，键入 URL：`https://adobe.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > 标识符非实际值。 按照“配置 Adobe Cloud SSO”部分第 4 步中的指导进行操作。 于是，可以打开联合元数据 XML 文件，从该文件中获取实体 ID 值，并将其作为标识符值放入 Azure AD 配置。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. Adobe Creative Cloud 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/edit-attribute.png)

1. 除了上述属性，Adobe Creative Cloud 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | 电子邮件 | user.mail |

    > [!NOTE]
    > 用户需要有一个有效的 Microsoft 365 ExO 许可证，以便在 SAML 响应中填充电子邮件声明值。

1. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中找到“联合数据 XML”，然后选择“下载”以下载 XML 元数据文件并将其保存在计算机上   。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Adobe Creative Cloud”部分中，根据要求复制相应的 URL。

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

本部分将通过授予 B.Simon 访问 Adobe Creative Cloud 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  
1. 在应用程序列表中，选择“Adobe Creative Cloud”。
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组”   。
1. 选择“添加用户”。 然后，在“添加分配”对话框中选择“用户和组”。  
1. 在“用户和组”对话框中，从用户列表中选择“B.Simon”。   然后选择屏幕底部的“选择”  。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中选择“分配”。

## <a name="configure-adobe-creative-cloud-sso"></a>配置 Adobe Creative Cloud SSO

1. 在另一个 Web 浏览器窗口中，以系统管理员身份登录到 [Adobe 管理控制台](https://adminconsole.adobe.com)。

1. 转到顶部导航栏上的“设置”，然后选择“标识”。 目录列表随即打开。 选择所需的联合目录。

1. 在“目录详细信息”页上，选择“配置”。

1. 复制实体 ID 和 ACS URL（断言使用者服务 URL 或回复 URL）。 在 Azure 门户中的相应字段中输入这些 URL。

    ![在应用端配置单一登录](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. 在“配置应用设置”对话框中，将 Adobe 提供的实体 ID 值用作“标识符”。

    b. 在“配置应用设置”对话框中，将 Adobe 提供的 ACS URL（断言使用者服务 URL）值用作“回复 URL”。

1. 在页面底部附近，上传你从 Azure 门户下载的 **联合数据 XML** 文件。 

    ![联合数据 XML 文件](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "IdP 元数据 XML")

1. 选择“保存”。

### <a name="create-adobe-creative-cloud-test-user"></a>创建 Adobe Creative Cloud 测试用户

为了使 Azure AD 用户能够登录 Adobe Creative Cloud，必须将其预配到 Adobe Creative Cloud 中。 就 Adobe Creative Cloud 来说，预配任务需要手动完成。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：

1. 以管理员身份登录到 [Adobe 管理控制台](https://adminconsole.adobe.com)站点。

2. 添加 Adobe 控制台中的用户作为“联合 ID”，并将其分配到某个产品配置文件。 有关添加用户的详细信息，请参阅[在 Adobe 管理控制台中添加用户](https://helpx.adobe.com/enterprise/using/users.html#Addusers)。

3. 现在，请在“Adobe 登录”窗体中键入电子邮件地址/UPN，按 Tab，随后应会联合回到 Azure AD：
   * Web 访问：www\.adobe.com > 登录
   * 在“桌面应用实用工具”>“登录”中
   * 在“应用程序”>“帮助”>“登录”中

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Adobe Creative Cloud 登录 URL，你可以从此处启动登录流。 

* 直接转到 Adobe Creative Cloud 登录 URL，并从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Adobe Creative Cloud 磁贴时，将会重定向到 Adobe Creative Cloud 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Adobe Creative Cloud 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)