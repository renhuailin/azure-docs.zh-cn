---
title: Azure Application Insights 中的连接字符串 | Microsoft Docs
description: 如何使用连接字符串。
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: 2b4a7ac88f92c830daecf943d6937946c4a89f82
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297438"
---
# <a name="connection-strings"></a>连接字符串

## <a name="overview"></a>概述

连接字符串为 Application Insight 用户提供了单个配置设置，无需多个代理设置。 对于希望向监视服务发送数据的 Intranet Web 服务器、主权云或混合云环境非常有用。

键值对为用户提供了一种简单的方式来为每个 Application Insights (AI) 服务/产品定义前缀后缀组合。

> [!IMPORTANT]
> 建议不要同时设置连接字符串和检测密钥。 如果用户同时设置了这两个值，则以最后设置的值为准。 

> [!TIP]
> 建议对检测密钥使用连接字符串。

## <a name="scenario-overview"></a>方案概述 

我们在其中将影响最大的此项设置可视化的客户方案：

- 防火墙例外或代理重定向 

    如果需要监视 Intranet Web 服务器，我们的早期解决方案会要求客户将各个服务终结点添加到你的配置中。 有关详细信息，请参阅[此文](../faq.yml#can-i-monitor-an-intranet-web-server-)。 
    连接字符串只需进行单个设置，简化了此工作，因此是一种更好的选择。 简单的前缀后缀修正，允许自动填充所有终结点并将其重定向到正确的服务。 

- 主权云或混合云环境

    用户可以将数据发送到已定义的 [Azure 政府区域](../../azure-government/compare-azure-government-global-azure.md#application-insights)。
    使用连接字符串可以定义 Intranet 服务器或混合云设置的终结点设置。 

## <a name="getting-started"></a>入门

### <a name="finding-my-connection-string"></a>如何查找我的连接字符串？

连接字符串显示在 Application Insights 资源的“概览”边栏选项卡上。

![“概览”边栏选项卡上的连接字符串](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>架构

#### <a name="max-length"></a>最大长度

连接的最大支持长度为 4096 个字符。

#### <a name="key-value-pairs"></a>键值对

连接字符串由以分号分隔的键/值对表示的设置列表组成：`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>语法

- `InstrumentationKey`（例如：00000000-0000-0000-0000-000000000000）连接字符串是必填字段。
- `Authorization`（例如：ikey）（此设置为可选，因为目前我们仅支持 ikey 授权。）
- `EndpointSuffix`（例如：applicationinsights.azure.cn）设置终结点后缀将指示 SDK 连接到哪个 Azure 云。 SDK 将为各个服务组装终结点的其余部分。
- 显式终结点。
  任何服务均可在连接字符串中显式重写。
   - `IngestionEndpoint`（例如：`https://dc.applicationinsights.azure.com`）
   - `LiveEndpoint`（例如：`https://live.applicationinsights.azure.com`）
   - `ProfilerEndpoint`（例如：`https://profiler.monitor.azure.com`）
   - `SnapshotEndpoint`（例如：`https://snapshot.monitor.azure.com`）

#### <a name="endpoint-schema"></a>终结点架构

`<prefix>.<suffix>`
- 前缀：定义某个服务。 
- 后缀：定义公共域名。

##### <a name="valid-suffixes"></a>有效后缀

下面是有效后缀的列表 
- applicationinsights.azure.cn
- applicationinsights.us


另请参阅：[需要修改终结点的区域](./custom-endpoints.md#regions-that-require-endpoint-modification)


##### <a name="valid-prefixes"></a>有效的前缀

- [遥测引入](./app-insights-overview.md)：`dc`
- [实时指标](./live-stream.md)：`live`
- [探查器](./profiler-overview.md)：`profiler`
- [快照](./snapshot-debugger.md)：`snapshot`



## <a name="connection-string-examples"></a>连接字符串示例


### <a name="minimal-valid-connection-string"></a>最小有效连接字符串

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

在此示例中，仅设置了检测密钥。

- 授权方案默认为“ikey” 
- 检测密钥：00000000-0000-0000-0000-000000000000
- 区域服务 URI 基于 [SDK 默认值](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs)，并将连接到公共全局 Azure：
   - 引入：`https://dc.services.visualstudio.com/`
   - 实时指标：`https://rt.services.visualstudio.com/`
   - 探查器：`https://profiler.monitor.azure.com/`
   - 调试程序：`https://snapshot.monitor.azure.com/`



### <a name="connection-string-with-endpoint-suffix"></a>带有终结点后缀的连接字符串

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

在此示例中，此连接字符串指定终结点后缀，SDK 将构造服务终结点。

- 授权方案默认为“ikey” 
- 检测密钥：00000000-0000-0000-0000-000000000000
- 区域服务 URI 基于所提供的终结点后缀： 
   - 引入：`https://dc.ai.contoso.com`
   - 实时指标：`https://live.ai.contoso.com`
   - 探查器：`https://profiler.ai.contoso.com`
   - 调试程序：`https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>带有显式终结点重写的连接字符串 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

在此示例中，此连接字符串指定每个服务的显式重写。 SDK 将使用所提供的确切终结点而不进行修改。

- 授权方案默认为“ikey” 
- 检测密钥：00000000-0000-0000-0000-000000000000
- 区域服务 URI 基于显式重写值： 
   - 引入：`https://custom.com:111/`
   - 实时指标：`https://custom.com:222/`
   - 探查器：`https://custom.com:333/`
   - 调试程序：`https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>如何设置连接字符串

以下 SDK 版本支持连接字符串：
- .NET 和 .NET Core v2.12.0
- Java v2.5.1 和 Java 3.0
- JavaScript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

可以通过代码、环境变量或配置文件设置连接字符串。



### <a name="environment-variable"></a>环境变量

- 连接字符串： `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="code-samples"></a>代码示例

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

设置属性 [TelemetryConfiguration.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274) 或 [ApplicationInsightsServiceOptions.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/81288f26921df1e8e713d31e7e9c2187ac9e6590/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs#L66-L69)

.NET 显式设置：
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET 配置文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore 显式设置：
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.json： 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)

可以在 `applicationinsights.json` 配置文件中设置连接字符串：

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
}
```

请参阅[连接字符串配置](./java-standalone-config.md#connection-string)，了解更多详细信息。

对于 Application Insights Java 2.x，可以在 `ApplicationInsights.xml` 配置文件中设置连接字符串：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

重要说明：JavaScript 不支持使用环境变量。

使用代码片段：

当前代码片段（下面列出）为版本“5”，该版本在代码片段中编码为 sv:"#"，[可在 GitHub 上找到当前版本](https://go.microsoft.com/fwlink/?linkid=2156318)。

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}});
</script>
```

> [!NOTE]
> 为了提高可读性并减少可能的 JavaScript 错误，在上述代码片段的新行上列出了所有可能的配置选项，如果不希望更改注释行的值，可以将其删除。

手动设置：
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

我们建议用户设置环境变量。

若要显式设置连接字符串，请执行以下代码：

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```

---

## <a name="next-steps"></a>后续步骤

在运行时开始使用：

* [Azure VM 和 Azure 虚拟机规模集的托管 IIS 应用](./azure-vm-vmss-apps.md)
* [IIS 服务器](./status-monitor-v2-overview.md)
* [Azure Web 应用](./azure-web-apps.md)

在开发时开始使用：

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)