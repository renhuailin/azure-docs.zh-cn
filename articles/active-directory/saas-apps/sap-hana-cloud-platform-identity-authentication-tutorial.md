---
title: 教程：Azure Active Directory 与 SAP Cloud Platform Identity Authentication 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAP Cloud Platform Identity Authentication 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: eeee49a49e4d0c7269056cee9975303c4f22e69b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746206"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>教程：Azure Active Directory 与 SAP Cloud Platform Identity Authentication 的单一登录 (SSO) 集成

本教程介绍如何将 SAP Cloud Platform Identity Authentication 与 Azure Active Directory (Azure AD) 集成。 将 SAP Cloud Platform Identity Authentication 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SAP Cloud Platform Identity Authentication。
* 让用户使用其 Azure AD 帐户自动登录到 SAP Cloud Platform Identity Authentication。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 SAP Cloud Platform Identity Authentication 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SAP Cloud Platform Identity Authentication 支持 **SP** 和 **IDP** 发起的 SSO。
* SAP Cloud Platform Identity Authentication 支持[自动用户预配](sap-cloud-platform-identity-authentication-provisioning-tutorial.md)。

深入了解技术细节前，先理解后面将会遇到的概念至关重要。 通过 SAP Cloud Platform Identity Authentication 和 Active Directory 联合身份验证服务，可以使用受 SAP Cloud Platform Identity Authentication 保护的 SAP 应用和服务，跨受 Azure AD（可用作 IdP）保护的应用或服务实现 SSO。

目前，SAP Cloud Platform Identity Authentication 可用作 SAP 应用的代理标识提供者， 而 Azure Active Directory 则在此设置中充当主要标识提供者。 

下图展示了此关系：

![创建 Azure AD 测试用户](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

在此设置中，SAP Cloud Platform Identity Authentication 租户被配置为 Azure Active Directory 中受信任的应用。

随后会在 SAP Cloud Platform Identity Authentication 管理控制台中，配置要通过这种方式进行保护的所有 SAP 应用和服务。

因此，需要在 SAP Cloud Platform Identity Authentication（而不是 Azure Active Directory）中进行授权，授予对 SAP 应用和服务的访问权限。

通过使用 Azure Active Directory 市场将 SAP Cloud Platform Identity Authentication 配置为应用，便无需配置各个声明或 SAML 断言。

> [!NOTE]
> 目前，只有 Web SSO 已通过双方测试。 应用到 API 通信或 API 间通信所需的流应该可以正常运行，但尚未经过测试。 将在后续活动中对这些流进行测试。

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>从库添加 SAP Cloud Platform Identity Authentication

要配置 SAP Cloud Platform Identity Authentication 到 Azure AD 中的集成，需将 SAP Cloud Platform Identity Authentication 从库添加到托管型 SaaS 应用的列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分中，在搜索框中键入“SAP Cloud Platform Identity Authentication”。
1. 从结果面板中选择“SAP Cloud Platform Identity Authentication”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform-identity-authentication"></a>配置并测试 SAP Cloud Platform Identity Authentication 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 SAP Cloud Platform Identity Authentication 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 SAP Cloud Platform Identity Authentication 中的相关用户之间建立链接关系。

若要配置并测试 SAP Cloud Platform Identity Authentication 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 SAP Cloud Platform Identity Authentication SSO](#configure-sap-cloud-platform-identity-authentication-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 SAP Cloud Platform Identity Authentication 测试用户](#create-sap-cloud-platform-identity-authentication-test-user)** - 在 SAP Cloud Platform Identity Authentication 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“SAP Cloud Platform Identity Authentication”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 若要在 **IDP** 发起模式下进行配置，请在“基本 SAML 配置”部分中执行以下步骤：

    a. 在“标识符”文本框中，使用以下模式键入值：`<IAS-tenant-id>.accounts.ondemand.com`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [SAP Cloud Platform Identity Authentication 客户支持团队](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)获取这些值。 如果不了解标识符值，请阅读有关[租户 SAML 2.0 配置](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)的 SAP Cloud Platform Identity Authentication 文档。

5. 若要在 **SP** 发起模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![SAP Cloud Platform Identity Authentication 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > 此值不是真实值。 请将此值更新为实际登录 URL。 请使用特定业务应用程序登录 URL。 如果有任何疑问，请联系 [SAP Cloud Platform Identity Authentication 客户支持团队](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)。

1. SAP Cloud Platform Identity Authentication 应用程序需要特定格式的 SAML 断言，因此，你需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![图像](common/default-attributes.png)

1. 除了上述属性，SAP Cloud Platform Identity Authentication 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分，单击“下载”  以根据要求下载从给定选项提供的元数据 XML 并将其保存在计算机上。 

    ![证书下载链接](common/metadataxml.png)

9. 在“设置 SAP Cloud Platform Identity Authentication”部分，根据要求复制相应的 URL。

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

在本部分中，将通过授予 B.Simon 访问 SAP Cloud Platform Identity Authentication 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“SAP Cloud Platform Identity Authentication”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。

1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>配置 SAP Cloud Platform Identity Authentication SSO

1. 若要在 SAP Cloud Platform Identity Authentication 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录”浏览器扩展 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 SAP Cloud Platform Identity Authentication”会将你定向到 SAP Cloud Platform Identity Authentication 应用程序。 在此处，提供管理员凭据以登录到 SAP Cloud Platform Identity Authentication。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

3. 如果要手动设置 SAP Cloud Platform Identity Authentication，请在另一个 Web 浏览器窗口中，转到 SAP Cloud Platform Identity Authentication 管理控制台。 URL 采用以下模式：`https://<tenant-id>.accounts.ondemand.com/admin`。 然后，阅读与 SAP Cloud Platform Identity Authentication 相关的文档 - [与 Microsoft Azure AD 集成](https://developers.sap.com/tutorials/cp-ias-azure-ad.html)。

2. 在 Azure 门户中，选择“保存”按钮。

3. 仅当要为另一个 SAP 应用添加和启用 SSO 时，才继续执行以下步骤。 重复执行“从库添加 SAP Cloud Platform Identity Authentication”部分下的步骤。

4. 在 Azure 门户的“SAP Cloud Platform Identity Authentication”应用集成页上，选择“链接登录”。

    ![配置链接登录](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked-sign-on.png)

5. 保存配置。

> [!NOTE]
> 新应用利用旧 SAP 应用的单一登录配置。 确保在 SAP Cloud Platform Identity Authentication 管理控制台中使用相同的企业标识提供者。

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>创建 SAP Cloud Platform Identity Authentication 测试用户

无需在 SAP Cloud Platform Identity Authentication 上创建用户。 Azure AD 用户存储中的用户可以使用 SSO 功能。

SAP Cloud Platform Identity Authentication 支持“联合身份验证”选项。 使用此选项，应用可以检查 SAP Cloud Platform Identity Authentication 的用户存储中是否有经过公司标识提供者验证的用户。

“联合身份验证”选项默认处于禁用状态。 如果启用了“联合身份验证”，只有已导入 SAP Cloud Platform Identity Authentication 的用户才能访问应用。

若要详细了解如何启用或禁用 SAP Cloud Platform Identity Authentication 的联合身份验证，请参阅[配置 SAP Cloud Platform Identity Authentication 用户存储的联合身份验证](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html)中的“启用 SAP Cloud Platform Identity Authentication 的联合身份验证”。

> [!NOTE]
> SAP Cloud Platform Identity Authentication 还支持自动用户预配，在[此处](./sap-cloud-platform-identity-authentication-provisioning-tutorial.md)可以找到有关如何配置自动用户预配的更多详细信息。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 SAP Cloud Platform Identity Authentication 登录 URL，可在其中启动登录流。

* 直接转到 SAP Cloud Platform Identity Authentication 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的 SAP Cloud Platform Identity Authentication

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击 SAP Cloud Platform Identity Authentication 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 SAP Cloud Platform Identity Authentication。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 SAP Cloud Platform Identity Authentication 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)