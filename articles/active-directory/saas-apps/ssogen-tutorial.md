---
title: 教程：Azure Active Directory 与 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 与 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的单一登录 (SSO) 集成。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: d968b0417b65d2297a057968dc7ea64f96b39783
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800852"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>教程：Azure Active Directory 与 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的单一登录 (SSO) 集成

在本教程中，你将了解如何将 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）与 Azure Active Directory (Azure AD) 进行集成。 将 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）。
* 让用户使用其 Azure AD 帐户自动登录到 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SSOGEN - 用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关支持 **SP 和 IDP** 发起的 SSO。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>从库中添加用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关

若要配置 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）与 Azure AD 的集成，需要从库中将 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中键入“从库中添加 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）”。
1. 从结果面板中添加“SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）”，然后添加应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>配置并测试用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）中的相关用户之间建立链接关系。

若要配置并测试用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关的 Azure AD SSO，需要执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关](#configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建“用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关”测试用户](#create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user)** - 在用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中，在用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>` 

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“回复 URL”和“注销 URL”更新这些值。 联系 [SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）客户端支持团队](mailto:support@ssogen.com)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）应用程序要求通过 **user.onpremisessamaccountname** 来映射 **nameidentifier**，因此你需要单击“编辑”图标并更改属性映射来编辑属性映射。

    ![image](common/edit-attribute.png)

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分中，通过向 B.Simon 授予对 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso"></a>配置用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关

若要在 **SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）** 端配置单一登录，请找到下方特定于应用程序的 SSO 注册文档：

* Oracle EBS - Azure AD SSO 集成：[https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft - Azure AD SSO 集成：[https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards - Azure AD SSO 集成：[https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache - Azure AD SSO 集成：[https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user"></a>创建“用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关”测试用户

身份验证成功后，Azure AD 向用户应用程序发送唯一的用户标识符（名称 ID）。  请确保唯一用户标识符（名称 ID）与你的应用程序中的用户记录相匹配，例如 Oracle EBS 中的 FND_USER.USER_NAME。

若需支持，请联系 [info@ssogen.com](mailto:info@ssogen.com) 和 [support@ssogen.com](mailto:support@ssogen.com)。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关的登录 URL，你可以在其中启动登录流。  

* 直接前往用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关的登录 URL，并从该位置启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，应会自动登录到为其设置了 SSO 的用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关。 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击“用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关”磁贴时，如果是在 SP 模式下配置的，你会被重定向到应用程序登录页面以启动登录流，如果是在 IDP 模式下配置的，你应会自动登录到为其设置了 SSO 的用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关后，即可强制实施会话控制，实时防止组织的敏感数据发生外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。