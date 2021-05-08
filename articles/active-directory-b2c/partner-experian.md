---
title: 为 Azure Active Directory B2C 配置 Experian 的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C 身份验证与 Experian 集成，以进行身份验证并基于用户属性进行校对以防止欺诈行为。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 84e6f76bcae55ed905e11c46df66478908529970
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258021"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>为 Azure Active Directory B2C 配置 Experian 的教程

在本示例教程中，我们提供有关如何将 Azure AD B2C 与 [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration) 集成的指导。 Experian 提供多种解决方案，可在[此处](https://www.experian.com/)找到。

此示例中使用 Experian 的集成数字标识和欺诈风险平台 CrossCore。 CrossCore 是用于验证用户标识的 ID 验证服务。 它根据用户在注册流过程中提供的信息进行风险分析。 CrossCore 用于确定是否应该允许用户继续登录。 以下属性可用于 CrossCore 风险分析：

- 电子邮件
- IP 地址
- 名
- Middle Name
- Surname
- 街道地址
- 城市
- 省/自治区/直辖市
- 邮政编码
- 国家/地区
- 电话号码

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md)。

## <a name="scenario-description"></a>方案描述

Experian 集成包括以下组件：

- Azure AD B2C – 授权服务器，负责验证用户的凭据，也称为标识提供者

- Experian – Experian 服务采用用户提供的输入，并验证用户的身份

- 自定义 Rest API – 此 API 实现 Azure AD B2C 与 Experian 服务之间的集成。

以下体系结构图体现了实施详情。

![Experian 体系结构图的屏幕截图](media/partner-experian/experian-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户访问登录页。 用户选择注册以创建新帐户，并将信息输入到页面中。 Azure AD B2C 收集用户属性。
| 2. | Azure AD B2C 调用中间层 API 并传递用户属性。
| 3. | 中间层 API 收集用户属性，并将其转换为 Experian API 可以使用的格式。 然后，将其发送到 Experian。
| 4. | Experian 会使用该信息并加以处理，以根据风险分析来验证用户标识。 然后，它会将结果返回到中间层 API。
| 5. | 中间层 API 处理这些信息，并将相关信息以正确的 JSON 格式发送回 Azure AD B2C。
| 6. | Azure AD B2C 接收从中间层 API 返回的信息。 如果它显示“失败”响应，则会向用户显示错误消息。 如果它显示“成功”响应，则会对用户进行身份验证并将该用户写入目录。

## <a name="onboard-with-experian"></a>通过 Experian 加入

1. 若要创建 Experian 帐户，请联系 [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)

2. 在创建帐户后，你将收到 API 配置所需的信息。 以下各部分将介绍这一过程。

## <a name="configure-azure-ad-b2c-with-experian"></a>为 Azure AD B2C 配置 Experian

### <a name="part-1---deploy-the-api"></a>第 1 部分 - 部署 API

将提供的 [API 代码](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln)部署到 Azure 服务。 可以按照这些[说明](/visualstudio/deployment/quickstart-deploy-to-azure)从 Visual Studio 发布代码。

>[!NOTE]
>你将会需要已部署的服务的 URL，以便为 Azure AD 配置所需的设置。

### <a name="part-2---deploy-the-client-certificate"></a>第 2 部分：部署客户端证书

Experian API 调用受客户端证书的保护。 此客户端证书将由 Experian 提供。 按照本[文档](../app-service/environment/certificates.md#private-client-certificate)中所述的说明，证书必须上传到 Azure 应用服务。 示例策略使用该过程中的以下键步骤：

- 上传证书

- 设置具有证书指纹的 `WEBSITE_LOAD_ROOT_CERTIFICATES` 密钥。

### <a name="part-3---configure-the-api"></a>第 3 部分 - 配置 API

可以[在 Azure 中的应用服务中配置](../app-service/configure-common.md#configure-app-settings)应用程序设置。 采用这种方法，可以安全地配置设置，而无需将其签入存储库。 需要为 Rest API 提供以下设置：

| 应用程序设置 | Source | 注释 |
| :-------- | :------------| :-----------|
|CrossCoreConfig:TenantId | Experian 帐户配置 |     |
|CrossCoreConfig:OrgCode | Experian 帐户配置 |     |
|CrossCore:ApiEndpoint |Experian 帐户配置|  |
|CrossCore:ClientReference | Experian 帐户配置 | |
| CrossCore:ModelCode |Experian 帐户配置|
| CrossCore:OrgCode | Experian 帐户配置 |
| CrossCore:SignatureKey  | Experian 帐户配置 |
| CrossCore:TenantId  | Experian 帐户配置 |
| CrossCore:CertificateThumbprint | Experian 证书 |
| BasicAuth:ApiUsername | 定义 API 的用户名 | 在 ExtId 配置中使用 |
| BasicAuth:ApiPassword | 定义 API 的密码 | 在 ExtId 配置中使用

### <a name="part-4---create-api-policy-keys"></a>第 4 部分 - 创建 API 策略密钥

请参阅此[文档](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys)并创建两个策略密钥，一个用于 API 用户名，另一个用于在前面为 HTTP 基本身份验证定义的 API 密码。

>[!NOTE]
>后面将会需要用到这些密钥来配置策略。

### <a name="part-5---replace-the-configuration-values"></a>第 5 部分 - 替换配置值

在提供的[自定义策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)中，查找以下占位符，并将其替换为你实例中的相应值

|                      占位符                       |                                   替换为值                                 |                   示例                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | 你的租户短名称                                                           | "yourtenant" from yourtenant.onmicrosoft.com |
| {your_trustframeworkbase_policy}                       | TrustFrameworkBase 策略的 Azure AD B2C 名称                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C 租户中配置的 IdentityExperienceFramework 应用的应用 ID      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C 租户中配置的 ProxyIdentityExperienceFramework 应用的应用 ID | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 租户的存储应用程序的应用 ID                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 租户的存储应用程序的对象 ID                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | 在[此处](#part-4---create-api-policy-keys)创建的用户名密钥的名称             | B2C\_1A\_RestApiUsername                     |
| {your_api_password_key_name}                           | 在[此处](#part-4---create-api-policy-keys)创建的密码密钥的名称             | B2C\_1A\_RestApiPassword                     |
| {your_app_service_URL}                                 | 已设置的应用服务的 URL                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>第 6 部分 - 配置 Azure AD B2C 策略

请参考此[文档](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)，以了解有关如何设置 Azure AD B2C 租户和配置策略的说明。

>[!NOTE]
>此示例策略基于[本地帐户新手包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)。

>[!NOTE]
> 作为最佳做法，我们会建议客户在属性收集页中添加许可通知。 通知用户：信息将会发送到第三方服务来进行身份验证。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，在“策略”下选择“用户流”。

2. 选择前面创建的用户流。

3. 选择“运行用户流”，然后选择设置：

   a. 应用程序：选择已注册的应用（示例为 JWT）

   b. 回复 URL：选择“重定向 URL” 

   c. 选择“运行用户流”。

4. 浏览注册流程并创建帐户

5. 注销

6. 浏览登录流  

7. 选择“继续”后，会弹出 CrossCore 拼图。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参阅以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
