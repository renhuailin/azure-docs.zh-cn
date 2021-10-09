---
title: 教程：Azure AD SSO 与 The Cloud Security Fabric 集成
description: 了解如何在 Azure Active Directory 和 Cloud Security Fabric 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2021
ms.author: jeedes
ms.openlocfilehash: a3b787b3ed320c5c5c1381831c7b0a24a33c1186
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619716"
---
# <a name="tutorial-azure-ad-sso-integration-with-the-cloud-security-fabric"></a>教程：Azure AD SSO 与 The Cloud Security Fabric 集成

本教程介绍如何将 The Cloud Security Fabric 与 Azure Active Directory (Azure AD) 集成。 将 The Cloud Security Fabric 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 The Cloud Security Fabric。
* 让用户使用其 Azure AD 帐户自动登录到 The Cloud Security Fabric。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 The Cloud Security Fabric 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* The Cloud Security Fabric 支持 SP 发起的 SSO。

## <a name="add-the-cloud-security-fabric-from-the-gallery"></a>从库中添加 The Cloud Security Fabric

若要配置 Cloud Security Fabric 与 Azure AD 的集成，需从库中将 Cloud Security Fabric 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **The Cloud Security Fabric**。
1. 从结果面板中选择“The Cloud Security Fabric”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-the-cloud-security-fabric"></a>配置并测试 The Cloud Security Fabric 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 The Cloud Security Fabric 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 The Cloud Security Fabric 中的相关用户之间建立链接关系。

若要配置并测试 The Cloud Security Fabric 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 The Cloud Security Fabric SSO](#configure-the-cloud-security-fabric-sso) - 在应用程序端配置单一登录设置。
    1. **[创建 The Cloud Security Fabric 测试用户](#create-the-cloud-security-fabric-test-user)** - 在 The Cloud Security Fabric 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“The Cloud Security Fabric”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式之一键入 URL：

      | **登录 URL** |
      |--------|
      | `https://platform.cloudlock.com` |
      | `https://app.cloudlock.com` |
      
   b. 在“标识符(实体 ID)”文本框中，使用以下模式之一键入 URL：

      | **Identifier** |
      |---------|
      | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
      | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |
     
    > [!NOTE]
    > 标识符非实际值。 请使用实际标识符更新此值。 联系 [Cloud Security Fabric 客户支持团队](mailto:support@cloudlock.com)来获取值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

5. 若要根据要求修改“签名”选项，请单击“编辑”按钮，打开“SAML 签名证书”对话框  。

    ![SAML 响应](./media/ciscocloudlock-tutorial/saml.png)

    a. 选择“为 SAML 响应和断言签名”选项作为“签名选项”   。

    b. 为“签名算法”选择“SHA-256”选项。

    c. 单击“保存”  。  

6. 在“设置 The Cloud Security Fabric”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 The Cloud Security Fabric 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Cloud Security Fabric”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-the-cloud-security-fabric-sso"></a>配置 The Cloud Security Fabric SSO

若要在 The Cloud Security Fabric 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [The Cloud Security Fabric 支持团队](mailto:support@cloudlock.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-the-cloud-security-fabric-test-user"></a>创建 The Cloud Security Fabric 测试用户

在本部分，你将在 The Cloud Security Fabric 中创建名为 B.Simon 的用户。 与 [Cloud Security Fabric 支持团队](mailto:support@cloudlock.com)协作，在 Cloud Security Fabric 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 The Cloud Security Fabric 登录 URL，可在其中启动登录流。 

* 直接转到 The Cloud Security Fabric 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 The Cloud Security Fabric 磁贴时，会重定向到 The Cloud Security Fabric 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 The Cloud Security Fabric 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。