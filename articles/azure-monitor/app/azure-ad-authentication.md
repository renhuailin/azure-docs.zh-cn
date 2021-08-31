---
title: 适用于 Application Insights 的 Azure AD 身份验证（预览）
description: 了解如何启用 Azure Active Directory (Azure AD) 身份验证，以确保在 Application Insights 资源中仅引入经过身份验证的遥测。
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: 3b50948d5dcc408595ccc8434d7fb29c07c68ab0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747685"
---
# <a name="azure-ad-authentication-for-application-insights-preview"></a>适用于 Application Insights 的 Azure AD 身份验证（预览）
Application Insights 现在支持 Azure Active Directory (Azure AD) 身份验证。 通过使用 Azure AD，现在可以确保在 Application Insights 资源中仅引入经过身份验证的遥测。 

通常，使用各种身份验证系统可能很麻烦并带来风险，因为难以大规模管理凭据。 现在可以选择退出本地身份验证，并确保在 Application Insights 资源中只引入使用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)和 [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) 专门进行身份验证的遥测。 此功能是增强遥测的安全性和可靠性的一个步骤，用于做出关键操作（警报/自动缩放等）和业务决策。

> [!IMPORTANT]
> Azure AD 身份验证目前处于预览阶段。
> 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

下面是公共预览版中不支持的 SDK/场景：
- [Application Insights Java 2.x SDK](java-2x-agent.md) – Azure AD 身份验证仅适用于 Application Insights Java Agent >=3.2.0。 
- [ApplicationInsights JavaScript Web SDK](javascript.md)。 
- 具有 Python 3.4 和 3.5 版的 [Application Insights OpenCensus Python SDK](opencensus-python.md)。
- 不建议将[基于证书/机密的 Azure AD](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) 用于生产。 改用托管标识。 
- 默认情况下，对应用服务、VM/虚拟机规模集、Azure Functions 等进行无代码监视（针对语言）。
- [可用性测试](availability-overview.md)。
- [探查器](profiler-overview.md)。

## <a name="prerequisites-to-enable-azure-ad-authentication-ingestion"></a>启用 Azure AD 身份验证引入的先决条件

- 熟悉：
    - [托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 
    - [服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。
    - [分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。 
- 你具有资源组的“所有者”角色，可以使用 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)授予访问权限。

## <a name="configuring-and-enabling-azure-ad-based-authentication"></a>配置和启用基于 Azure AD 的身份验证 

1. 使用托管标识或服务主体创建标识（如果还没有标识）：

    1. 使用托管标识（推荐）：

        [为 Azure 服务设置托管标识](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)（VM、应用服务等）。

    1. 使用服务主体（不推荐）：

        如需详细了解如何创建可以访问资源的 Azure AD 应用程序和服务主体，请参阅[创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。

1. 向 Azure 服务分配角色。 

    按照[分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)中的步骤，将“监视指标发布者”角色从目标 Application Insights 资源添加到发送遥测数据的 Azure 资源。 

    > [!NOTE]
    > 尽管“监视指标发布者”角色表示指标，但它会将所有遥测数据发布到 App Insights 资源。

1. 按照以下每种语言的配置指南操作。

### <a name="aspnet-and-net"></a>[ASP.NET 和 .NET](#tab/net)

> [!NOTE]
> 从 [版本 2.18-Beta3](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.18.0-beta3) 开始，包含对 Application Insights .NET SDK 中 Azure AD 的支持。

Application Insights .NET SDK 支持 [Azure 标识](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity#credential-classes)提供的凭据类。

- `DefaultAzureCredential` 推荐用于本地开发。
- `ManagedIdentityCredential` 推荐用于系统分配和用户分配的托管标识。
    - 对于系统分配的托管标识，使用不带参数的默认构造函数。
    - 对于用户分配的托管标识，向构造函数提供 clientId。
- `ClientSecretCredential` 推荐用于服务主体。 
    - 向构造函数提供 tenantId、clientId 和 clientSecret。

下面是使用 .NET 手动创建和配置 `TelemetryConfiguration` 的示例：

```csharp
var config = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
}
var credential = new DefaultAzureCredential();
config.SetAzureTokenCredential(credential);

```

下面是使用 ASP.NET Core 配置 `TelemetryConfiguration` 的示例：
```csharp
services.Configure<TelemetryConfiguration>(config =>
{
       var credential = new DefaultAzureCredential();
       config.SetAzureTokenCredential(credential);
});
services.AddApplicationInsightsTelemetry(new ApplicationInsightsServiceOptions
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
});
```
### <a name="nodejs"></a>[Node.js](#tab/nodejs)
 
> [!NOTE]
> 从[版本 2.1.0-beta.1](https://www.npmjs.com/package/applicationinsights/v/2.1.0-beta.1) 开始，包含对 Application Insights Node.JS 中 Azure AD 的支持。

Application Insights Node.JS 支持 [Azure 标识](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/identity/identity#credential-classes)提供的凭据类。

#### <a name="defaultazurecredential"></a>DefaultAzureCredential

```javascript
let appInsights = require("applicationinsights");
import { DefaultAzureCredential } from "@azure/identity"; 
 
const credential = new DefaultAzureCredential();
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

#### <a name="clientsecretcredential"></a>ClientSecretCredential

```javascript
let appInsights = require("applicationinsights");
import { ClientSecretCredential } from "@azure/identity"; 
 
const credential = new ClientSecretCredential(
    "<YOUR_TENANT_ID>",
    "<YOUR_CLIENT_ID>",
    "<YOUR_CLIENT_SECRET>"
  );
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

### <a name="java"></a>[Java](#tab/java)

> [!NOTE]
> 从 [Java 3.2.0-BETA](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0-BETA) 开始，包含对 Application Insights Java 代理中 Azure AD 的支持。 

1. [使用 Java 代理配置应用程序。](java-in-process-agent.md#quickstart)

    > [!IMPORTANT]
    > 使用 Java 代理配置应用时，请使用包含“IngestionEndpoint”的完整连接字符串。 例如，`InstrumentationKey=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX;IngestionEndpoint=https://XXXX.applicationinsights.azure.com/`。

    > [!NOTE]
    >  有关从 2.X SDK 迁移到 3.X Java 代理的详细信息，请参阅[从 Application Insights Java 2.x SDK 升级](java-standalone-upgrade-from-2x.md)。

1. 根据所使用的身份验证，将 json 配置添加到 ApplicationInsights.json 配置文件中。 我们建议用户使用托管标识。

#### <a name="system-assigned-managed-identity"></a>系统分配的托管标识

下面是关于如何配置 Java 代理以使用系统分配的托管标识进行 Azure AD 身份验证的示例。

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "SAMI" 
    } 
  } 
} 
```

#### <a name="user-assigned-managed-identity"></a>用户分配的托管标识

下面是关于如何配置 Java 代理以使用用户分配的托管标识进行 Azure AD 身份验证的示例。

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "UAMI", 
      "clientId":"<USER-ASSIGNED MANAGED IDENTITY CLIENT ID>" 
    } 
  }  
} 
```
:::image type="content" source="media/azure-ad-authentication/user-assigned-managed-identity.png" alt-text="用户分配的托管标识的屏幕截图。" lightbox="media/azure-ad-authentication/user-assigned-managed-identity.png":::

#### <a name="client-secret"></a>客户端机密

下面是关于如何配置 Java 代理以使用用户服务主体进行 Azure AD 身份验证的示例。 建议用户仅在开发期间使用此类型的身份验证。 添加身份验证功能的最终目标是消除机密。

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint",
   "preview": { 
        "authentication": { 
        "enabled": true, 
        "type": "CLIENTSECRET", 
        "clientId":"<YOUR CLIENT ID>", 
        "clientSecret":"<YOUR CLIENT SECRET>", 
        "tenantId":"<YOUR TENANT ID>" 
    } 
  } 
} 
```
:::image type="content" source="media/azure-ad-authentication/client-secret-tenant-id.png" alt-text="具有 tenantID 和 ClientID 的客户端机密的屏幕截图。" lightbox="media/azure-ad-authentication/client-secret-tenant-id.png":::

:::image type="content" source="media/azure-ad-authentication/client-secret-cs.png" alt-text="具有客户端机密的客户端机密的屏幕截图。" lightbox="media/azure-ad-authentication/client-secret-cs.png":::

### <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Azure AD 身份验证仅适用于 Python v2.7、v3.6 和 v3.7。 从 beta 版本 [opencensus-ext-azure 1.1b0](https://pypi.org/project/opencensus-ext-azure/1.1b0/) 开始，包含对 Application Insights Opencensus Python SDK 中 Azure AD 的支持。


构造适当的[凭据](/python/api/overview/azure/identity-readme?view=azure-python#credentials)，并将其传递给 Azure Monitor 导出程序的构造函数。 请确保使用资源的检测密钥和引入终结点设置连接字符串。

下面是 Opencensus Azure Monitor 导出程序支持的以下类型的身份验证。 建议在生产环境中使用托管标识。


#### <a name="system-assigned-managed-identity"></a>系统分配的托管标识

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential()
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="user-assigned-managed-identity"></a>用户分配的托管标识

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential(client_id="<client-id>")
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="client-secret"></a>客户端机密

```python
from azure.identity import ClientSecretCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tenant_id = "<tenant-id>"
client_id = "<client-id"
client_secret = "<client-secret>"

credential = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...
```
---

## <a name="disable-local-authentication"></a>禁用本地身份验证

启用 Azure AD 身份验证后，可以选择禁用本地身份验证。 这样，你可以引入由 Azure AD 专门验证的遥测数据，这将影响数据访问（例如通过 API 密钥访问数据）。 

可以使用 Azure 门户、Azure Policy 或以编程方式禁用本地身份验证。

### <a name="azure-portal"></a>Azure 门户

1.  在 Application Insights 资源的左侧菜单中的“配置”标题下选择“属性”。 如果已启用本地身份验证，则选择“启用（单击以更改）”。 

    :::image type="content" source="./media/azure-ad-authentication/enabled.png" alt-text="在“配置”下的“属性”中选中并启用（单击以更改）本地身份验证按钮的屏幕截图。":::

1. 选择“已禁用”并应用更改。 

    :::image type="content" source="./media/azure-ad-authentication/disable.png" alt-text="本地身份验证的屏幕截图，其中突出显示“已启用”/“已禁用”按钮。":::

1. 资源禁用本地身份验证后，“概述”窗格中将显示相应的信息。

    :::image type="content" source="./media/azure-ad-authentication/overview.png" alt-text="“概述”选项卡的屏幕截图，其中突出显示“已禁用（单击以更改）”。":::

### <a name="azure-policy"></a>Azure Policy 

“DisableLocalAuth”的 Azure 策略将拒绝用户在未将此属性设置为“true”的情况下创建新的 Application Insights 资源。 策略名称为“Application Insights 组件应阻止非 AAD 身份验证引入”。

若要将此策略应用到订阅，请[创建新的策略分配并分配该策略](../..//governance/policy/assign-policy-portal.md)。

以下是策略模板定义：
```JSON
{
    "properties": {
        "displayName": "Application Insights components should block non-AAD auth ingestion",
        "policyType": "BuiltIn",
        "mode": "Indexed",
        "description": "Improve Application Insights security by disabling log ingestion that are not AAD-based.",
        "metadata": {
            "version": "1.0.0",
            "category": "Monitoring"
        },
        "parameters": {
            "effect": {
                "type": "String",
                "metadata": {
                    "displayName": "Effect",
                    "description": "The effect determines what happens when the policy rule is evaluated to match"
                },
                "allowedValues": [
                    "audit",
                    "deny",
                    "disabled"
                ],
                "defaultValue": "audit"
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Insights/components"
                    },
                    {
                        "field": "Microsoft.Insights/components/DisableLocalAuth",
                        "notEquals": "true"                        
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effect')]"
            }
        }
    }
}
```

### <a name="programmatic-enablement"></a>编程启用 

属性 `DisableLocalAuth` 用于禁用 Application Insights 资源上的任何本地身份验证。 设置为 `true` 时，此属性强制所有访问都必须使用 Azure AD 身份验证。

下面是一个示例 Azure 资源管理器模板，可用于创建禁用本地身份验证的基于工作区的 Application Insights 资源。

```JSON 
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        },
        "disableLocalAuth": {
            "type": "bool"
        }
     
    },
    "resources": [
        {
        "name": "[parameters('name')]",
        "type": "microsoft.insights/components",
        "location": "[parameters('regionId')]",
        "tags": "[parameters('tagsArray')]",
        "apiVersion": "2020-02-02-preview",
        "dependsOn": [],
        "properties": {
            "Application_Type": "[parameters('type')]",
            "Flow_Type": "Redfield",
            "Request_Source": "[parameters('requestSource')]",
            "WorkspaceResourceId": "[parameters('workspaceResourceId')]",
            "DisableLocalAuth": "[parameters('disableLocalAuth')]"
            }
    }
 ]
}

```

## <a name="troubleshooting"></a>疑难解答

本部分提供了不同的故障排除场景和步骤，用户可以在提交支持工单之前采取这些步骤来解决问题。 

### <a name="ingestion-http-errors"></a>引入 HTTP 错误

引入服务会返回具体错误，而不考虑 SDK 语言。 可以使用 Fiddler 之类的工具收集网络流量。 应该筛选发往连接字符串中设置的引入终结点的流量。

#### <a name="http11-400-authentication-not-support"></a>不支持 HTTP/1.1 400 身份验证 

这表明 Application Insights 资源已配置为“仅限 Azure AD”，但 SDK 未正确配置且正在发送到错误的 API。

> [!NOTE]
>  “v2/track”不支持 Azure AD。 正确配置 SDK 后，遥测数据将发送到“v2.1/track”。

后续步骤应该是查看 SDK 配置。

#### <a name="http11-401-authorization-required"></a>需要 HTTP/1.1 401 授权

这表明 SDK 已正确配置，但无法获取有效的令牌。 这可能表示 Azure Active Directory 存在问题。

下一步应该是识别 SDK 日志中的异常或来自 Azure 标识的网络错误。

#### <a name="http11-403-unauthorized"></a>HTTP/1.1 403 未授权 

这表明为 SDK 配置的凭据无权访问 Application Insights 资源或订阅。

下一步应该是查看 Application Insights 资源的访问控制。 必须为 SDK 配置被授予了“监视指标发布者”角色的凭据。

### <a name="language-specific-troubleshooting"></a>特定于语言的疑难解答

### <a name="aspnet-and-net"></a>[ASP.NET 和 .NET](#tab/net)

#### <a name="event-source"></a>事件源

Application Insights .NET SDK 使用事件源发出错误日志。 若要详细了解如何收集事件源日志，请访问[疑难解答：无数据 - 使用 PerfView 收集日志](asp-net-troubleshoot-no-data.md#PerfView)。

如果 SDK 未能获取令牌，则异常消息将记录为：“未能获取 AAD 令牌。 错误消息：“

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

可以通过以下设置启用内部日志。 启用此设置后，错误日志将显示在控制台中，包括与 Azure AD 集成相关的任何错误。 例如，在提供错误凭据的情况下无法生成令牌，或在引入终结点无法使用提供的凭据进行身份验证时出错。

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").setInternalLogging(true, true);
```

### <a name="java"></a>[Java](#tab/java)

#### <a name="http-traffic"></a>HTTP 流量

可以使用 Fiddler 等工具检查网络流量。 若要使流量通过 fiddler 进行隧道传输，请在配置文件中添加以下代理设置：

```JSON
"proxy": {
"host": "localhost",
"port": 8888
}
```

或者，在运行应用程序时添加以下 jvm 参数：`-Djava.net.useSystemProxies=true -Dhttps.proxyHost=localhost -Dhttps.proxyPort=8888`

如果在代理中启用了 Azure AD，出站流量将包含 HTTP 标头“Authorization”。


#### <a name="401-unauthorized"></a>401 未授权 

如果日志文件 `WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 401, please check your credentials` 中出现以下 WARN 消息，则指示代理发送遥测数据失败。 你可能尚未对代理启用 Azure AD 身份验证，但 Application Insights 资源配置为 `DisableLocalAuth: true`。 请确保传递了有效的凭据，并且它有权访问 Application Insights 资源。


如果使用 fiddler，则可能会看到以下响应标头：`HTTP/1.1 401 Unauthorized - please provide the valid authorization token`。


#### <a name="credentialunavailableexception"></a>CredentialUnavailableException

如果日志文件 `com.azure.identity.CredentialUnavailableException: ManagedIdentityCredential authentication unavailable. Connection to IMDS endpoint cannot be established` 中显示以下异常，则指示代理未成功获取访问令牌，这可能是由于你在用户分配的托管标识配置中提供的 `clientId` 无效


#### <a name="failed-to-send-telemetry"></a>无法发送遥测数据

如果日志文件 `WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 403, please check your credentials` 中出现以下 WARN 消息，则指示代理发送遥测数据失败。 这可能是因为提供的凭据无法授予将遥测数据引入组件的权限

如果使用 fiddler，则可能会看到以下响应标头：`HTTP/1.1 403 Forbidden - provided credentials do not grant the access to ingest the telemetry into the component`。

根本原因可能是以下原因之一：
- 已在启用系统分配的托管标识的情况下创建资源，或者可能已将用户分配的标识与资源相关联，但忘记将 `Monitoring Metrics Publisher` 角色添加到资源（如果使用 SAMI）或用户分配的标识（如果使用 UAMI）。
- 已提供正确的凭据来获取访问令牌，但这些凭据不属于正确的 Application Insights 资源。 请确保资源（VM、应用服务等）或用户分配的标识在 Application Insights 资源中具有 `Monitoring Metrics Publisher` 角色。

#### <a name="invalid-tenantid"></a>无效 TenantId

如果日志文件 `com.microsoft.aad.msal4j.MsalServiceException: Specified tenant identifier <TENANT-ID> is neither a valid DNS name, nor a valid external domain.` 中显示以下异常，则指示代理未成功获取访问令牌，这可能是因为你在客户端机密配置中提供的 `tenantId` 无效/错误。

#### <a name="invalid-client-secret"></a>无效客户端机密

如果日志文件 `com.microsoft.aad.msal4j.MsalServiceException: Invalid client secret is provided` 中显示以下异常，则指示代理未成功获取访问令牌，这可能是因为你在客户端机密配置中提供的 `clientSecret` 无效。


#### <a name="invalid-clientid"></a>无效 ClientId

如果日志文件 `com.microsoft.aad.msal4j.MsalServiceException: Application with identifier <CLIENT_ID> was not found in the directory` 中显示以下异常，则指示代理未成功获取访问令牌，这可能是因为你在客户端机密配置中提供的“clientId”无效/错误

 如果应用程序尚未由租户管理员安装，或者尚未获得租户中的任何用户同意，则可能会发生这种情况。 你可能将身份验证请求发送给了错误的租户。

### <a name="python"></a>[Python](#tab/python)

#### <a name="error-starts-with-credential-error-with-no-status-code"></a>错误以“凭据错误”开头（没有状态代码）

使用的凭据有错误，并且客户端无法获取用于授权的令牌。 这通常是由于缺少状态所需的数据。 例如，传入了系统 ManagedIdentityCredential，但资源未配置为使用系统托管的标识。

#### <a name="error-starts-with-authentication-error-with-no-status-code"></a>错误以“身份验证错误”开头（没有状态代码）

客户端未能使用给定的凭据进行身份验证。 通常是由于使用的凭据没有正确的角色分配。

#### <a name="im-getting-a-status-code-400-in-my-error-logs"></a>我的错误日志中出现状态代码 400

你可能缺少凭据或凭据设置为 `None`，但 Application Insights 资源配置为 `DisableLocalAuth: true`。 请确保传递了有效的凭据，并且它有权访问 Application Insights 资源。

#### <a name="im-getting-a-status-code-403-in-my-error-logs"></a>我的错误日志中出现状态代码 403

通常，当提供的凭据未授予为 Application Insights 资源引入遥测数据的权限时会出现这种情况。 确保 AI 资源具有正确的角色分配。

---
## <a name="next-steps"></a>后续步骤
* [在门户中监视遥测](overview-dashboard.md)。
* [使用实时指标流进行诊断](live-stream.md)。
