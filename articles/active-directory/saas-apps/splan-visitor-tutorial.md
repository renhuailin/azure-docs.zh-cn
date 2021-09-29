---
title: Tutorial:将 Azure Active Directory 单一登录 (SSO) 与 Splan Visitor 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Splan Visitor 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: a2c33808be505209ff5eca58480b9fd5f03d9d4f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785527"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Tutorial:将 Azure Active Directory 单一登录 (SSO) 与 Splan Visitor 集成

本教程介绍如何将 Splan Visitor 与 Azure Active Directory (Azure AD) 集成。 将 Splan Visitor 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Splan Visitor。
* 让用户使用其 Azure AD 帐户自动登录到 Splan Visitor。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

要开始，需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Splan Visitor 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Splan Visitor 支持 IdP 发起的 SSO。

## <a name="add-splan-visitor-from-the-gallery"></a>从库中添加 Splan Visitor

若要配置 Splan Visitor 与 Azure AD 的集成，请从库中将 Splan Visitor 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或者个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧窗格中选择“Azure Active Directory”服务。 
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，输入“Splan Visitor” 。
1. 从结果面板中选择“Splan Visitor”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>配置并测试 Splan Visitor 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Splan Visitor 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Splan Visitor 中的相关用户之间建立链接关系。

若要配置并测试 Splan Visitor 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，使用测试 B.Simon 用户测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
1. **[配置 Splan Visitor SSO](#configure-splan-visitor-sso)** ，通过 Splan Visitor 配置单一登录设置。
    1. **[创建 Splan Visitor 测试用户](#create-a-splan-visitor-test-user)** 在 Splan Visitor 中创建 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO：

1. 在 Azure 门户中的“Splan Visitor”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔图标以编辑设置  。

   ![屏幕截图突出显示了基本 SAML 配置的“编辑/笔”图标。](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，应用程序已预先配置，且已通过 Azure 预先填充了所需的 URL。 选择“保存”按钮保存配置。

1. 在“设置 SAML 单一登录”页面的“SAML 签名证书”部分中，找到“联合元数据 XML”  。 选择“下载”以下载证书，并将其保存在计算机上。

    ![屏幕截图突出显示了联合元数据 XML 下载链接。](common/metadataxml.png)

1. 在“设置 Splan Visitor”部分，根据要求复制相应的 URL。

    ![屏幕截图突出显示了配置 URL 部分。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B. Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“姓名”字段中输入 **B.Simon**。  
   1. 在“用户名”字段中，以 username@companydomain.extension 格式输入用户名。 例如，输入 B.Simon@contoso.com  。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Splan Visitor 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Splan Visitor”打开应用概述。
1. 找到“管理”部分，然后选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”   。
1. 如果将为用户分配角色，请从“选择角色”下拉菜单中选择角色。 如果尚未为此应用设置任何角色，则继续选择“默认访问权限”角色。
1. 在“添加分配”对话框中选择“分配”。  

## <a name="configure-splan-visitor-sso"></a>配置 Splan Visitor SSO

若要配置 Splan Visitor 的单一登录，请将下载的联合元数据 XML 以及从 Azure 门户复制的相应 URL 发送给 [Splan Visitor 支持团队](mailto:support@splan.com)。 这可确保在两端正确设置 SAML SSO 连接。

### <a name="create-a-splan-visitor-test-user"></a>创建 Splan Visitor 测试用户

在 Splan Visitor 中创建名为 Britta Simon 的测试用户。 请与 [Splan Visitor 支持团队](mailto:support@splan.com)协作，将用户添加到 Splan Visitor。 必须先创建并激活用户，然后才能使用单一登录。

## <a name="test-sso"></a>测试 SSO

使用以下选项之一测试 Azure AD 单一登录配置：

* **Azure 门户**：选择“测试此应用程序”，以自动登录到为其设置了 SSO 的 Splan Visitor。
* **Microsoft 的“我的应用”门户**：选择“Splan Visitor”磁贴，以自动登录到为其设置了 SSO 的 Splan Visitor。 有关“我的应用”门户的详细信息，请参阅[从“我的应用”门户登录和启动应用](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Splan Visitor 后，可[了解如何在 Microsoft Cloud App Security 中强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。 会话控制有助于实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。