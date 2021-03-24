---
title: 教程：Azure Active Directory 与 KnowBe4 安全意识培训集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 KnowBe4 安全意识培训之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: jeedes
ms.openlocfilehash: 77d810fc8327e3777102f2a09f0ca32835aad4b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180755"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>教程：Azure Active Directory 与 KnowBe4 安全意识培训集成

本教程介绍如何将 KnowBe4 安全意识培训与 Azure Active Directory (Azure AD) 集成。
将 KnowBe4 安全意识培训与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 KnowBe4 安全意识培训。
* 可使用户使用其 Azure AD 帐户自动登录到 KnowBe4 Security Awareness Training（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 KnowBe4 安全意识培训的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 KnowBe4 Security Awareness Training 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* KnowBe4 Security Awareness Training 支持 **SP** 发起的 SSO

* KnowBe4 Security Awareness Training 支持 **恰时** 用户预配

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-knowbe4-from-the-gallery"></a>从库中添加 KnowBe4

要配置 KnowBe4 与 Azure AD 的集成，需要从库中将 KnowBe4 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“KnowBe4” 。
1. 从结果面板中选择“KnowBe4”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO

在本部分中，将基于名为 Britta Simon 的测试用户配置并测试 KnowBe4 的 Azure AD 单一登录。
若要使单一登录有效，需要在 Azure AD 用户与 KnowBe4 中的相关用户之间建立链接关系。

若要配置并测试 KnowBe4 的 Azure AD 单一登录，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD SSO。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使用户 Britta Simon 能够使用 Azure AD SSO。
2. **[配置 KnowBe4 Security Awareness Training SSO](#configure-knowbe4-security-awareness-training-sso)** - 在应用程序端配置 SSO 设置。
    * **[创建 KnowBe4 Security Awareness Training 测试用户](#create-knowbe4-security-awareness-training-test-user)** - 在 KnowBe4 Security Awareness Training 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 KnowBe4 应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.KnowBe4.com/auth/saml/<instancename>` 

    > [!NOTE]
    > 登录 URL 值不是实际值。 请使用实际登录 URL 更新此值。 请联系 [KnowBe4 安全意识培训客户端支持团队](mailto:support@KnowBe4.com)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求通过从给定的选项下载 **证书(原始)** 并将其保存在计算机上。

    ![证书下载链接](common/certificateraw.png)

6. 在“设置 KnowBe4 Security Awareness Training”部分，根据要求复制相应 URL。

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

在本部分中，将通过向 B.Simon 授予对 KnowBe4 的访问权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“KnowBe4”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-knowbe4-security-awareness-training-sso"></a>配置 KnowBe4 Security Awareness Training SSO

若要在 **KnowBe4 Security Awareness Training** 端配置单一登录，需要将下载的 **证书(原始)** 以及从 Azure 门户复制的相应 URL 发送给 [KnowBe4 Security Awareness Training 支持团队](mailto:support@KnowBe4.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-knowbe4-security-awareness-training-test-user"></a>创建 KnowBe4 Security Awareness Training 测试用户

在本部分中，将在 KnowBe4 中创建名为 Britta Simon 的用户。 KnowBe4 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 KnowBe4 中尚不存在用户，身份验证后会创建一个新用户。

### <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

1. 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 KnowBe4 登录 URL，可以从那里启动登录流。 

2. 直接转到 KnowBe4 登录 URL，并从那里启动登录流。

3. 可以使用 Microsoft 访问面板。 在访问面板中单击 KnowBe4 磁贴时，将会重定向到 KnowBe4 登录 URL。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md)（访问面板简介）。

## <a name="next-steps"></a>后续步骤

配置 KnowBe4 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。