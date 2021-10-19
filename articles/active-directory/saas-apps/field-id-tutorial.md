---
title: 教程：Azure AD SSO 与 Field iD 集成
description: 了解如何在 Azure Active Directory 与 Field iD 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2021
ms.author: jeedes
ms.openlocfilehash: ac235f95bfb88b8bab0733b1dbbc6c0f3e09af90
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808811"
---
# <a name="tutorial-azure-ad-sso-integration-with-field-id"></a>教程：Azure AD SSO 与 Field iD 集成

在本教程中，了解如何将 Field iD 与 Azure Active Directory (Azure AD) 集成。 将 Field iD 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Field iD。
* 让用户使用其 Azure AD 帐户自动登录到 Field iD。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

要开始，需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Field iD 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Field iD 支持 IDP 发起的 SSO。

## <a name="add-field-id-from-the-gallery"></a>从库中添加 Field iD

若要配置 Field iD 与 Azure AD 的集成，需要从库中将 Field iD 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航面板中选择“Azure Active Directory”。
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Field iD” 。
1. 从结果面板中选择“Field iD”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-field-id"></a>配置并测试 Field iD 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Field iD 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Field iD 中的相关用户之间建立链接关系。

若要配置并测试 Field iD 的 Azure AD SSO，请完成以下步骤：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。
   1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以使用 B.Simon 测试 Azure AD 单一登录。
   1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够使用 Azure AD 单一登录。
1. [配置 Field iD SSO](#configure-field-id-sso) - 在应用程序端配置单一登录设置。
   1. [创建 Field iD 测试用户](#create-a-field-id-test-user) - 使 Field iD 中 B.Simon 的对应用户链接到该用户在 Azure AD 中的表示形式。
1. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Field iD 应用程序集成页上，找到“管理”部分 。 然后选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形图标以编辑设置 。

   ![“设置 SAML 单一登录”页的屏幕截图，其中突出显示了铅笔图标](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，按照以下步骤操作：

   a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<tenantname>.fieldid.com/fieldid`

   b. 在“回复 URL”文本框中，键入使用以下模式的 URL：`https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Field iD 支持团队](mailto:support@ecompliance.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，选择“复制”图标，以复制“应用联合元数据 URL”  。 然后将其保存在计算机上。

    ![“SAML 签名证书”的屏幕截图，其中突出显示了复制图标](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。  
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 对于“名称”，请输入 `B.Simon`。  
   1. 对于“用户名”，输入 username@companydomain.extension（例如 `B.Simon@contoso.com`）。
   1. 选中“显示密码”复选框，并记下“密码”框中显示的值。 
   1. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Field iD 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。 
1. 在“应用程序”列表中选择“Field iD”。
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后选择屏幕底部的“选择”   。
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。 然后选择屏幕底部的“选择”  。
1. 在“添加分配”对话框中选择“分配”。 

## <a name="configure-field-id-sso"></a>配置 Field iD SSO

若要在 Field iD 端配置单一登录，请将“应用联合元数据 URL”发送给 [Field iD 支持团队](mailto:support@ecompliance.com)。 他们将确保在两端正确设置 SAML SSO 连接。

### <a name="create-a-field-id-test-user"></a>创建 Field iD 测试用户

本部分需在 Field iD 中创建名为“Britta Simon”的用户。 在 [Field iD 支持团队](mailto:support@ecompliance.com)的配合下，将用户添加到 Field iD 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的 Field iD。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 Field iD 磁贴时，应会自动登录到为其设置了 SSO 的 Field iD。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Field iD 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。