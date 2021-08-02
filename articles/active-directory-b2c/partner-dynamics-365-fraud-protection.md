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
ms.date: 5/12/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 219cb793da7835922ad707d0ad1ee7e122990ba8
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962187"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>教程：使用 Azure Active Directory B2C 配置 Microsoft Dynamics 365 Fraud Protection

本示例教程介绍如何将 [Microsoft Dynamics 365 Fraud Protection](/dynamics365/fraud-protection/overview) (DFP) 与 Azure Active Directory (AD) B2C 集成。

Microsoft DFP 为组织提供了评估尝试创建欺诈性帐户和登录的风险的能力。客户可以使用 Microsoft DFP 评估来阻止或质询尝试创建新的虚假帐户或损害现有帐户的可疑行为。

该示例演示如何将 Microsoft DFP 设备指纹以及帐户创建和登录评估 API 终结点合并到 Azure AD B2C 自定义策略中。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](./tutorial-create-tenant.md)。 租户已链接到 Azure 订阅。

- 获取 Microsoft DFP [订阅](https://dynamics.microsoft.com/pricing/#Sales)。 还可以设置[试用版客户端版本](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin)。

## <a name="scenario-description"></a>方案描述

Microsoft DFP 集成包括以下组件：

- **Azure AD B2C 租户**：对用户进行身份验证，并充当 Microsoft DFP 的客户端。 承载一个指纹脚本，该脚本收集每个执行目标策略的用户的标识和诊断数据。 根据 Microsoft DFP 返回的规则评估结果，稍后会阻止或质询登录或注册尝试。

- 自定义 UI 模板：用于自定义 Azure AD B2C 呈现的页面的 HTML 内容。 这些页面包含 Microsoft DFP 指纹所需的 JavaScript 代码片段

- **Microsoft DFP 指纹服务**：动态嵌入的脚本，它记录设备遥测和自断言用户详细信息，以创建唯一可识别的指纹，供稍后在决策过程中使用。

- **Microsoft DFP API 终结点**：提供决策结果，并接受反映客户端应用程序采取的操作的最终状态。 Azure AD B2C 使用 REST API 连接器直接与 Microsoft DFP 终结点进行通信。 通过向 Azure AD 租户授予 client_credentials 进行 API 身份验证，在该租户中，Microsoft DFP 已获得许可并进行安装，以获取持有者令牌。

以下体系结构图体现了实施详情。

![显示 microsoft dynamics365 fraud protectio 体系结构示意图的图像](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户访问登录页。 用户选择“注册”以创建新帐户，并在页面中输入信息。 Azure AD B2C 收集用户属性。
| 2. | Azure AD B2C 调用 Microsoft DFP API 并传递用户属性。
| 3. | Microsoft DFP API 使用这些信息并对其进行处理，然后将结果返回 Azure AD B2C。
| 4. | Azure AD B2C 接收从 Microsoft DFP API 返回的信息。 如果它显示“失败”响应，则会向用户显示错误消息。 如果它显示“成功”响应，则会对用户进行身份验证并将该用户写入目录。

## <a name="set-up-the-solution"></a>设置解决方案

1. [创建 Facebook 应用程序](./identity-provider-facebook.md#create-a-facebook-application)，该应用程序配置为允许与 Azure AD B2C 联合。
2. [添加创建的 Facebook 机密](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key)作为 Identity Experience Framework 策略密钥。

## <a name="configure-your-application-under-microsoft-dfp"></a>在 Microsoft DFP 下配置应用程序

[设置 Azure AD 租户](/dynamics365/fraud-protection/integrate-real-time-api)以使用 Microsoft DFP。

## <a name="set-up-your-custom-domain"></a>设置自定义域

在生产环境中，必须[对 Azure AD B2C 使用自定义域](./custom-domain.md?pivots=b2c-custom-policy)并[对 Microsoft DFP 指纹服务使用自定义域](/dynamics365/fraud-protection/device-fingerprinting#set-up-dns)。 这两个服务的域应位于同一个根 DNS 区域中，以防止浏览器隐私设置阻止跨域 cookie，这在非生产环境中不是必需的。

下面是一个示例：

| 环境 | 服务 | 域 |
|:------------|:---------------|:---------------|
| 开发 | Azure AD B2C | contoso-dev.b2clogin.com |
| 开发 | Microsoft DFP 指纹 | fpt.dfp.microsoft-int.com |
| UAT | Azure AD B2C | contoso-uat.b2clogin.com |
| UAT | Microsoft DFP 指纹 | fpt.dfp.microsoft.com |
| 生产 | Azure AD B2C | login.contoso.com |
| 生产 | Microsoft DFP 指纹 | fpt.login.contoso.com |

## <a name="deploy-the-ui-templates"></a>部署 UI 模板

1. 将提供的 [Azure AD B2C UI 模板](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/ui-templates)部署到面向公众的 Internet 托管服务（例如 Azure Blob 存储）。

2. 将值 `https://<YOUR-UI-BASE-URL>/` 替换为部署位置的根 URL。

  >[!NOTE]
  >稍后需要基 URL 来配置 Azure AD B2C 策略。

3. 在 `ui-templates/js/dfp.js` 文件中，将 `<YOUR-DFP-INSTANCE-ID>` 替换为 Microsoft DFP 实例 ID。

4. 确保为 Azure AD B2C 域名 `https://{your_tenant_name}.b2clogin.com` 或 `your custom domain` 启用了 CORS。

有关详细信息，请参阅 [UI 自定义文档](./customize-ui-with-html.md?pivots=b2c-custom-policy)。

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C 配置

### <a name="add-policy-keys-for-your-microsoft-dfp-client-app-id-and-secret"></a>为 Microsoft DFP 客户端应用 ID 和机密添加策略密钥

1. 在设置了 Microsoft DFP 的 Azure AD 租户中，创建 [Azure AD 应用程序并授予管理员许可](/dynamics365/fraud-protection/integrate-real-time-api#create-azure-active-directory-applications)。
2. 为此应用程序注册创建一个机密值，并记下应用程序的客户端 ID 和客户端机密值。
3. 将客户端 ID 和客户端机密值另存为 [Azure AD B2C 租户中的策略密钥](./policy-keys-overview.md)。

 >[!NOTE]
 >稍后需要策略密钥来配置 Azure AD B2C 策略。

### <a name="replace-the-configuration-values"></a>替换配置值

在提供的[自定义策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/policies)中，查找以下占位符，并将它们替换为你实例中的相应值。

| 占位符 | 替换为 | 说明 |
| :-------- | :------------| :-----------|
|{Settings:Production} | 是否在生产模式下部署策略 | `true` 或 `false`  |
|{Settings:Tenant} | 你的租户短名称 |  `your-tenant` - 来自 your-tenant.onmicrosoft.com  |
| {Settings:DeploymentMode}    |  要使用的 Application Insights 部署模式   |  `Production` 或 `Development`  |
|  {Settings:DeveloperMode}    | 是否在 Application Insights 开发人员模式下部署策略      |   `true` 或 `false`    |
|  {Settings:AppInsightsInstrumentationKey}  |  Application Insights 实例的检测密钥*   |  `01234567-89ab-cdef-0123-456789abcdef` |
|  {Settings:IdentityExperienceFrameworkAppId}  | Azure AD B2C 租户中配置的 IdentityExperienceFramework 应用的应用 ID  | `01234567-89ab-cdef-0123-456789abcdef`|
|  {Settings:ProxyIdentityExperienceFrameworkAppId}  |   Azure AD B2C 租户中配置的 ProxyIdentityExperienceFramework 应用的应用 ID |   `01234567-89ab-cdef-0123-456789abcdef`|
| {Settings:FacebookClientId}  | 配置用于与 B2C 进行联接的 Facebook 应用的应用 ID    | `000000000000000`   |
|   {Settings:FacebookClientSecretKeyContainer}  | 将 Facebook 应用机密另存为的策略密钥的名称    |  `B2C_1A_FacebookAppSecret` |
|   {Settings:ContentDefinitionBaseUri} |  部署 UI 文件的终结点   | `https://<my-storage-account>.blob.core.windows.net/<my-storage-container>`   |
|  {Settings:DfpApiBaseUrl}   |  DFP API 实例的基路径 - 在 DFP 门户中找到   | `https://tenantname-01234567-89ab-cdef-0123-456789abcdef.api.dfp.dynamics.com/v1.0/`   |
| {Settings:DfpApiAuthScope} | DFP API 服务的 client_credentials 范围 | `https://api.dfp.dynamics-int.com/.default or https://api.dfp.dynamics.com/.default` |
| {Settings:DfpTenantId} | Azure AD 租户（而不是 B2C）的 ID，其中 DFP 已获得许可并进行安装 | `01234567-89ab-cdef-0123-456789abcdef` 或 `consoto.onmicrosoft.com` |
| {Settings:DfpAppClientIdKeyContainer} | 将 DFP 客户端 ID 另存为的策略密钥的名称 | `B2C_1A_DFPClientId` |
| {Settings:DfpAppClientSecretKeyContainer} | 将 DFP 客户端机密另存为的策略密钥的名称 | `B2C_1A_DFPClientSecret` |

*可以在任何 Azure AD 租户/订阅中设置 Application Insights。 此值是可选的，但[建议使用它来帮助调试](./troubleshoot-with-application-insights.md)。

>[!NOTE]
>将许可通知添加到“属性集合”页。 请注意，系统将记录用户的遥测和用户标识信息，以便进行帐户保护。

## <a name="configure-the-azure-ad-b2c-policy"></a>配置 Azure AD B2C 策略

1. 转到 Policies 文件夹中的 [Azure AD B2C 策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/policies)。

2. 按照此[文档](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack)的说明，下载 [LocalAccounts 入门包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

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

- [Azure AD B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)