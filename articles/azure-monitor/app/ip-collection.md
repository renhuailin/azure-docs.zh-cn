---
title: Azure Application Insights IP 地址收集 | Microsoft Docs
description: 了解如何使用 Azure Application Insights 处理 IP 地址和地理位置
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js, devx-track-azurepowershell
ms.openlocfilehash: 8115ea2334d0ea90ef0e31bb857f3e76154912ce
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110082533"
---
# <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 地址处理

本文介绍 Application Insights 中的地理位置查找和 IP 地址处理工作的方式，以及如何修改默认行为。

## <a name="default-behavior"></a>默认行为

默认情况下，IP 地址是暂时收集的，不会存储在 Application Insights 中。 基本过程如下：

将遥测发送到 Azure 后，将使用 IP 地址通过[来自 MaxMind 的 GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) 进行地理位置查找。 此查找结果用于填充字段 `client_City`、`client_StateOrProvince` 和 `client_CountryOrRegion`。 然后放弃该地址，并将 `0.0.0.0` 写入 `client_IP` 字段。

* 浏览器遥测：我们会暂时收集发送方的 IP 地址。 IP 地址由引入终结点计算。
* 服务器遥测：Application Insights 遥测模块暂时收集客户端 IP 地址。 设置 `X-Forwarded-For` 标头时，不会在本地收集 IP 地址。

此行为是有意设计的，目的是帮助避免不必要地收集个人数据。 我们建议尽量避免收集个人数据。 

## <a name="overriding-default-behavior"></a>重写默认行为

而默认设置是不收集 IP 地址。 我们仍提供灵活选择来替代此行为。 但是，建议验证集合是否违反任何合规性要求或当地法规。 

若要详细了解 Application Insights 中的个人数据处理，请参阅[个人数据指南](../logs/personal-data-mgmt.md)。

## <a name="storing-ip-address-data"></a>存储 IP 地址数据

若要启用 IP 收集和存储，必须将 Application Insights 组件的 `DisableIpMasking` 属性设置为 `true`。 可以通过 Azure 资源管理器模板或调用 REST API 来设置此属性。 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>门户

如果只需修改单个 Application Insights 资源的行为，请使用 Azure 门户。 

1. 转到你的 Application Insights 资源并选择“自动化” > “导出模板”  

2. 选择“部署”

    ![用红色标注的带有“部署”字样的按钮](media/ip-collection/deploy.png)

3. 选择“编辑模板”。

    ![用红色标注的带有“编辑”字样的按钮](media/ip-collection/edit-template.png)

4. 对资源的 json 进行以下更改，然后选择“保存”：

    ![在屏幕截图中，“IbizaAIExtension”的后面添加了一个逗号，并在下面添加了包含 "DisableIpMasking": true 的新行](media/ip-collection/save.png)

    > [!WARNING]
    > 如果遇到了包含以下消息的错误：“资源组所在的位置不受模板中的一个或多个资源支持。请选择其他资源组。” 请从下拉列表中暂时选择另一个资源组，然后重新选择原始资源组来解决此错误。

5. 选择“查看 + 创建” > “创建”。

    > [!NOTE]
    > 如果看到“部署失败”，请查看部署详细信息，查找类型为“microsoft.insights/components”的部署详细信息，并检查状态。 如果成功，则部署对 DisableIpMasking 做的更改。

6. 部署完成后，将会记录新的遥测数据。

    如果再次选择并编辑模板，则仅显示默认模板，而不会显示新添加的属性。 如果未看到 IP 地址数据并希望确认是否已设置 `"DisableIpMasking": true`，请运行以下 PowerShell：
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    将返回属性列表作为结果。 其中一个属性显示为 `DisableIpMasking: true`。 如果在使用 Azure 资源管理器部署新属性之前运行 PowerShell，该属性将不存在。

### <a name="rest-api"></a>REST API

用于做出相同修改的 [REST API](/rest/api/azure/) 有效负载如下：

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>遥测初始化程序

如果需要比 `DisableIpMasking` 更灵活的替代方法，则可以使用[遥测初始化表达式](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)将全部或部分 IP 地址复制到自定义字段。 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet--aspnet-core"></a>ASP.NET / ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> 如果无法访问 `ISupportProperties`，请检查并确保运行最新稳定版本的 Application Insights SDK。 `ISupportProperties` 适合用于高基数值，而 `GlobalProperties` 更适合用于低基数值，如区域名称、环境名称等。 

### <a name="enable-telemetry-initializer-for-aspnet"></a>为 ASP.NET 启用遥测初始化表达式

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>为 ASP.NET Core 启用遥测初始化表达式

对于 ASP.NET Core，可以采用与 ASP.NET 相同的方式创建遥测初始化表达式，但若要启用初始化表达式，请参考以下示例：

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[客户端 JavaScript](#tab/javascript)

### <a name="client-side-javascript"></a>客户端 JavaScript

与服务器端 SDK 不同，客户端 JavaScript SDK 不会计算 IP 地址。 默认情况下，客户端遥测的 IP 地址计算在 Azure 中的引入终结点处执行。 

如果要直接在客户端上计算 IP 地址，则需要添加自己的自定义逻辑，并使用结果设置 `ai.location.ip` 标记。 设置 `ai.location.ip` 时，引入终结点不执行 IP 地址计算，而提供的 IP 地址用于地理位置查找。 在这种情况下，IP 地址默认仍会归零。 

若要保留自定义逻辑计算的整个 IP 地址，可以使用遥测初始化表达式将 `ai.location.ip` 中提供的 IP 地址数据复制到单独的自定义字段。 但同样，与服务器端 SDK 不同，如果不依赖第三方库或你自己的自定义集合逻辑，客户端 SDK 将不会计算地址。    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

如果客户端数据在转发到引入终结点之前遍历代理，则 IP 地址计算可能会显示代理（而不是客户端）的 IP 地址。 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>查看遥测初始化表达式的结果

如果将新流量发送到站点，请等待几分钟。 然后，可以运行查询，确认集合正常工作：

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

新收集的 IP 地址将显示在 `customDimensions_client-ip` 列中。 默认的 `client-ip` 列仍将四个八进制数全部归零。 

如果从 localhost 进行测试，并且 `customDimensions_client-ip` 的值为 `::1`，则此值为预期行为。 `::1` 表示 IPv6 中的环回地址。 它等效于 IPv4 中的 `127.0.0.1`。

## <a name="next-steps"></a>后续步骤

* 详细了解 Application Insights 中的[个人数据收集](../logs/personal-data-mgmt.md)。

* 详细了解 Application Insights 中 [IP 地址收集](https://apmtips.com/posts/2016-07-05-client-ip-address/)的工作原理。 （本文是由我们的某位工程师早前撰写的一篇外部博客文章。 其中所述的机制不同当前的默认行为，现在，IP 地址将记录为 `0.0.0.0`，不过此文更深入地描述了内置 `ClientIpHeaderTelemetryInitializer` 的机制。）
