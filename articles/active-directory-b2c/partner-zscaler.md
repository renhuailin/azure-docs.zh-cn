---
title: 通过 Zscaler 配置 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C authentication 与 Zscaler 集成
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ff51c2a71dfcaec580733a92e265628ac816e229
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095933"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>有关使用 Azure Active Directory B2C 配置安全混合访问的 Zscaler 专用访问的教程

在本教程中，了解如何将 Azure AD B2C authentication 与 [Zscaler Private Access (ZPA) ](https://www.zscaler.com/products/zscaler-private-access)集成。 ZPA 提供对专用应用程序和资产的基于策略、安全的访问，而不会产生虚拟专用网络 (VPN) 的成本、麻烦或安全风险。 Zscaler 的安全混合访问选项可在与 Azure AD B2C 结合使用时，为面向消费者的应用程序提供零攻击面。

## <a name="prerequisites"></a>先决条件

若要开始，你将需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 链接到 Azure 订阅的[Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)。

- [ZPA 订阅](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>方案描述

ZPA 集成包括以下组件：

- Azure AD B2C-标识提供者 (IdP) 负责验证用户的凭据。 它还负责注册新用户。

- ZPA-负责通过强制实现 [零信任访问](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.)来保护 web 应用程序的服务。

- Web 应用程序-承载用户尝试访问的服务。

下图显示了 ZPA 如何与 Azure AD B2C 集成。

![图像显示 zscaler 体系结构关系图](media/partner-zscaler/zscaler-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户到达 ZPA 用户门户或 ZPA 浏览器访问应用程序。
| 2. | ZPA 要求用户上下文信息，然后才能决定是否允许用户访问 web 应用程序。 若要对用户进行身份验证，ZPA 会执行 SAML 重定向到 Azure AD B2C 登录页。  
| 3. | 用户到达 Azure AB B2C 登录页。 如果是新用户，则用户将注册以创建新帐户。 现有用户将使用其现有的凭据登录。 Azure AD B2C 验证用户的身份。
| 4. | 身份验证成功后，Azure AD B2C 会将用户重定向回 ZPA，连同 SAML 断言。 ZPA 验证 SAML 断言并设置用户上下文。
| 5. | ZPA 评估用户的访问策略。 如果允许用户访问 web 应用程序，则允许连接通过。

## <a name="onboard-to-zpa"></a>载入 ZPA

本教程假定你已具有 ZPA 的工作设置。 若要开始使用 ZPA，请参阅 [ZPA 的分步配置指南](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>第1部分-将 Azure AD B2C 配置为 ZPA 上的 IdP

若要将 Azure AD B2C 配置为 [标识提供程序 (IdP) 上的 ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign)：

1. 登录到 [ZPA 管理门户](https://admin.private.zscaler.com)

2. 中转到 **管理**  >  **IdP 配置**

3. 选择 "**添加 IdP 配置**"

4. 对于 **1 IdP 信息**，请输入以下内容：

   a. **名称**： Azure AD B2C

   b. **单一登录**：选择用户

   c. **域**：选择要与此 IdP 关联的身份验证域，然后选择 "**完成**"

   ![图像显示 idp 信息](media/partner-zscaler/add-idp-configuration.png)

5. 选择“下一步”

6. 对于 **2 个 SP 元数据**：

   a. 复制服务提供程序 URL，并记下它以供以后使用。

   b. 复制服务提供程序实体 ID 并记下它以供以后使用。

   ![Image 显示了 sp 元数据信息](media/partner-zscaler/sp-metadata.png)

7. 选择 **暂停**

配置 Azure AD B2C 后，IdP 配置的其余部分将恢复。

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>第2部分-配置自定义策略 Azure AD B2C

>[!Note]
>只有在没有自定义策略的情况下，才需要执行此步骤。 如果你已有一个或多个自定义策略，则可以跳过此步骤。

[Azure Active Directory B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)一文提供了有关如何在 Azure AD B2C 租户上配置自定义策略的说明。

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>第3部分-在 Azure AD B2C 中将 ZPA 注册为 SAML 应用程序

[在 Azure AD B2C 中注册 saml 应用程序](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)一文提供了有关如何在 Azure AD B2C 中配置 saml 应用程序的说明。 在 [步骤 3.2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata)中，将提供 Azure AD B2C 使用的 IdP SAML 元数据 URL。 稍后需要用到它。

按照 [步骤 4.2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest)中的说明进行操作。 在指令的步骤4.2 中，需要更新应用程序清单。 按如下所示更新属性：

- **identifierUris**：使用在第1部分中记下的服务提供程序实体 ID **，步骤 6a**。

- **samlMetadataUrl**：跳过此属性，因为 ZPA 不托管 SAML 元数据 URL。

- **replyUrlsWithType**：使用在第 **1 部分第6b 部分** 中记下的服务提供程序 URL。

- **logoutUrl**：跳过此属性，因为 ZPA 不支持注销 URL。

其余步骤与本教程无关。

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>第4部分-从 Azure AD B2C 提取 IdP SAML 元数据

在上一步中，需要获取以下格式的 SAML 元数据 URL：

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

其中 `<tenant-name>` ，是 Azure AD B2C 租户的名称， `<policy-name>` 是你在上一步中创建的自定义 SAML 策略的名称。

例如： https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata

打开 web 浏览器并导航到 "SAML 元数据 URL"。 加载页面时，右键单击页面上的任意位置。 选择 " **将页另存为** "，并将文件保存在计算机上;你将在下一部分中使用此项。

### <a name="part-5---complete-idp-configuration-on-zpa"></a>第5部分-在 ZPA 上完成 IdP 配置

[在 ZPA 管理门户](https://help.zscaler.com/zpa/configuring-idp-single-sign)中完成在第1部分中部分配置的 IdP 配置：

1. 登录到 [ZPA 管理门户](https://admin.private.zscaler.com)

2. 请参阅 **管理**  >  **IdP 配置**。

3. 选择在第1部分中配置的 IdP，然后选择 " **恢复**"。

4. 对于 **3 Create IdP**

   a. 请参阅 " **IdP metadata file**  >  " （**选择文件**）并上传你在第4部分中保存的元数据文件。

   b. 验证 IdP 配置的 **状态** 是否为 "已 **启用**"。

   c. 选择“保存”。

      ![图像显示创建 idp 信息](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>测试解决方案

请访问 ZPA 用户门户或浏览器访问应用程序，并测试注册或登录过程。 这会导致 SAML 身份验证成功。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure Active Directory B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [在 Azure AD B2C 中注册 SAML 应用程序](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [ZPA 的分步配置指南](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [配置 IdP 以进行单一登录](https://help.zscaler.com/zpa/configuring-idp-single-sign)
