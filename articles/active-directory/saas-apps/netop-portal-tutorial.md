---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Netop Portal 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Netop Portal 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2021
ms.author: jeedes
ms.openlocfilehash: 4e6c05af3486d74ca30587d91fbe505f9a3b2d0f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124802068"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netop-portal"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Netop Portal 集成

本教程介绍如何将 Netop Portal 与 Azure Active Directory (Azure AD) 集成。 将 Netop Portal 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Netop Portal。
* 让用户使用其 Azure AD 帐户自动登录到 Netop Portal。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Netop Portal 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Netop Portal 支持 IDP 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-netop-portal-from-the-gallery"></a>从库中添加 Netop Portal

要配置 Netop Portal 与 Azure AD 的集成，需要从库中将 Netop Portal 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Netop Portal**。
1. 在结果面板中选择“Netop Portal”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-netop-portal"></a>配置并测试 Netop Portal 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 Netop Portal 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Netop Portal 中的相关用户之间建立链接关系。

若要配置并测试 Netop Portal 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Netop Portal SSO](#configure-netop-portal-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Netop Portal 测试用户](#create-netop-portal-test-user)** - 在 Netop Portal 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Netop Portal”应用程序集成页上，找到“管理”部分，并选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在基本 SAML 配置部分，应用程序在 IDP 发起的模块中进行了预配置，且已通过 Azure 预填充了必要的 URL 。 用户需要单击“保存”  按钮来保存配置。

1. Netop Portal 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/edit-attribute.png)

1. 除上述属性以外，Netop Portal 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 |  源属性|
    | ---------------| --------- |
    | NRC-ACCOUNT-ID | “adfs demo” |
    | NRC-EMAIL | user.userprincipalname |
    | NRC-GIVEN-NAME | user.givenname |
    | NRC-SURNAME | user.surname |
    | NRC-USERNAME | user.userprincipalname |
    | NameIdentifier | user.userprincipalname |
    | | |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Netop Portal”部分，根据要求复制相应的 URL。

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

在本部分，你将通过授予 B.Simon 访问 Netop Portal 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Netop Portal”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-netop-portal-sso"></a>配置 Netop Portal SSO

若要在 Netop Portal 端配置单一登录，需要发送下载的“联合元数据 XML”以及从 Azure 门户获得的登录 URL。 按照[此处](https://kb.netop.com/article/netop-remote-control-portal-adfs-and-azure-ad-integration-456.html)的文档的步骤 3 中的说明操作，以便配置适用于 Azure AD 身份验证的 NetOp Portal。

### <a name="create-netop-portal-test-user"></a>创建 Netop Portal 测试用户

在本部分中，将在 Netop Portal 中创建一个名为 Britta Simon 的用户。 与 [Netop Portal 支持团队](mailto:casemanager@netop.com)协作，在 Netop Portal 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 Netop Portal。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击“Netop Portal”磁贴时，你应会自动登录到为其设置了 SSO 的 Netop Portal。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 Netop Portal 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。