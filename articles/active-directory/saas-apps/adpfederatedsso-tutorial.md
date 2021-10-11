---
title: 教程：Azure AD SSO 与 ADP 集成
description: 了解如何在 Azure Active Directory 和 ADP 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2021
ms.author: jeedes
ms.openlocfilehash: b0ee99863c0ba0287d3fd192ed04145fd3a3c69f
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402560"
---
# <a name="tutorial-azure-ad-sso-integration-with-adp"></a>教程：Azure AD SSO 与 ADP 集成

本教程介绍如何将 ADP 与 Azure Active Directory (Azure AD) 集成。 将 ADP 与 Azure AD 集成后，可以执行以下操作：

* 在 Azure AD 中控制谁有权访问 ADP。
* 让用户可使用其 Azure AD 帐户自动登录到 ADP。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 ADP 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* ADP 支持 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-adp-from-the-gallery"></a>从库中添加 ADP

若要配置 ADP 与 Azure AD 的集成，需要从库中将 ADP 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **ADP**。
1. 从结果面板中选择“ADP”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-adp"></a>配置并测试 ADP 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 ADP 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 ADP 中的相关用户之间建立链接关系。

若要配置并测试 ADP 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. **[配置 ADP SSO](#configure-adp-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 ADP 测试用户](#create-adp-test-user)** - 在 ADP 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的 **ADP** 应用程序集成页上，单击“属性”选项卡并执行以下步骤： 

    ![单一登录属性](./media/adpfederatedsso-tutorial/properties.png)

    a. 将“允许用户登录”字段值设置为“是”。

    b. 复制“用户访问 URL”并将其粘贴到“配置登录 URL”部分，教程稍后将对此进行说明。

    c. 将“需要进行用户分配”字段值设置为“是”。

    d. 将“对用户可见”字段值设置为“否”。

1. 在 Azure 门户的“ADP”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    在“标识符(实体 ID)”文本框中，键入 URL：`https://fed.adp.com`

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 ADP”部分中，根据要求复制相应 URL。

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

在本部分中，将通过授予 B.Simon 访问 ADP 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“ADP”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-adp-sso"></a>配置 ADP SSO

若要在 **ADP** 端配置单一登录，需要在 [ADP 网站](https://adpfedsso.adp.com/public/login/index.fcc)中上传已下载的 **元数据 XML**。

> [!NOTE]  
> 此过程可能需要几天时间。

### <a name="configure-your-adp-services-for-federated-access"></a>配置 ADP 服务进行联合访问

>[!Important]
> 必须将需要联合访问 ADP 服务的员工分配到 ADP 服务应用，然后，必须将用户重新分配到特定的 ADP 服务。
收到 ADP 代表的确认后，请配置 ADP 服务并分配/管理用户，以控制用户对特定 ADP 服务的访问。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **ADP**。
1. 从结果面板中选择“ADP”，然后添加该应用。 在该应用添加到租户时等待几秒钟。
1. 在 Azure 门户中的 **ADP** 应用程序集成页上，单击“属性”选项卡并执行以下步骤：  

    ![单一登录链接属性选项卡](./media/adpfederatedsso-tutorial/application.png)

    1. 将“允许用户登录”字段值设置为“是”。

    1. 将“需要进行用户分配”字段值设置为“是”。

    1. 将“对用户可见”字段值设置为“是”。

1. 在 Azure 门户的“ADP”应用程序集成页上，找到“管理”部分，选择“单一登录”  。

1. 在“选择单一登录方法”对话框中，选择“模式”作为“链接”。 将应用程序链接到 **ADP**。

    ![单一登录已链接](./media/adpfederatedsso-tutorial/linked.png)

1. 导航到“配置登录 URL”部分，执行以下步骤：

    ![配置单一登录](./media/adpfederatedsso-tutorial/users.png)

    1. 粘贴从上述“属性”选项卡复制的“用户访问 URL”（在 ADP 主应用中）。

    1. 以下 5 个不同的应用支持“中继状态 URL”。 必须手动将特定应用程序的相应“中继状态 URL”值追加到“用户访问 URL”。

        * **ADP Workforce Now**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

        * **ADP Workforce Now Enhanced Time**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`

        * **ADP Vantage HCM**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

        * **ADP Enterprise HR**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

        * **MyADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

1. 单击“保存”以保存更改。

1. 收到 ADP 代表的确认后，可以开始使用一个或两个用户进行测试。

    1. 将少量的用户分配到 ADP 服务应用，以测试联合访问。

    1. 如果这些用户能够访问库中的 ADP 服务应用，并且能够访问其 ADP 服务，则表示测试成功。

1. 确认测试成功后，将联合的 ADP 服务分配到单个用户或用户组（本教程稍后会介绍），并向员工推出该服务。

### <a name="create-adp-test-user"></a>创建 ADP 测试用户

在本部分中，将在 ADP 中创建一个名为 B.Simon 的用户。 请与 [ADP 支持团队](https://www.adp.com/contact-us/overview.aspx)协作，将用户添加到 ADP 帐户中。 

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应自动登录到为其设置了 SSO 的 ADP。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 ADP 磁贴时，应会自动登录到已为其设置了 SSO 的 ADP。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 ADP 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。