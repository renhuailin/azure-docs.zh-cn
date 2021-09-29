---
title: 教程：Azure Active Directory 与 Procore SSO 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Procore SSO 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: def91912dd628b99fb2d4e62fc7799b4366887d8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124756273"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>教程：Azure Active Directory 与 Procore SSO 的集成

本教程介绍如何将 Procore SSO 与 Azure Active Directory (Azure AD) 集成。 将 Procore SSO 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Procore SSO。
* 让用户使用其 Azure AD 帐户自动登录到 Procore SSO。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Procore SSO 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 Procore SSO 订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Procore SSO 支持 IDP 发起的 SSO。

## <a name="add-procore-sso-from-the-gallery"></a>从库中添加 Procore SSO

要配置 Procore SSO 与 Azure AD 的集成，需要从库中将 Procore SSO 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Procore SSO”。
1. 从结果面板中选择“Procore SSO”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-procore-sso"></a>配置并测试 Procore SSO 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Procore SSO 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Procore SSO 中的相关用户之间建立关联。

若要配置并测试 Procore SSO 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Procore SSO](#configure-procore-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Procore SSO 测试用户](#create-procore-sso-test-user) - 在 Procore SSO 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Procore SSO”应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成  。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Procore SSO”部分中，根据要求复制相应的 URL  。

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

在本部分，你将通过授予 B.Simon 访问 Procore SSO 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Procore SSO”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-procore-sso"></a>配置 Procore SSO 

1. 若要在 Procore SSO 端配置单一登录，请以管理员身份登录 Procore 公司站点  。

2. 在工具箱下拉菜单中，单击“管理员”  以打开 SSO 设置页。

    ![显示选择了“目录”的 Procore 公司站点的屏幕截图。](./media/procoresso-tutorial/admin.png)

3. 按如下所述在框中粘贴值。

    ![显示“添加人员”对话框的屏幕截图。](./media/procoresso-tutorial/setting.png)   

    a. 在“单一登录颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    b. 在“SAML 登录目标 URL”框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. 现在打开上面从 Azure 门户下载的 **联合元数据 XML**，并复制名为 **X509Certificate** 的标记中的证书。 将复制的值粘贴到“单一登录 x509 证书”  框中。

4. 单击“保存更改”  。

5. 进行这些设置之后，需要将用于登录到 Procore 的 **域名**（例如 **contoso.com**）发送给 [Procore 支持团队](https://support.procore.com/)，他们会为该域激活联合 SSO。

### <a name="create-procore-sso-test-user"></a>创建 Procore SSO 测试用户

请按照以下步骤在 Procore SSO 端创建一个 Procore 测试用户。

1. 以管理员身份登录到 Procore 公司站点。    

2. 从工具箱下拉菜单中，单击“目录”  以打开公司目录页。

    ![显示从工具箱选择了“目录”的 Procore 公司站点的屏幕截图。](./media/procoresso-tutorial/directory.png)

3. 单击“添加人员”选项以打开窗体，并执行以下选项。

    ![显示“将人员添加到 Boylan 构造”的屏幕截图，可在其中输入用户信息。](./media/procoresso-tutorial/user.png)

    a. 在“名字”  文本框中，输入用户的名字（如 **Britta**）。

    b. 在“姓氏”  文本框中，输入用户的姓氏（如 **Simon**）。

    c. 在“电子邮件地址”文本框中，输入用户的电子邮件地址（如 BrittaSimon@contoso.com）。

    d. 对“权限模板”  选择“稍后应用权限模板”  。

    e. 单击“创建”。 

4. 检查并更新新添加的联系人的详细信息。

    ![显示“编辑”页的屏幕截图，可在其中验证用户设置。](./media/procoresso-tutorial/details.png)

5. 单击“保存和发送邀请”（如果需要通过邮件发送邀请）或“保存”（直接保存）以完成用户注册   。
    
    ![显示“当前项目设置”的屏幕截图，可在其中“保存”和“发送邀请”。](./media/procoresso-tutorial/save.png)

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Procore SSO。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Procore SSO”磁贴时，你应会自动登录到为其设置了 SSO 的 Procore SSO。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Procore SSO 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。