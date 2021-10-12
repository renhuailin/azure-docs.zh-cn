---
title: 教程：Azure AD SSO 与 Keeper Password Manager 的集成
description: 了解如何在 Azure Active Directory 与 Keeper Password Manager 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/24/2021
ms.author: jeedes
ms.openlocfilehash: 3488ecba825d82cd1f3790b977b8b039eb38486f
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402940"
---
# <a name="tutorial-azure-ad-sso-integration-with-keeper-password-manager"></a>教程：Azure AD SSO 与 Keeper Password Manager 的集成

本教程介绍如何将 Keeper Password Manager 与 Azure Active Directory (Azure AD) 集成。 将 Keeper Password Manager 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Keeper Password Manager。
* 让用户使用其 Azure AD 帐户自动登录到 Keeper Password Manager。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Keeper Password Manager 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Keeper Password Manager 支持 SP 发起的 SSO。
* Keeper Password Manager 支持[自动用户预配和取消预配](keeper-password-manager-digitalvault-provisioning-tutorial.md)（推荐）。
* Keeper Password Manager 支持实时用户预配。

## <a name="add-keeper-password-manager-from-the-gallery"></a>从库中添加 Keeper Password Manager

若要配置 Keeper Password Manager 与 Azure AD 的集成，请从库中将该应用程序添加到托管软件即服务(SaaS) 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧窗格中选择“Azure Active Directory”服务。 
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”的搜索框中，键入“Keeper Password Manager” 。
1. 从结果面板中选择“Keeper Password Manager”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager"></a>配置并测试 Keeper Password Manager 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Keeper Password Manager 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Keeper Password Manager 中的相关用户之间建立关联。

若要配置并测试 Keeper Password Manager 的 Azure AD SSO，请执行以下步骤：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。

    1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
    1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。

1. [配置 Keeper Password Manager SSO](#configure-keeper-password-manager-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Keeper Password Manager 测试用户](#create-a-keeper-password-manager-test-user) - 在 Keeper Password Manager 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Keeper Password Manager”应用程序集成页上，找到“管理”部分 。 选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形图标以编辑设置   。

   ![“设置 SAML 单一登录”的屏幕截图，其中突出显示了笔形图标。](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 对于“标识符(实体 ID)”，请使用以下模式之一键入 URL：
    * 对于云 SSO：`https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * 对于本地 SSO：`https://<KEEPER_FQDN>/sso-connect`

    b. 对于“回复 URL”，请使用以下模式之一键入 URL：
    * 对于云 SSO：`https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * 对于本地 SSO：`https://<KEEPER_FQDN>/sso-connect/saml/sso`

    c. 对于“登录 URL”，请使用以下模式之一键入 URL：
    * 对于云 SSO：`https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * 对于本地 SSO：`https://<KEEPER_FQDN>/sso-connect/saml/login`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 若要获取这些值，请与 [Keeper Password Manager 客户端支持团队](https://keepersecurity.com/contact.html)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Keeper Password Manager 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![“用户属性和声明”的屏幕截图。](common/default-attributes.png)

1. 除此以外，Keeper Password Manager 应用程序还要求在 SAML 响应中传回其他几个属性。 这些属性如下表所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ------------| --------- |
    | First | user.givenname |
    | 上一个 | user.surname |
    | 电子邮件 | user.mail |

5. 在“设置 SAML 单一登录”上的“SAML 签名证书”部分，选择“下载”  。 这将根据你的要求从选项中下载“联合元数据 XML”并将其保存在计算机上。

    ![“SAML 签名证书”的屏幕截图，其中突出显示了“下载”。](common/metadataxml.png)

6. 在“设置 Keeper Password Manager”上，根据要求复制相应的 URL。

    ![“设置 Keeper Password Manager”的屏幕截图，其中突出显示了 URL。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

在本部分，你将在名为 `B.Simon` 的 Azure 门户中创建一个测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。
1. 在屏幕顶部选择“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 对于“名称”，请输入 `B.Simon`。  
   1. 对于“用户名”，请输入 `username@companydomain.extension`。 例如，`B.Simon@contoso.com`。
   1. 选择“显示密码”并记下显示的值。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Keeper Password Manager 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  
1. 在应用程序列表中，选择“Keeper Password Manager”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”。 在“添加分配”中，选择“用户和组” 。
1. 在“用户和组”中，从用户列表中选择“B.Simon” 。 然后选择屏幕底部的“选择”  。
1. 如果你希望将某角色分配给用户，可以从“选择角色”列表中选择该角色。 如果尚未为此应用设置任何角色，则选择“默认访问权限”角色。
1. 在“添加分配”中，选择“分配” 。

## <a name="configure-keeper-password-manager-sso"></a>配置 Keeper Password Manager SSO

若要为该应用配置 SSO，请参阅 [Keeper 支持指南](https://docs.keeper.io/sso-connect-guide/)中的准则。

### <a name="create-a-keeper-password-manager-test-user"></a>创建 Keeper Password Manager 测试用户

若要使 Azure AD 用户能够登录到 Keeper Password Manager，必须预配这些用户。 应用程序支持实时用户预配，且进行身份验证后，会在应用程序中自动创建用户。 若要手动设置用户，请联系 [Keeper 支持人员](https://keepersecurity.com/contact.html)。

> [!NOTE]
> Keeper Password Manager 还支持自动用户预配，可在[此处](./keeper-password-manager-digitalvault-provisioning-tutorial.md)找到有关如何配置自动用户预配的更多详细信息。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Keeper Password Manager 登录 URL，可从此处启动登录流。 

* 直接转到 Keeper Password Manager 登录 URL，从此处启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Keeper Password Manager 磁贴时，会重定向到 Keeper Password Manager 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Keeper Password Manager 后，可以强制实施会话控制。 这可以实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 有关详细信息，请参阅[了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。