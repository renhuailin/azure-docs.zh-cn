---
title: 教程-配置 Zscaler Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C authentication 与 Zscaler 集成。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: dbeb3cd4fccf80f434e6c7ac08c658632f64b135
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096847"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>教程：使用 Azure Active Directory B2C 配置 Zscaler 专用访问

在本教程中，你将了解如何将 Azure Active Directory B2C (Azure AD B2C) authentication 与 [Zscaler Private Access (ZPA) ](https://www.zscaler.com/products/zscaler-private-access)集成。 ZPA 提供对专用应用程序和资产的基于策略、安全的访问，而不会产生虚拟专用网络 (VPN) 的成本、麻烦或安全风险。 Zscaler 安全混合访问产品，可在与 Azure AD B2C 结合使用时，为面向消费者的应用程序提供零攻击面。

## <a name="prerequisites"></a>先决条件

在开始之前，需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。  
- 链接到 Azure 订阅的[Azure AD B2C 租户](./tutorial-create-tenant.md)。  
- [ZPA 订阅](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)。

## <a name="scenario-description"></a>方案描述

ZPA 集成包括以下组件：

- **Azure AD B2C**：标识提供者 (IdP) 负责验证用户的凭据。 它还负责注册新用户。  
- **ZPA**：通过强制实施 [零信任访问](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.)来保护 web 应用程序的服务。  
- **Web 应用程序**：承载用户尝试访问的服务。

下图显示了 ZPA 如何与 Azure AD B2C 集成。

![Zscaler 体系结构的关系图，其中显示了 ZPA 与 Azure AD B2C 的集成方式。](media/partner-zscaler/zscaler-architecture-diagram.png)

下表描述了序列：

|步骤 | 说明 |
| :-----:| :-----------|
| 1 | 用户到达 ZPA 用户门户或 ZPA 浏览器访问应用程序。
| 2 | ZPA 要求用户上下文信息，然后才能决定是否允许用户访问 web 应用程序。 若要对用户进行身份验证，ZPA 会执行 SAML 重定向到 Azure AD B2C 登录页。  
| 3 | 用户到达 Azure AB B2C 登录页。 新用户注册以创建一个帐户，并且现有用户使用其现有的凭据登录。 Azure AD B2C 验证用户的身份。
| 4 | 身份验证成功后，Azure AD B2C 会将用户重定向回 ZPA，连同 SAML 断言。 ZPA 验证 SAML 断言并设置用户上下文。
| 5 | ZPA 评估用户的访问策略。 如果允许用户访问 web 应用程序，则允许连接通过。

## <a name="onboard-to-zpa"></a>载入 ZPA

本教程假设已有一个有效的 ZPA 安装程序。 如果要开始使用 ZPA，请参阅 ZPA 的分步 [配置指南](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)。

## <a name="integrate-zpa-with-azure-ad-b2c"></a>将 ZPA 与 Azure AD B2C 集成

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>步骤1：将 Azure AD B2C 配置为 ZPA 上的 IdP

若要将 Azure AD B2C 配置为 [ZPA 上的 IdP](https://help.zscaler.com/zpa/configuring-idp-single-sign)，请执行以下操作：

1. 登录到 [ZPA 管理门户](https://admin.private.zscaler.com)。

1. 请参阅 **管理**  >  **IdP 配置**。

1. 选择 " **添加 IdP 配置**"。

   此时将打开 " **添加 IdP 配置** " 窗格。

   !["添加 IdP 配置" 窗格中的 "IdP 信息" 选项卡的屏幕截图。](media/partner-zscaler/add-idp-configuration.png)

1. 选择 " **IdP 信息** " 选项卡，然后执行以下操作：

   a. 在 " **名称** " 框中，输入 **Azure AD B2C**。  
   b. 在 " **单一登录**" 下，选择 " **用户**"。  
   c. 在 " **域** " 下拉列表中，选择要与此 IdP 相关联的身份验证域。

1. 选择“**下一页**”。

1. 选择 " **SP 元数据** " 选项卡，然后执行以下操作：

   a. 在 " **服务提供程序 URL**" 下，复制或记下值供以后使用。  
   b. 在 " **服务提供商实体 ID**" 下，复制或记下值供以后使用。

   !["添加 IdP 配置" 窗格上的 "SP 元数据" 选项卡的屏幕截图。](media/partner-zscaler/sp-metadata.png)

1. 选择 " **暂停**"。

配置 Azure AD B2C 之后，IdP 配置的其余部分将继续进行。

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>步骤2：在 Azure AD B2C 中配置自定义策略

>[!Note]
>仅当尚未配置自定义策略时，才需要执行此步骤。 如果你已有一个或多个自定义策略，则可以跳过此步骤。

若要在 Azure AD B2C 租户上配置自定义策略，请参阅 [Azure Active Directory B2C 中的自定义策略入门](./custom-policy-get-started.md)。

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>步骤3：在 Azure AD B2C 中将 ZPA 注册为 SAML 应用程序

若要在 Azure AD B2C 中配置 SAML 应用程序，请参阅 [在 Azure AD B2C 中注册 saml 应用程序](./saml-service-provider.md)。 

在 ["上传策略"](./saml-service-provider.md#upload-your-policy)步骤中，复制或记下 Azure AD B2C 使用的 IdP SAML 元数据 URL。 稍后需要用到此信息。

按照说明步骤 ["在 Azure AD B2C 中配置应用程序"](./saml-service-provider.md#configure-your-application-in-azure-ad-b2c)。 在步骤4.2 中，按如下所示更新应用程序清单属性：

- 对于 **identifierUris**：使用之前在 "步骤 1.6. b" 中复制或记录的服务提供程序实体 ID。  
- 对于 **samlMetadataUrl**：跳过此属性，因为 ZPA 不托管 SAML 元数据 URL。  
- 对于 **replyUrlsWithType**：使用之前在 "步骤 1.6. a" 中复制或记录的服务提供程序 URL。  
- 对于 **logoutUrl**：跳过此属性，因为 ZPA 不支持注销 URL。

其余步骤与本教程无关。

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>步骤4：从 Azure AD B2C 提取 IdP SAML 元数据

接下来，需要获取以下格式的 SAML 元数据 URL：

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

请注意， `<tenant-name>` 是 Azure AD B2C 租户的名称， `<policy-name>` 是你在上一步中创建的自定义 SAML 策略的名称。

例如，URL 可能为 `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata`。

打开 web 浏览器并中转到 "SAML 元数据 URL"。 右键单击页面上的任意位置，选择 " **另存为**"，然后将文件保存到计算机上，以便在下一步中使用。

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>步骤5：完成 ZPA 上的 IdP 配置

完成前面 "步骤1：将 Azure AD B2C 配置为 ZPA 上的 IdP" 中部分配置的 [ZPA 管理门户中的 IdP 配置](https://help.zscaler.com/zpa/configuring-idp-single-sign) 。

1. 在 [ZPA 管理门户](https://admin.private.zscaler.com)中，请参阅 **管理**  >  **IdP 配置**。

1. 选择在 "步骤 1" 中配置的 IdP，然后选择 " **恢复**"。

1. 在 " **添加 IdP 配置** " 窗格上，选择 " **创建 IdP** " 选项卡，然后执行以下操作：

   a. 在 " **IdP 元数据文件**" 下，上传先前在 "步骤4：从 Azure AD B2C 提取 IdP SAML 元数据" 中保存的元数据文件。  
   b. 验证 IdP 配置的 **状态** 是否为 " **已启用**"。  
   c. 选择“保存”。

   !["添加 IdP 配置" 窗格上的 "创建 IdP" 选项卡的屏幕截图。](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>测试解决方案

请访问 ZPA 用户门户或浏览器访问应用程序，并测试注册或登录过程。 测试应会导致 SAML 身份验证成功。

## <a name="next-steps"></a>后续步骤

有关详细信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略入门](./custom-policy-get-started.md)
- [在 Azure AD B2C 中注册 SAML 应用程序](./saml-service-provider.md)
- [ZPA 的分步配置指南](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [配置 IdP 以进行单一登录](https://help.zscaler.com/zpa/configuring-idp-single-sign)