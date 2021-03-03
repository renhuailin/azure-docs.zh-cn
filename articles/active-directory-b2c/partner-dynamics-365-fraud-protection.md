---
title: 有关通过 Microsoft Dynamics 365 欺诈防护配置 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 本教程介绍如何配置 Microsoft Dynamics 365 欺诈防护 Azure Active Directory B2C
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: cf441108c9fd0ae87f265604f6f0706d92516746
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646548"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>教程：将 Microsoft Dynamics 365 欺诈防护配置 Azure Active Directory B2C

在此示例教程中，我们将提供有关如何将 [Microsoft Dynamics 365 欺诈防护](/dynamics365/fraud-protection/overview) (DFP) 与 AZURE ACTIVE DIRECTORY (AD) B2C 集成的指南。

Microsoft DFP 使客户端能够评估尝试创建新帐户的风险，并尝试登录到客户端的生态系统。 客户可以使用 Microsoft DFP 评估来阻止或质询尝试创建新的虚假帐户或损害现有帐户。 帐户保护包括人工智能可实现的设备指纹、用于实时风险评估的 Api、规则和列表经验，可根据客户端的业务需求优化风险策略，并提供记分卡来监视客户端生态系统中的欺诈保护和趋势。

在此示例中，我们会将 Microsoft DFP 的帐户保护功能与 Azure AD B2C 用户流集成。 该服务将在每次登录或注册尝试时对外指纹，并监视过去或存在的可疑行为。 Azure AD B2C 从 Microsoft DFP 调用决策终结点，该终结点根据标识的用户的所有过去和当前行为返回结果，同时还从 Microsoft DFP 服务中指定的自定义规则返回结果。 Azure AD B2C 根据此结果做出批准决策，并将相同的结果传递给 Microsoft DFP。

## <a name="prerequisites"></a>先决条件

若要开始，你将需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](./tutorial-create-tenant.md)。 租户已链接到 Azure 订阅。

- 获取 Microsoft DFP [订阅](https://dynamics.microsoft.com/pricing/#Sales)。 还可以设置试用版 [客户端版本](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) 。

## <a name="scenario-description"></a>方案描述

Microsoft DFP 集成包括以下组件：

- **Azure AD B2C 租户**：对用户进行身份验证，并充当 Microsoft DFP 的客户端。 承载一个指纹脚本，该脚本收集每个执行目标策略的用户的标识和诊断数据。 如果 Microsoft DFP 发现可疑，请登录或尝试注册。

- **自定义应用服务**：提供两个用途的 web 应用程序。

  - 提供 HTML 页面用作标识体验框架的 UI。 负责嵌入 Microsoft Dynamics 365 指纹脚本。

  - 一个 API 控制器，其中包含用于将 Microsoft DFP 连接 Azure AD B2C 的 RESTful 终结点。 处理的数据处理、结构和遵从二者的安全要求。

- **MICROSOFT DFP 指纹服务**：动态嵌入的脚本，它记录设备遥测和自断言用户详细信息，以创建唯一可识别的指纹，供稍后在决策过程中使用。

- **MICROSOFT DFP API 终结点**：提供决策结果并接受最终状态，以反映客户端应用程序采取的操作。 Azure AD B2C 不会直接与终结点通信，因为存在不同的安全和 API 负载要求，而是使用应用服务作为中间。

下面的体系结构关系图显示了实现。

![图像显示 microsoft dynamics365 欺诈防护体系结构示意图](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户到达登录页。 用户选择 "注册" 以创建新帐户，并将信息输入到页面中。 Azure AD B2C 收集用户属性。
| 2. | Azure AD B2C 调用中间层 API 并传递用户特性。
| 3. | 中间层 API 收集用户属性，并将其转换为 Microsoft DFP API 可以使用的格式。 然后，将其发送到 Microsoft DFP API。
| 4. | Microsoft DFP API 使用信息并进行处理后，它会将结果返回给中间层 API。
| 5. | 中间层 API 处理信息并将相关信息发送回 Azure AD B2C。
| 6. | Azure AD B2C 接收来自中间层 API 的信息。 如果显示失败响应，则向用户显示一条错误消息。 如果它显示成功响应，则会对用户进行身份验证并将其写入到目录中。

## <a name="set-up-the-solution"></a>设置解决方案

1. 创建配置为允许联合 Azure AD B2C 的[Facebook 应用程序](./identity-provider-facebook.md#create-a-facebook-application)。
2. [添加你创建的 Facebook 机密](./custom-policy-get-started.md#create-the-facebook-key) 作为标识体验框架策略密钥。

## <a name="configure-your-application-under-microsoft-dfp"></a>在 Microsoft DFP 下配置应用程序

[将 Azure AD 租户设置](/dynamics365/fraud-protection/integrate-real-time-api) 为使用 Microsoft DFP。

## <a name="deploy-to-the-web-application"></a>部署到 web 应用程序

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>实现 Microsoft DFP 服务指纹

Microsoft [DFP 设备指纹](/dynamics365/fraud-protection/device-fingerprinting)是 microsoft DFP 帐户保护的要求。

>[!NOTE]
>除了 Azure AD B2C UI 页，客户还可以在应用代码内实现指纹服务，以进行更全面的设备分析。 此示例不包括应用代码中的指纹服务。

### <a name="deploy-the-azure-ad-b2c-api-code"></a>部署 Azure AD B2C API 代码

将 [提供的 API 代码](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) 部署到 Azure 服务。 可 [从 Visual Studio 发布](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)代码。

设置 CORS，添加允许的 **源**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>稍后你将需要部署的服务的 URL 来配置 Azure AD 以及所需的设置。

有关详细信息，请参阅 [应用服务文档](../app-service/app-service-web-tutorial-rest-api.md) 。

### <a name="add-context-dependent-configuration-settings"></a>添加上下文相关的配置设置

在 [Azure 中的应用服务](../app-service/configure-common.md#configure-app-settings)中配置应用程序设置。 这允许安全配置设置，而无需将其签入存储库。 Rest API 需要提供以下设置：

| 应用程序设置 | Source | 注释 |
| :-------- | :------------| :-----------|
|FraudProtectionSettings： InstanceId | Microsoft DFP 配置 |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | 你的 Microsoft 设备指纹 "客户 ID" |     |
| FraudProtectionSettings:ApiBaseUrl |  Microsoft DFP 门户中的基本 URL   | 请删除 "-int" 以改为调用生产 API
|  TokenProviderConfig：资源 | https://api.dfp.dynamics-int.com |   请删除 "-int" 以改为调用生产 API  |
|   TokenProviderConfig： ClientId       |欺诈防护商家 Azure AD 客户端应用 ID      |       |
| TokenProviderConfig：颁发机构 | https://login.microsoftonline.com/<directory_ID> | 欺诈防护商家 Azure AD 租户机构 |
| TokenProviderConfig： CertificateThumbprint * | 用于针对你的商家进行身份验证 Azure AD 客户端应用的证书指纹 |
| TokenProviderConfig： ClientSecret * | 商家 Azure AD 客户端应用的机密 | 建议使用机密管理器 |

* 仅设置2标记的参数中的1，具体取决于是否使用证书或机密（如密码）进行身份验证。

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C 配置

### <a name="replace-the-configuration-values"></a>替换配置值

在提供的 [自定义策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)中，查找以下占位符，并将其替换为实例中的相应值。

| 占位符 | 替换为 | 说明 |
| :-------- | :------------| :-----------|
|{your_tenant_name} | 租户短名称 |  yourtenant.onmicrosoft.com 的 "yourtenant"   |
|{your_tenantId} | Azure AD B2C 租户的租户 ID |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   Azure AD B2C 租户中配置的 IdentityExperienceFramework 应用的应用 ID    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  Azure AD B2C 租户中配置的 ProxyIdentityExperienceFramework 应用的应用 ID      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  租户的存储应用程序的应用 ID   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | 租户的存储应用程序的对象 ID    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   App insights 实例的检测密钥 *  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | 应用服务中的终结点，通过该终结点提供 UI 文件    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | 应用服务的 URL    |  https://yourapp.azurewebsites.net  |
|   {-facebook-应用 id}  |  为联合身份验证而配置的 facebook 应用的应用 ID Azure AD B2C   | 000000000000000   |
|  {-facebook-应用-密钥}   |  已将 facebook 应用机密保存为的策略密钥的名称   | B2C_1A_FacebookAppSecret   |

* App insights 可以在不同的租户中。 此步骤是可选的。 删除相应的技术配置文件和 OrechestrationSteps （如果不需要）。

### <a name="call-microsoft-dfp-label-api"></a>调用 Microsoft DFP 标签 API

客户需要 [实现标签 API](/dynamics365/fraud-protection/integrate-ap-api)。 有关详细信息，请参阅 [MICROSOFT DFP API](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) 。

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

UserID 的值需要与对应 Azure AD B2C 配置值 (ObjectID) 的值相同。

>[!NOTE]
>将许可通知添加到 "属性集合" 页。 通知将记录用户的遥测和用户标识信息，以便进行帐户保护。

## <a name="configure-the-azure-ad-b2c-policy"></a>配置 Azure AD B2C 策略

1. 在 "策略" 文件夹中转到 " [Azure AD B2C" 策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) 。

2. 按照此 [文档](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) 下载 [LocalAccounts starter pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. 为 Azure AD B2C 租户配置策略。

>[!NOTE]
>更新提供的策略，使其与特定租户相关。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，并在 "策略" 下选择 " **标识体验框架**"。

2. 选择先前创建的 **SignUpSignIn**。

3. 选择 " **运行用户流** "，然后选择设置：

   a. **应用程序**：选择注册应用 (示例为 JWT) 

   b. **回复 url**：选择 "**重定向 url** "

   c. 选择“运行用户流”。

4. 浏览注册流并创建帐户

5. 创建用户属性后，将在流中调用 Microsoft DFP 服务。 如果流未完成，请检查该用户是否未保存在目录中。

>[!NOTE]
>如果使用 [MICROSOFT DFP 规则引擎](/dynamics365/fraud-protection/rules)，请直接在 Microsoft DFP 门户中更新规则。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Microsoft DFP 示例](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./custom-policy-get-started.md?tabs=applications)
