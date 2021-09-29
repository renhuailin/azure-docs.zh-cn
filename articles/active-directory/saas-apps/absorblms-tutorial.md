---
title: 教程：Azure Active Directory 与 Absorb LMS 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Absorb LMS 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 1347ee33905f9c39e752c1a78e3d5573f9fb6f2d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124770921"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>教程：Azure Active Directory 与 Absorb LMS 的集成

本教程将介绍如何将 Absorb LMS 与 Azure Active Directory (Azure AD) 集成。 将 Absorb LMS 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Absorb LMS。
* 让用户使用其 Azure AD 帐户自动登录到 Absorb LMS。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Absorb LMS 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Absorb LMS 单一登录的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Absorb LMS 支持 IDP 发起的 SSO

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-absorb-lms-from-the-gallery"></a>从库中添加 Absorb LMS

要配置 Absorb LMS 与 Azure AD 的集成，需要将库中的 Absorb LMS 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Absorb LMS” 。
1. 从结果面板中选择“Absorb LMS”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>配置并测试 Absorb LMS 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Absorb LMS 的 Azure AD SSO。 若要使 SSO 正常运行，需要在 Azure AD 用户与 Absorb LMS 中的相关用户之间建立关联。

若要配置并测试 Absorb LMS 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Absorb LMS SSO](#configure-absorb-lms-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Absorb LMS 测试用户](#create-absorb-lms-test-user)** - 在 Absorb LMS 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Absorb LMS”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    如果使用的是 Absorb 5 - UI，请使用以下配置：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.myabsorb.com/account/saml`

    如果使用的是 Absorb 5 - New Learner Experience，请使用以下配置：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Absorb LMS 客户端支持团队](https://support.absorblms.com/hc/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。

    ![image](common/edit-attribute.png)

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Absorb LMS”部分，根据要求复制相应的 URL。

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

本部分将通过授予 B.Simon 访问 Absorb LMS 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Absorb LMS”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-absorb-lms-sso"></a>配置 Absorb LMS SSO

1. 在新的 Web 浏览器窗口中，以管理员身份登录 Absorb LMS 公司站点。

2. 选择右上角的“帐户”按钮。

    ![“帐户”按钮](./media/absorblms-tutorial/account.png)

3. 在“帐户”窗格中，选择“门户设置”。

    ![“门户设置”链接](./media/absorblms-tutorial/portal.png)

4. 选择“管理 SSO 设置”选项卡。

    ![“用户”选项卡](./media/absorblms-tutorial/sso.png)

5. 在“管理单一登录设置”页上，执行以下操作：

    ![“单一登录”配置页](./media/absorblms-tutorial/settings.png)

    a. 在“名称”文本框中输入名称，例如“Azure AD 市场 SSO”。

    b. 选择“SAML”作为“方法”。

    c. 在记事本中，打开从 Azure 门户下载的证书。 删除 ---BEGIN CERTIFICATE--- 和 ---END CERTIFICATE--- 标记。 然后，在“密钥”框中，粘贴其余内容。

    d. 在“模式”框中，选择“发起的标识提供者”。

    e. 在“ID 属性”框中，选择在 Azure AD 中配置为用户标识符的属性。 例如，如果在已 Azure AD 中选择“nameidentifier”，请选择“用户名”。

    f. 选择“Sha256”作为“签名类型”。

    g. 在“登录 URL”框中，粘贴 Azure 门户应用程序的“属性”页中的“用户访问 URL”。

    h. 在“注销 URL”中，粘贴从 Azure 门户的“配置登录”窗口中复制的“注销 URL”值。

    i. 将“自动重定向”切换为“开启”。

6. 选择“保存”。

    ![“仅允许 SSO 登录”切换](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>创建 Absorb LMS 测试用户

要使 Azure AD 用户能够登录 Absorb LMS，必须在 Absorb LMS 中设置他们。 对于 Absorb LMS，预配是一项手动任务。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录 Absorb LMS 公司站点。

2. 在“用户”窗格中，选择“用户”。

    ![“用户”链接](./media/absorblms-tutorial/users.png)

3. 选择“用户”选项卡。

    ![“新增”下拉列表](./media/absorblms-tutorial/add.png)

4. 在“添加用户”页上执行以下操作：

    ![“添加用户”页](./media/absorblms-tutorial/user.png)

    a. 在“名字”框中键入名字，例如 Britta。

    b. 在“姓氏”框中键入姓氏，例如 Simon。

    c. 在“用户名”框中键入全名，例如 Britta Simon。

    d. 在“密码”框中，键入用户密码。

    e. 在“确认密码”框中，重新键入该密码。

    f. 将“是否活动”设置切换为“活动”。

5. 选择“保存”。

    ![“仅允许 SSO 登录”切换](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > 默认情况下，SSO 中未启用“用户预配”。 如果客户想启用此功能，需要按[此](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning)文档所述进行设置。 另请注意，只有采用 ACS URL-`https://company.myabsorb.com/api/rest/v2/authentication/saml` 的 Absorb 5 - New Learner Experience 才能使用“用户预配”功能

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Absorb LMS。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Absorb LMS 磁贴时，你应会自动登录到为其设置了 SSO 的 Absorb LMS。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Absorb LMS 后，即可强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。