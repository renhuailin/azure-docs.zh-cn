---
title: 使用 Microsoft Dynamics 365 Fraud Protection 配置 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 本教程介绍如何使用 Microsoft Dynamics 365 Fraud Protection 配置 Azure Active Directory B2C，以便识别有风险和欺诈的帐户
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
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646548"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>教程：使用 Azure Active Directory B2C 配置 Microsoft Dynamics 365 Fraud Protection

本示例教程提供有关如何将 [Microsoft Dynamics 365 Fraud Protection](/dynamics365/fraud-protection/overview) (DFP) 与 Azure Active Directory (AD) B2C 集成的指南。

Microsoft DFP 为客户端提供了评估尝试创建新帐户和尝试登录客户端生态系统的行为是否具有欺诈性的能力。 客户可以使用 Microsoft DFP 评估来阻止或质询尝试创建新的虚假帐户或损害现有帐户的可疑行为。 帐户保护包括人工智能提供支持的设备指纹、用于实时风险评估的 API、根据客户业务需求优化风险策略的规则和列表经验，以及用于监视欺诈保护效果和客户端生态系统中趋势的记分卡。

在本示例中，我们会将 Microsoft DFP 的帐户保护功能与 Azure AD B2C 用户流集成。 该服务将在每次登录或注册尝试使用外部指纹，并监视任何过去或现在的可疑行为。 Azure AD B2C 从 Microsoft DFP 调用决策终结点，该终结点根据标识的用户的所有过去和现在行为，以及 Microsoft DFP 服务中指定的自定义规则返回结果。 Azure AD B2C 根据此结果做出审批决策，并将相同的结果传回 Microsoft DFP。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](./tutorial-create-tenant.md)。 租户已链接到 Azure 订阅。

- 获取 Microsoft DFP [订阅](https://dynamics.microsoft.com/pricing/#Sales)。 还可以设置[试用版客户端版本](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin)。

## <a name="scenario-description"></a>方案描述

Microsoft DFP 集成包括以下组件：

- **Azure AD B2C 租户**：对用户进行身份验证，并充当 Microsoft DFP 的客户端。 承载一个指纹脚本，该脚本收集每个执行目标策略的用户的标识和诊断数据。 如果发现可疑行为，Microsoft DFP 稍后会阻止或质询登录或注册尝试。

- **自定义应用服务**：用于实现两个目的的 Web 应用程序。

  - 提供用作 Identity Experience Framework UI 的 HTML 页面。 负责嵌入 Microsoft Dynamics 365 指纹脚本。

  - 一个 API 控制器，其中包含用于将 Microsoft DFP 连接到 Azure AD B2C 的 RESTful 终结点。 句柄的数据处理、结构，并遵循两者的安全要求。

- **Microsoft DFP 指纹服务**：动态嵌入的脚本，它记录设备遥测和自断言用户详细信息，以创建唯一可识别的指纹，供稍后在决策过程中使用。

- **Microsoft DFP API 终结点**：提供决策结果，并接受反映客户端应用程序采取的操作的最终状态。 由于不同的安全性和 API 有效负载要求，Azure AD B2C 不直接与终结点通信，而是使用应用服务作为中间层。

以下体系结构图体现了实施详情。

![显示 microsoft dynamics365 fraud protectio 体系结构示意图的图像](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户访问登录页。 用户选择“注册”以创建新帐户，并在页面中输入信息。 Azure AD B2C 收集用户属性。
| 2. | Azure AD B2C 调用中间层 API 并传递用户属性。
| 3. | 中间层 API 收集用户属性，并将其转换为 Microsoft DFP API 可以使用的格式。 然后，系统会将它发送到 Microsoft DFP API。
| 4. | Microsoft DFP API 使用这些信息并对其进行处理，然后将结果返回中间层 API。
| 5. | 中间层 API 处理信息并将相关信息发回 Azure AD B2C。
| 6. | Azure AD B2C 接收从中间层 API 返回的信息。 如果它显示“失败”响应，则会向用户显示错误消息。 如果它显示“成功”响应，则会对用户进行身份验证并将该用户写入目录。

## <a name="set-up-the-solution"></a>设置解决方案

1. [创建 Facebook 应用程序](./identity-provider-facebook.md#create-a-facebook-application)，该应用程序配置为允许与 Azure AD B2C 联合。
2. [添加创建的 Facebook 机密](./custom-policy-get-started.md#create-the-facebook-key)作为 Identity Experience Framework 策略密钥。

## <a name="configure-your-application-under-microsoft-dfp"></a>在 Microsoft DFP 下配置应用程序

[设置 Azure AD 租户](/dynamics365/fraud-protection/integrate-real-time-api)以使用 Microsoft DFP。

## <a name="deploy-to-the-web-application"></a>部署到 Web 应用程序

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>实现 Microsoft DFP 服务指纹

Microsoft DFP 帐户保护需要 [Microsoft DFP 设备指纹](/dynamics365/fraud-protection/device-fingerprinting)。

>[!NOTE]
>除了 Azure AD B2C UI 页，客户还可以在应用代码内实现指纹服务，从而进行更全面的设备分析。 此示例不包括应用代码中的指纹服务。

### <a name="deploy-the-azure-ad-b2c-api-code"></a>部署 Azure AD B2C API 代码

将[提供的 API 代码](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API)部署到 Azure 服务。 该代码可以[发布自 Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)。

设置 CORS，添加允许的源 `https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>稍后，你将需要已部署的服务的 URL，以便为 Azure AD 配置所需设置。

若要了解详细信息，请参阅[应用服务文档](../app-service/app-service-web-tutorial-rest-api.md)。

### <a name="add-context-dependent-configuration-settings"></a>添加与上下文相关的配置设置

在 [Azure 的应用服务中](../app-service/configure-common.md#configure-app-settings)配置应用程序设置。 这能够实现安全地配置设置，而无需将其签入存储库。 Rest API 需要提供以下设置：

| 应用程序设置 | Source | 注释 |
| :-------- | :------------| :-----------|
|FraudProtectionSettings:InstanceId | Microsoft DFP 配置 |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | Microsoft 设备指纹客户 ID |     |
| FraudProtectionSettings:ApiBaseUrl |  Microsoft DFP 门户中的基本 URL   | 删除“-int”以改为调用生产 API
|  TokenProviderConfig: Resource | https://api.dfp.dynamics-int.com |   删除“-int”以改为调用生产 API  |
|   TokenProviderConfig:ClientId       |Fraud Protection 商家 Azure AD 客户端应用 ID      |       |
| TokenProviderConfig:Authority | https://login.microsoftonline.com/<directory_ID> | Fraud Protection 商家 Azure AD 租户颁发机构 |
| TokenProviderConfig:CertificateThumbprint* | 用于对商家 Azure AD 客户端应用进行身份验证的证书指纹 |
| TokenProviderConfig:ClientSecret* | 商家 Azure AD 客户端应用的机密 | 建议使用机密管理器 |

\* 仅设置标记的两个参数其中一个，具体取决于使用证书还是机密（例如密码）进行身份验证。

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C 配置

### <a name="replace-the-configuration-values"></a>替换配置值

在提供的[自定义策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)中，查找以下占位符，并将它们替换为你实例中的相应值。

| 占位符 | 替换为 | 说明 |
| :-------- | :------------| :-----------|
|{your_tenant_name} | 你的租户短名称 |  来自 yourtenant.onmicrosoft.com 的“yourtenant”   |
|{your_tenantId} | Azure AD B2C 租户的租户 ID |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   Azure AD B2C 租户中配置的 IdentityExperienceFramework 应用的应用 ID    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  Azure AD B2C 租户中配置的 ProxyIdentityExperienceFramework 应用的应用 ID      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  租户的存储应用程序的应用 ID   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | 租户的存储应用程序的对象 ID    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   应用见解实例的检测密钥*  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | 应用服务中提供 UI 文件的终结点    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | 应用服务的 URL    |  https://yourapp.azurewebsites.net  |
|   {your-facebook-app-id}  |  配置用于与 Azure AD B2C 进行联接的 Facebook 应用的应用 ID   | 000000000000000   |
|  {your-facebook-app-secret}   |  将 Facebook 应用机密另存为的策略密钥的名称   | B2C_1A_FacebookAppSecret   |

*应用见解可以在不同的租户中。 此步骤是可选的。 删除相应的 TechnicalProfiles 和 OrechestrationSteps（如果不需要）。

### <a name="call-microsoft-dfp-label-api"></a>调用 Microsoft DFP 标签 API

客户需要[实现标签 API](/dynamics365/fraud-protection/integrate-ap-api)。 若要了解详细信息，请参阅 [Microsoft DFP API](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0)。

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

UserID 的值需要与对应的 Azure AD B2C 配置值 (ObjectID) 的值相同。

>[!NOTE]
>将许可通知添加到“属性集合”页。 请注意，系统将记录用户的遥测和用户标识信息，以便进行帐户保护。

## <a name="configure-the-azure-ad-b2c-policy"></a>配置 Azure AD B2C 策略

1. 转到 Policies 文件夹中的 [Azure AD B2C 策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)。

2. 按照此[文档](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)的说明，下载 [LocalAccounts 入门包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. 为 Azure AD B2C 租户配置策略。

>[!NOTE]
>更新提供的策略，使其与你的特定租户相关。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，在“策略”下选择“Identity Experience Framework”。

2. 选择前面创建的“SignUpSignIn”。

3. 选择“运行用户流”，然后选择设置：

   a. 应用程序：选择已注册的应用（示例为 JWT）

   b. 回复 URL：选择“重定向 URL” 

   c. 选择“运行用户流”。

4. 完成注册流程并创建帐户

5. 系统将在流程过程中（创建用户属性后）调用 Microsoft DFP 服务。 如果流未完成，请检查该用户是否未保存在目录中。

>[!NOTE]
>如果使用的是 [Microsoft DFP 规则引擎](/dynamics365/fraud-protection/rules)，请直接在 Microsoft DFP 门户中更新规则。

## <a name="next-steps"></a>后续步骤

有关更多信息，请查看以下文章：

- [Microsoft DFP 示例](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./custom-policy-get-started.md?tabs=applications)
