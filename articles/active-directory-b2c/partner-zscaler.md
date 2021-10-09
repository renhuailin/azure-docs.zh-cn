---
title: 教程 - 为 Azure Active Directory B2C 配置 Zscaler
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C 身份验证与 Zscaler 集成。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: fb4ff79e1115086ef84694485cd9e7978295a9c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598834"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>教程：通过 Azure Active Directory B2C 配置 Zscaler Private Access

在本教程中，你将了解如何将 Azure Active Directory B2C (Azure AD B2C) 身份验证与 [Zscaler Private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access) 集成。 ZPA 提供对专用应用程序和资产的基于策略的安全访问，而不会带来虚拟专用网络 (VPN) 的成本、麻烦或安全风险。 Zscaler 安全混合访问产品/服务与 Azure AD B2C 结合使用时，可以让面向使用者的应用程序的被攻击面为零。

## <a name="prerequisites"></a>先决条件

在开始之前，你需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。  
- 一个已关联到你的 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md)。  
- [ZPA 订阅](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)。

## <a name="scenario-description"></a>方案描述

ZPA 集成包括以下组件：

- Azure AD B2C：负责验证用户凭据的标识提供者 (IdP)。 它还负责注册新用户。  
- ZPA：通过强制实施[零信任访问](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.)来负责保护 Web 应用程序的一项服务。  
- Web 应用程序：承载着用户尝试访问的服务。

下图显示了 ZPA 如何与 Azure AD B2C 集成。

![Zscaler 体系结构的示意图，其中显示了 ZPA 与 Azure AD B2C 的集成方式。](media/partner-zscaler/zscaler-architecture-diagram.png)

下表中描述了该序列：

|步骤 | 说明 |
| :-----:| :-----------|
| 1 | 用户到达某个 ZPA 用户门户或某个通过 ZPA 浏览器进行访问的应用程序。
| 2 | ZPA 需要获得用户上下文信息，然后才能决定是否允许用户访问 Web 应用程序。 为了对用户进行身份验证，ZPA 执行 SAML 重定向以重定向到 Azure AD B2C 登录页。  
| 3 | 用户到达 Azure AB B2C 登录页。 新用户通过注册来创建帐户，现有用户使用其现有凭据登录。 Azure AD B2C 对用户身份进行验证。
| 4 | 身份验证成功后，Azure AD B2C 会将用户连同 SAML 断言一起重定向回 ZPA。 ZPA 对 SAML 断言进行验证并设置用户上下文。
| 5 | ZPA 评估用户的访问策略。 如果允许用户访问 Web 应用程序，则允许连接通过。

## <a name="onboard-to-zpa"></a>加入到 ZPA

本教程假设你已有一个正常工作的 ZPA 设置。 如果要开始使用 ZPA，请参阅 [ZPA 的分步配置指南](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)。

## <a name="integrate-zpa-with-azure-ad-b2c"></a>将 ZPA 与 Azure AD B2C 集成

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>步骤 1：将 Azure AD B2C 配置为 ZPA 上的 IdP

若要将 Azure AD B2C 配置为 [ZPA 上的 IdP](https://help.zscaler.com/zpa/configuring-idp-single-sign)，请执行以下操作：

1. 登录到 [ZPA 管理门户](https://admin.private.zscaler.com)。

1. 转到“管理” > “IdP 配置”。 

1. 选择“添加 IdP 配置”。

   此时会打开“添加 IdP 配置”窗格。

   ![“添加 IdP 配置”窗格中的“IdP 信息”选项卡的屏幕截图。](media/partner-zscaler/add-idp-configuration.png)

1. 选择“IdP 信息”选项卡，然后执行以下操作：

   a. 在“名称”框中，输入“Azure AD B2C”。  
   b. 在“单一登录”下选择“用户”。   
   c. 在“域”下拉列表中，选择要与此 IdP 相关联的身份验证域。

1. 选择“下一步”。

1. 选择“SP 元数据”选项卡，然后执行以下操作：

   a. 在“服务提供商 URL”下，复制或记下该值供以后使用。  
   b. 在“服务提供商实体 ID”下，复制或记下该值供以后使用。

   ![“添加 IdP 配置”窗格上的“SP 元数据”选项卡的屏幕截图。](media/partner-zscaler/sp-metadata.png)

1. 选择“暂停”。

配置 Azure AD B2C 之后，继续进行 IdP 配置的其余部分。

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>步骤 2：在 Azure AD B2C 中配置自定义策略

>[!Note]
>仅当尚未配置自定义策略时，才需要执行此步骤。 如果你已有一个或多个自定义策略，则可以跳过此步骤。

若要在 Azure AD B2C 租户上配置自定义策略，请参阅 [Azure Active Directory B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)。

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>步骤 3：在 Azure AD B2C 中将 ZPA 注册为 SAML 应用程序

若要在 Azure AD B2C 中配置 SAML 应用程序，请参阅[在 Azure AD B2C 中注册 SAML 应用程序](./saml-service-provider.md)。 

在[“上传策略”](./saml-service-provider.md#upload-your-policy)步骤中，复制或记下 Azure AD B2C 使用的 IdP SAML 元数据 URL。 稍后需要用到此信息。

按照说明完成[“在 Azure AD B2C 中配置应用程序”](./saml-service-provider.md#configure-your-application-in-azure-ad-b2c)步骤。 在步骤 4.2 中更新应用清单属性，如下所述：

- 对于“identifierUris”：使用之前在“步骤 1.6. b”中复制或记录的服务提供商实体 ID。  
- 对于“samlMetadataUrl”：跳过此属性，因为 ZPA 不承载 SAML 元数据 URL。  
- 对于“replyUrlsWithType”：使用之前在“步骤 1.6. a”中复制或记录的服务提供商 URL。  
- 对于“logoutUrl”：跳过此属性，因为 ZPA 不支持使用注销 URL。

其余步骤与本教程无关。

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>步骤 4：从 Azure AD B2C 中提取 IdP SAML 元数据

接下来，你需要获取以下格式的 SAML 元数据 URL：

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata`

请注意，`<tenant-name>` 是你的 Azure AD B2C 租户的名称，`<policy-name>` 是你在之前的步骤中创建的自定义 SAML 策略的名称。

例如，URL 可能为：

`https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml/Samlp/metadata`.

打开 Web 浏览器并转到 SAML 元数据 URL。 右键单击页面上的任意位置，选择“另存为”，然后将文件保存到计算机上，以便在下一步使用。

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>步骤 5：完成 ZPA 上的 IdP 配置

完成前面在“步骤 1：将 Azure AD B2C 配置为 ZPA 上的 IdP”中部分配置的 [ZPA 管理门户中的 IdP 配置](https://help.zscaler.com/zpa/configuring-idp-single-sign)。

1. 在 [ZPA 管理门户](https://admin.private.zscaler.com)中，转到“管理” > “IdP 配置”。 

1. 选择你在“步骤 1”中配置的 IdP，然后选择“继续”。

1. 在“添加 IdP 配置”窗格上，选择“创建 IdP”选项卡，然后执行以下操作：

   a. 在“IdP 元数据文件”下，上传你先前在“步骤 4：从 Azure AD B2C 中提取 IdP SAML 元数据”中保存的元数据文件。  
   b. 验证 IdP 配置的“状态”是否为“已启用”。  
   c. 选择“保存”。

   ![“添加 IdP 配置”窗格上的“创建 IdP”选项卡的屏幕截图。](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>测试解决方案

转到某个 ZPA 用户门户或某个通过浏览器进行访问的应用程序，测试注册或登录过程。 测试结果应当是 SAML 身份验证成功。

## <a name="next-steps"></a>后续步骤

有关详细信息，请查看下列文章：

- [Azure AD B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
- [在 Azure AD B2C 中注册 SAML 应用程序](./saml-service-provider.md)
- [ZPA 分步配置指南](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [配置 IdP 以进行单一登录](https://help.zscaler.com/zpa/configuring-idp-single-sign)
