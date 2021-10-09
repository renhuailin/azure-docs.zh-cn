---
title: 教程：Azure AD SSO 与 Profit.co 集成
description: 了解如何在 Azure Active Directory 与 Profit.co 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: c95eae59edc75e5d4394d4bbd9b07d6168f7b566
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548761"
---
# <a name="tutorial-azure-ad-sso-integration-with-profitco"></a>教程：Azure AD SSO 与 Profit.co 集成

本教程介绍如何将 Profit.co 与 Azure Active Directory (Azure AD) 集成。 将 Profit.co 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Profit.co。
* 允许用户使用其 Azure AD 帐户自动登录到 Profit.co。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Profit.co 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Profit.co 支持 IDP 发起的 SSO。

## <a name="add-profitco-from-the-gallery"></a>从库中添加 Profit.co

要配置 Profit.co 与 Azure AD 的集成，需要从库中将 Profit.co 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Profit.co” 。
1. 从结果面板中选择“Profit.co”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-profitco"></a>配置并测试 Profit.co 的 Azure AD SSO

使用名为“B.Simon”的测试用户配置并测试 Profit.co 的 Azure AD SSO。 若要使 SSO 有效，请在 Azure AD 用户与 Profit.co 相关用户之间建立关联。

下面是配置并测试 Profit.co 的 Azure AD SSO 的常规步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
1. [配置 Profit.co SSO](#configure-profitco-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Profit.co 测试用户](#create-a-profitco-test-user)，以在 Profit.co 中创建 B.Simon 的对应用户。该对应用户关联到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Profit.co 应用程序集成页上，找到“管理”部分 。 选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形图标以编辑设置   。

   ![“设置 SAML 单一登录”页的屏幕截图，其中突出显示了铅笔图标](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，应用程序已预先配置，且已在 Azure 中预先填充了所需的 URL。 用户需要选择“保存”按钮来保存配置。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，选择“复制”按钮。   这会复制“应用联合元数据 URL”并将其保存到计算机上。

    ![“SAML 签名证书”的屏幕截图，其中突出显示了复制按钮](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。   
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，并记下“密码”字段中显示的值。  
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，我们通过授予 B.Simon 访问 Profit.co 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。 
1. 在应用程序列表中选择“Profit.co”。
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组”   。
1. 选择“添加用户”。 在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框的“用户”列表中，选择“B.Simon”。  然后，选择屏幕底部的“选择”按钮。
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。  然后，选择屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中选择“分配”。 

## <a name="configure-profitco-sso"></a>配置 Profit.co SSO

若要在 Profit.co 端配置单一登录，需要将“应用联合元数据 URL”发送给 [Profit.co 支持团队](mailto:support@profit.co)。 他们会对此设置进行配置，以在两端正确设置 SAML SSO 连接。

### <a name="create-a-profitco-test-user"></a>创建 Profit.co 测试用户

在本部分中，你将在 Profit.co 中创建一个名为“B.Simon”的用户。与 [Profit.co 支持团队](mailto:support@profit.co)协作，将用户添加到 Profit.co 平台中。 只有在创建并激活用户后，才能使用单一登录。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Profit.co。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Profit.co 磁贴后，应会自动登录到为其设置了 SSO 的 Profit.co。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Profit.co 后，可以强制实施会话控制，实时防止组织的敏感数据遭受外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。