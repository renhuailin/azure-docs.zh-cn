---
title: Azure Application Insights IP 地址收集 | Microsoft Docs
description: 了解 Application Insights 如何处理 IP 地址和地理位置。
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js, devx-track-azurepowershell
ms.openlocfilehash: 28b3fb6242d496be7962961d36db3a85c6187d8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745892"
---
# <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 地址处理

本文介绍了 Application Insights 中的地理位置查找和 IP 地址处理工作的方式，以及如何修改默认行为。

## <a name="default-behavior"></a>默认行为

默认情况下，IP 地址是暂时收集的，不会存储在 Application Insights 中。 基本过程如下：

将遥测数据发送到 Azure 时，Application Insights 使用 IP 地址通过 [MaxMind 中的 GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) 执行地理位置查找。 Application Insights 使用此查找结果填充字段 `client_City`、`client_StateOrProvince` 和 `client_CountryOrRegion`。 然后放弃该地址，并将 `0.0.0.0` 写入 `client_IP` 字段。 

遥测类型包括：

* 浏览器遥测：Application Insights 收集发送方的 IP 地址。 ingestion 终结点计算 IP 地址。
* 服务器遥测：Application Insights 遥测模块暂时收集客户端 IP 地址。 设置 `X-Forwarded-For` 标头时，不会在本地收集 IP 地址。 当 IP 地址的传入列表具有多个 IP 地址时，最后一个 IP 地址用于填充地理位置字段。

此行为是有意设计的，目的是帮助避免不必要地收集个人数据。 我们建议尽量避免收集个人数据。 

> [!NOTE]
> 虽然默认不收集 IP 地址，但可以重写此行为。 但是，建议验证集合是否违反任何合规性要求或当地法规。 
>
> 若要了解有关在 Application Insights 中处理个人数据的详细信息，请参阅[个人数据指南](../logs/personal-data-mgmt.md)。


## <a name="storage-of-ip-address-data"></a>存储 IP 地址数据

若要启用 IP 收集和存储，必须将 Application Insights 组件的 `DisableIpMasking` 属性设置为 `true`。 可以通过 Azure 资源管理器模板或通过调用 REST API 来设置此属性。 

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

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

1. 转到 Application Insights 资源，然后选择“自动化” > “导出模板”。  

2. 选择“部署”。

    ![屏幕截图：显示红色突出显示的“部署”按钮。](media/ip-collection/deploy.png)

3. 选择“编辑模板”。

    ![屏幕截图：显示红色突出显示的“编辑”按钮及资源组相关警告。](media/ip-collection/edit-template.png)

    > [!NOTE]
    > 如果遇到以下错误（如屏幕截图）所示，可以解决此问题：“资源组位于模板中一个或多个资源不支持的位置。 请选择其他资源组。” 暂时从下拉列表中选择不同的资源组，然后重新选择原始资源组。

4. 找到 JSON 模板 `resources` 中的 `properties`，在最后一个 JSON 字段添加一个逗号，然后添加以下新行：`"DisableIpMasking": true`。 再选择“保存”。

    ![屏幕截图：显示在请求源属性后添加逗号和新行。](media/ip-collection/save.png)

5. 选择“查看 + 创建” > “创建”。

    > [!NOTE]
    > 如果看到“部署失败”，请查看部署详细信息，查找类型为 `microsoft.insights/components` 的部署详细信息，并检查状态。 如果成功，则部署对 `DisableIpMasking` 做的更改。

6. 部署完成后，将会记录新的遥测数据。

    如果再次选择并编辑模板，则仅显示默认模板，而不会显示新添加的属性。 如果看不到 IP 地址数据，并且想要确认 `"DisableIpMasking": true` 已设置，请运行以下 PowerShell 命令：
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using Azure Cloud Shell, you need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    最后返回一份属性列表。 其中一个属性显示为 `DisableIpMasking: true`。 如果在使用 Azure 资源管理器部署新属性之前运行 PowerShell 命令，该属性将不存在。

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

如果需要比 `DisableIpMasking` 更灵活的替代方法，可以使用[遥测初始化表达式](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)将 IP 地址的部分或全部复制到自定义字段。 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet-or-aspnet-core"></a>ASP.NET 或 ASP.NET Core

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
> 如果无法访问 `ISupportProperties`，请确保运行的是最新稳定版 Application Insights SDK。 `ISupportProperties` 适用于高基数值。 `GlobalProperties` 更适用于低基数值，如区域名称和环境名称。

### <a name="enable-the-telemetry-initializer-for-aspnet"></a>为 ASP.NET 启用遥测初始化表达式

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

### <a name="enable-the-telemetry-initializer-for-aspnet-core"></a>为 ASP.NET Core 启用遥测初始化表达式

可以使用与 ASP.NET 相同的方法为 ASP.NET Core 创建遥测初始化表达式。 若要启用初始化表达式，请使用以下示例作为参考：

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

若要直接在客户端计算 IP 地址，需要添加自己的自定义逻辑，并使用结果设置 `ai.location.ip` 标记。 设置 `ai.location.ip` 后，引入终结点不会执行 IP 地址计算，并且提供的 IP 地址将用于地理位置查找。 在这种情况下，IP 地址默认仍归零。 

若要保留根据自定义逻辑计算的整个 IP 地址，可以使用遥测初始化表达式，将 `ai.location.ip` 中提供的 IP 地址数据复制到单独的自定义字段。 但同样，与服务器端 SDK 不同，如果客户端 SDK 无法依赖于第三方库或你自己的自定义逻辑，则它不会计算地址。    


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

如果客户端数据在转发到引入终结点之前遍历某个代理，则 IP 地址计算可能会显示该代理（而不是客户端）的 IP 地址。 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>查看遥测初始化表达式的结果

如果将新流量发送到站点并等待几分钟，可以运行查询来确认集合是否正常工作：

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

新收集的 IP 地址将显示在 `customDimensions_client-ip` 列中。 默认 `client-ip` 列仍将所有四个八进制数归零。 

如果要从 localhost 进行测试，并且 `customDimensions_client-ip` 的值为 `::1`，则此值是预期行为。 `::1` 值是 IPv6 格式的环回地址。 它等效于 IPv4 中的 `127.0.0.1`。

## <a name="next-steps"></a>后续步骤

* 详细了解 Application Insights 中的[个人数据收集](../logs/personal-data-mgmt.md)。

* 详细了解 Application Insights 中 [IP 地址收集](https://apmtips.com/posts/2016-07-05-client-ip-address/)的工作原理。 本文是由我们的某位工程师早前撰写的一篇外部博客文章。 它在当前默认行为（其中 IP 地址记录为 `0.0.0.0`）之前，但深入探讨了内置遥测初始化表达式的机制。
