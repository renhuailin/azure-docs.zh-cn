---
title: 教程：Azure Active Directory 与 O.C. Tanner - AppreciateHub | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 O.C. Tanner - AppreciateHub 中创建名为“Britta Simon”的用户。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2021
ms.author: jeedes
ms.openlocfilehash: b8d9d32fdcb7b6f10ae2742edebd18ebbf798a4c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124801897"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oc-tanner---appreciatehub"></a>教程：Azure Active Directory 单一登录 (SSO) 与 O.C. 集成 Tanner - AppreciateHub

本教程介绍了如何将 O.C. Tanner - AppreciateHub 与 Azure Active Directory (Azure AD) 集成。 将 O.C. Tanner - AppreciateHub 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 O.C. Tanner - AppreciateHub 中创建名为“Britta Simon”的用户。
* 让用户使用其 Azure AD 帐户自动登录到 O.C. Tanner - AppreciateHub。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* O.C. 启用了单一登录 (SSO) 的 Tanner - AppreciateHub 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* O.C. Tanner - AppreciateHub 支持 IDP 发起的 SSO。

## <a name="add-oc-tanner---appreciatehub-from-the-gallery"></a>添加 O.C. Tanner - AppreciateHub

要通过配置将 O.C. Tanner - AppreciateHub 集成到 Azure AD 中，需要从库将 O.C. Tanner - AppreciateHub 添加到托管式 SaaS 应用的列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中键入“O.C.Tanner - AppreciateHub”。
1. 从结果面板中选择“O.C.Tanner - AppreciateHub”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-oc-tanner---appreciatehub"></a>配置并测试 O.C. 的 Azure AD SSO Tanner - AppreciateHub

使用名为  **B.Simon** 的测试用户配置并测试 O.C. Tanner - AppreciateHub 的 Azure AD SSO 若要正常使用 SSO，需要在 Azure AD 用户与 O.C. Tanner - AppreciateHub 中创建名为“Britta Simon”的用户。

若要配置并测试 O.C. Tanner - AppreciateHub 的 Azure AD 单一登录，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 O.C.Tanner - AppreciateHub SSO](#configure-oc-tanner---appreciatehub-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 O.C.Tanner - AppreciateHub 测试用户](#create-oc-tanner---appreciatehub-test-user)** - 在 O.C. Tanner - AppreciateHub 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“O.C. Tanner - AppreciateHub”应用程序集成页上，找到“管理”部分并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成  。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 O.C.Tanner - AppreciateHub”部分，根据要求复制相应的 URL。

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

在本部分中，将通过授予 B.Simon 访问 O.C. Tanner - AppreciateHub 的权限， Tanner - AppreciateHub 中创建名为“Britta Simon”的用户。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“**O.C.Tanner - AppreciateHub”。**
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-oc-tanner---appreciatehub-sso"></a>配置 O.C. Tanner - AppreciateHub SSO

要在 **O.C.Tanner - AppreciateHub** 端配置单一登录，需要将下载的“应用联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [O.C.Tanner - AppreciateHub 支持团队](mailto:sso@octanner.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-oc-tanner---appreciatehub-test-user"></a>创建 O.C. Tanner - AppreciateHub 测试用户

本部分的目的是在 O.C. Tanner - AppreciateHub 中创建名为“Britta Simon”的用户。

**若要在 O.C.Tanner - AppreciateHub 中创建名为“Britta Simon”的用户，请执行以下步骤：**

要求 [O.C. Tanner - AppreciateHub 支持团队](mailto:sso@octanner.com)创建一个用户，该用户的 nameID 属性值与 Azure AD 中 Britta Simon 的用户名相同。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 O.C. Tanner - AppreciateHub。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“O.C. Tanner - AppreciateHub”磁贴时，你应会自动登录到为其设置了 SSO 的 O.C. Tanner - AppreciateHub。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置完 O.C. Tanner - AppreciateHub 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。