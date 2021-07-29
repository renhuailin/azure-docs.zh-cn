---
title: 适用于 JavaScript Web 应用的 Azure Application Insights
description: 获取页面视图和会话计数、Web 客户端数据、单页应用程序 (SPA)，以及跟踪使用模式。 检测 JavaScript 网页中的异常和性能问题。
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 61b7aa455cf9b782ca10d749344c26f5d15caa40
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110072507"
---
# <a name="application-insights-for-web-pages"></a>适用于网页的 Application Insights

了解网页或应用的性能和使用情况。 如果将 [Application Insights](app-insights-overview.md) 添加到页面脚本，可以获取页面加载和 AJAX 调用的时间、浏览器异常和 AJAX 失败的计数和详细信息，以及用户和会话计数。 所有这些信息可按页面、客户端 OS 和浏览器版本、地理位置和其他维度细分。 可以针对失败计数或页面加载缓慢情况设置警报。 并且通过在 JavaScript 代码中插入跟踪调用，可以跟踪网页应用程序的不同功能的使用情况。

可以在任何网页中使用 Application Insights - 刚刚添加了 JavaScript 的简短片段。 如果 Web 服务是 [Java](java-in-process-agent.md) 或 [ASP.NET](asp-net.md)，则你可以将服务器端 SDK 与客户端 JavaScript SDK 结合使用，以全方面地了解应用的性能。

## <a name="adding-the-javascript-sdk"></a>添加 JavaScript SDK

> [!IMPORTANT]
> 建议使用[连接字符串](./sdk-connection-string.md?tabs=js)替代检测密钥。 新的 Azure 区域要求使用连接字符串而不是检测密钥。 连接字符串用于标识要与遥测数据关联的资源。 它还允许你修改可供你的资源将其用作遥测目标的终结点。 你需要复制连接字符串，并将其添加到应用程序的代码或环境变量中。

1. 首先需要一个 Application Insights 资源。 如果你尚未获得资源和检测密钥，请遵照[有关创建新资源的说明](create-new-resource.md)。
2. 复制你要将 JavaScript 遥测数据发送到的资源（从步骤 1）的检测密钥（也称为“iKey”）或[连接字符串](#connection-string-setup)。你需要将该密钥添加到 Application Insights JavaScript SDK 的 `instrumentationKey` 或 `connectionString` 设置。
3. 通过以下两个选项之一，将 Application Insights JavaScript SDK 添加到网页或应用：
    * [npm 设置](#npm-based-setup)
    * [JavaScript 代码片段](#snippet-based-setup)

> [!IMPORTANT]
> 仅使用一种方法将 JavaScript SDK 添加到应用程序。 如果使用 NPM 安装程序，请不要使用代码片段，反之亦然。

> [!NOTE]
> NPM 安装程序会将 JavaScript SDK 作为依赖项安装到项目中，启用 IntelliSense，而代码片段则会在运行时获取 SDK。 两者都支持相同的功能。 但是，需要更多自定义事件和配置的开发人员通常会选择 NPM 安装程序，而需要快速启用现成 Web 分析的用户则选择代码片段。

### <a name="npm-based-setup"></a>基于 npm 的设置

通过 NPM 安装。

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> Typings 已包含在此包中，因此无需安装单独的 typings 包 。
    
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>基于代码片段的设置

如果应用不使用 npm，则可以通过直接使用 Application Insights 来检测网页：只需将此代码片段粘贴到每个页面的顶部即可。 最好是将它用作 `<head>` 节中的第一个脚本，以便它可以监视所有依赖项存在的任何潜在问题或任何 JavaScript 问题。 如果使用的是 Blazor Server 应用，请在文件 `_Host.cshtml` 的顶部 `<head>` 部分中添加代码片段。

为了帮助跟踪应用程序使用的代码片段版本，从版本 2.5.5 开始，页面视图事件将包含一个新的标记“ai.internal.snippet”，该标记将包含标识的代码片段版本。

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
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> 为了提高可读性并减少可能的 JavaScript 错误，在上述代码片段的新行上列出了所有可能的配置选项，如果不希望更改注释行的值，可以将其删除。


#### <a name="reporting-script-load-failures"></a>报告脚本加载失败

此版本的代码片段检测并报告在将 SDK 作为扩展从 CDN 加载到 Azure Monitor 门户时出现的故障（在“故障”&gt;“异常”&gt;“浏览器”下），此异常提供此类故障的可见性，使你知道应用程序不会按预期方式报告遥测（或其他异常）。 此信号对于了解以下情况至关重要：由于未加载或初始化 SDK，因此缺少遥测数据，这可能导致：
- 漏报用户使用（或尝试使用）站点的方式；
- 缺少关于最终用户使用站点的方式的遥测数据；
- 遗漏可能阻止最终用户成功使用站点的 JavaScript 错误。

有关此异常的详细信息，请参阅 [SDK 加载失败](javascript-sdk-load-failure.md)疑难解答页面。

将此失败作为门户异常进行报告时不会使用 Application Insights 配置中的 ```disableExceptionTracking``` 配置选项，因此，如果发生此失败，则将始终通过代码片段进行报告，即使禁用了 window.onerror 支持也是如此。

IE 8（或更低版本）尤其不支持报告 SDK 加载失败。 假设大多数环境都不仅仅是 IE 8 或更低版本，这有助于减少代码片段的缩小版大小。 如果你有此要求并且希望收到这些异常，则需要包括 fetch polyfill，或创建自己的代码片段版本，使其使用 ```XDomainRequest``` 而不是 ```XMLHttpRequest```，建议使用[提供的代码片段源代码](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js)作为起点。

> [!NOTE]
> 如果你使用的是早期版本的代码片段，则强烈建议你更新到最新版本，以便收到以前未报告的问题。

#### <a name="snippet-configuration-options"></a>代码片段配置选项

现在，所有配置选项都已移到脚本的末尾，以避免意外引入 JavaScript 错误，这类错误不仅会导致 SDK 加载失败，而且还会阻止报告此失败。

每个配置选项都会显示在一个新行上，如果不希望覆盖作为 [可选] 列出的项的默认值，则可以删除该行以最大程度地减小所返回页面的大小。

可用配置有：
 
| 名称 | 类型 | 说明
|------|------|----------------
| src | 字符串 [必需] | 要从中加载 SDK 的完整 URL。 此值用于动态添加的 &lt;script /&gt; 标记的“src”属性。 你可以使用公共 CDN 位置，也可以使用自己的私有托管位置。
| name | 字符串 [可选] | 已初始化的 SDK 的全局名称，默认值为 `appInsights`。 因此 ```window.appInsights``` 将是对已初始化实例的引用。 注意：如果提供一个名称值或上一个实例似乎是通过全局名称 appInsightsSDK 分配的，则此名称值也将在全局命名空间中定义为 ```window.appInsightsSDK=<name value>```，SDK 初始化代码需要此名称，以确保它正在初始化和更新的代码片段主干和代理方法正确。
| ld | 毫秒数 [可选] | 定义在尝试加载 SDK 之前要等待的加载延迟。 默认值为 0 毫秒，任何负值都表示将立即向页面的 &lt;head&gt; 区域添加脚本标记，然后在加载脚本（或失败）之前阻止页面加载事件。
| useXhr | 布尔 [可选] | 此设置仅用于报告 SDK 加载失败。 报告将首先尝试使用 fetch()（如果可用），然后回退到 XHR，将此值设置为 true 即可绕过提取检查。 仅当在提取将无法发送失败事件的环境中使用应用程序时，才需要使用此值。
| crossOrigin | 字符串 [可选] | 通过包含此设置，添加以下载 SDK 的脚本标记将包含带有此字符串值的 crossOrigin 属性。 如果未定义（默认值），则不添加 crossOrigin 属性。 未定义建议的值（默认值）;""; 或“anonymous”（如需了解所有有效值，请参阅 [HTML 属性：`crossorigin`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) 文档）
| cfg | 对象 [必需] | 初始化期间传递到 Application Insights SDK 的配置。

### <a name="connection-string-setup"></a>连接字符串设置

对于 NPM 或代码片段设置，还可以使用连接字符串配置 Application Insights 的实例。 只需将 `instrumentationKey` 字段替换为 `connectionString` 字段。
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="sending-telemetry-to-the-azure-portal"></a>将遥测数据发送到 Azure 门户

默认情况下，Application Insights JavaScript SDK 会自动收集许多遥测项，这些项有助于确定应用程序和底层用户体验的运行状况。 其中包括：

- 应用中 **未捕获到的异常**，包括以下相关信息
    - 堆栈跟踪
    - 异常详细信息和错误随附的消息
    - 错误的行号与列号
    - 引发错误的 URL
- 应用发出的 **网络依赖项请求**：**XHR** 和 **FETCH**（默认已禁用提取集合）请求，包括以下相关信息
    - 依赖项源的 URL
    - 用于请求依赖项的命令和方法
    - 请求持续时间
    - 请求的结果代码和成功状态
    - 发出请求的用户的 ID（如果有）
    - 发出请求的关联上下文（如果有）
- **用户信息**（例如位置、网络、IP）
- **设备信息**（例如，浏览器、OS、版本、语言、型号）
- **会话信息**

### <a name="telemetry-initializers"></a>遥测初始化表达式
遥测初始化表达式用于在从用户浏览器发送收集的遥测内容之前先对其进行修改。 它们还可用于返回 `false` 以阻止发送某些遥测数据。 可将多个遥测初始化表达式添加到 Application Insights 实例，它们将按添加顺序执行。

`addTelemetryInitializer` 的输入参数是一个回调，该回调采用 [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) 作为参数，并返回 `boolean` 或 `void`。 如果返回 `false`，则不发送遥测项，否则将继续处理下一个遥测初始化表达式（如果有），或者将遥测项发送到遥测集合终结点。

使用遥测初始化表达式的示例：
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>配置
大多数配置字段的命名都可默认为 false。 除 `instrumentationKey` 以外的所有字段都是可选的。

| 名称 | 描述 | 默认 |
|------|-------------|---------|
| instrumentationKey | **必需**<br>从 Azure 门户获取的检测密钥。 | string<br/>NULL |
| accountId | 可选的帐户 ID（如果应用将用户分组到帐户中）。 不允许使用空格、逗号、分号、等于或竖线 | string<br/>NULL |
| sessionRenewalMs | 如果用户处于非活动状态有这么长的时间（以毫秒为单位），则会记录会话。 | numeric<br/>1800000<br/>（30 分钟） |
| sessionExpirationMs | 如果会话持续了这么长的时间（以毫秒为单位），则会记录会话。 | numeric<br/>86400000<br/>（24 小时） |
| maxBatchSizeInBytes | 遥测批的最大大小。 如果某个批超过此限制，则立即发送此批，并启动新批 | numeric<br/>10000 |
| maxBatchInterval | 发送前要批处理遥测数据的时间长短（毫秒） | numeric<br/>15000 |
| disable&#8203;ExceptionTracking | 如果为 true，则不自动收集异常。 | boolean<br/> false |
| disableTelemetry | 如果为 true，则不收集或发送遥测数据。 | boolean<br/>false |
| enableDebug | 如果为 true，则不管 SDK 日志记录设置如何，**内部** 调试数据都将引发为异常，**而不是** 记录这些数据。 默认值为 false。 <br>注意：如果启用此设置，每当发生内部错误时，都会导致遥测数据丢失。 这可能有利于快速识别 SDK 的配置或用法问题。 如果你不希望在调试时丢失遥测数据，请考虑使用 `consoleLoggingLevel` 或 `telemetryLoggingLevel`，而不是 `enableDebug`。 | boolean<br/>false |
| loggingLevelConsole | 将 **内部** Application Insights 错误记录到控制台。 <br>0：关闭， <br>1:仅限严重错误， <br>2:所有内容（错误和警告） | numeric<br/> 0 |
| loggingLevelTelemetry | 将 **内部** Application Insights 错误作为遥测数据发送。 <br>0：关闭， <br>1:仅限严重错误， <br>2:所有内容（错误和警告） | numeric<br/> 1 |
| diagnosticLogInterval | 内部日志记录队列的（内部）轮询间隔（以毫秒为单位） | numeric<br/> 10000 |
| samplingPercentage | 要发送的事件百分比。 默认值为 100，表示发送所有事件。 如果你希望避免大型应用程序达到数据上限，请设置此项。 | numeric<br/>100 |
| autoTrackPageVisitTime | 如果为 true，则对于页面视图，将跟踪前一个检测的页面的查看时间并将其作为遥测数据发送，同时，为当前的页面视图启动新的计时器。 | boolean<br/>false |
| disableAjaxTracking | 如果为 true，则不自动收集 Ajax 调用。 | boolean<br/> false |
| disableFetchTracking | 如果为 true，则不自动收集 Fetch 请求。|boolean<br/>是 |
| overridePageViewDuration | 如果为 true，则在调用 trackPageView 时，trackPageView 的默认行为将更改为记录页面视图持续时间间隔的结束时间。 如果为 false 且未为 trackPageView 提供自定义持续时间，则会使用导航计时 API 计算页面视图性能。 |boolean<br/>
| maxAjaxCallsPerView | 默认值为 500 - 控制每个页面视图将监视多少个 Ajax 调用。 设置为 -1 可监视页面上的所有（无限制）Ajax 调用。 | numeric<br/> 500 |
| disableDataLossAnalysis | 如果为 false，则对于尚未发送的项，启动时将检查内部遥测发送方缓冲区。 | boolean<br/> 是 |
| disable&#8203;CorrelationHeaders | 如果为 false，则 SDK 会将两个标头（“Request-Id”和“Request-Context”）添加到所有依赖项请求，以将其关联到服务器端上的对应请求。 | boolean<br/> false |
| correlationHeader&#8203;ExcludedDomains | 禁用特定域的关联标头 | string[]<br/>undefined |
| correlationHeader&#8203;ExcludePatterns | 使用正则表达式禁用关联标头 | regex[]<br/>undefined |
| correlationHeader&#8203;Domains | 启用特定域的关联标头 | string[]<br/>undefined |
| disableFlush&#8203;OnBeforeUnload | 如果为 true，则触发 onBeforeUnload 事件时不会调用 flush 方法 | boolean<br/> false |
| enableSessionStorageBuffer | 如果为 true，则会将包含所有未发送的遥测数据的缓冲区存储在会话存储中。 加载页面时会还原该缓冲区 | boolean<br />是 |
| cookieCfg | 启用 Cookie 使用的默认设置，请参阅 [ICookieCfgConfig](#icookiemgrconfig) 设置，了解完整的默认值。 | [ICookieCfgConfig](#icookiemgrconfig)<br>（自 2.6.0 起）<br/>undefined |
| ~~isCookieUseDisabled~~<br>disableCookiesUsage | 如果为 true，则 SDK 不会存储或读取 Cookie 中的任何数据。 请注意，这会禁用用户和会话 Cookie，令使用情况边栏选项卡和体验无效。 isCookieUseDisable 已弃用，取而代之的是 disableCookiesUsage，当两者都提供时，则优先考虑 disableCookiesUsage。<br>（自 v2.6.0 起）此外，如果还定义了 `cookieCfg.enabled`，则其优先级高于这些值，Cookie 的使用可以在初始化后通过 core.getCookieMgr().setEnabled(true) 重新启用。 | [`cookieCfg.enabled`](#icookiemgrconfig) 的别名<br>false |
| cookieDomain | 自定义 Cookie 域。 若要跨子域共享 Application Insights Cookie，此字段会有帮助。<br>（自 v2.6.0 起）如果定义了 `cookieCfg.domain`，则其优先级高于此值。 | [`cookieCfg.domain`](#icookiemgrconfig) 的别名<br>null |
| cookiePath | 自定义 cookie 路径。 如果要在应用程序网关后共享 Application Insights cookie，这会很有帮助。<br>如果定义了 `cookieCfg.path`，则其优先级高于此值。 | [`cookieCfg.path`](#icookiemgrconfig) 的别名<br>（自 2.6.0 起）<br/>null |
| isRetryDisabled | 如果为 false，则出现代码 206（部分成功）、408（超时）、429（请求过多）、500（内部服务器错误）、503（服务不可用）和 0（脱机，仅当已检测到此状态时）时会重试 | boolean<br/>false |
| isStorageUseDisabled | 如果为 true，则 SDK 不会存储或读取本地和会话存储中的任何数据。 | boolean<br/> false |
| isBeaconApiDisabled | 如果为 false，则 SDK 将使用[信标 API](https://www.w3.org/TR/beacon) 发送所有遥测数据 | boolean<br/>是 |
| onunloadDisableBeacon | 选项卡关闭时，SDK 将使用[信标 API](https://www.w3.org/TR/beacon) 发送所有剩余的遥测 | boolean<br/> false |
| sdkExtension | 设置 SDK 扩展名。 仅允许使用字母字符。 扩展名将添加为“ai.internal.sdkVersion”标记的前缀（例如“ext_javascript:2.0.0”）。 | string<br/> NULL |
| isBrowserLink&#8203;TrackingEnabled | 如果为 true，则 SDK 将跟踪所有[浏览器链接](/aspnet/core/client-side/using-browserlink)请求。 | boolean<br/>false |
| appId | appId 用于在客户端上发生的 AJAX 依赖项与服务器端请求之间进行关联。 启用信标 API 后，无法自动使用 appId，但可以在配置中手动设置它。 |string<br/> NULL |
| enable&#8203;CorsCorrelation | 如果为 true，则 SDK 会将两个标头（“Request-Id”和“Request-Context”）添加到所有 CORS 请求，以将传出的 AJAX 依赖项关联到服务器端上的对应请求。 | boolean<br/>false |
| namePrefix | 一个可选值，用作 localStorage 和 Cookie 名称的名称后缀。 | 字符串<br/>undefined |
| enable&#8203;AutoRoute&#8203;Tracking | 自动跟踪单页应用程序 (SPA) 中的路由更改。 如果为 true，则每次更改路由都会将一个新的页面视图发送到 Application Insights。 哈希路由更改 (`example.com/foo#bar`) 也会记录为新的页面视图。| boolean<br/>false |
| enableRequest&#8203;HeaderTracking | 如果为 true，则跟踪 AJAX & Fetch 请求标头。 | boolean<br/> false |
| enableResponse&#8203;HeaderTracking | 如果为 true，则跟踪 AJAX & Fetch 请求的响应头。 | boolean<br/> false |
| distributedTracingMode | 设置分布式跟踪模式。 如果设置了 AI_AND_W3C 模式或 W3C 模式，则将生成 W3C 跟踪上下文标头 (traceparent/tracestate)，并将其包含在所有传出请求中。 提供 AI_AND_W3C 是为了与任何旧版 Application Insights 检测服务向后兼容。 请参阅[此处](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)的示例。| `DistributedTracingModes`或<br/>numeric<br/>（自 v2.6.0 起）`DistributedTracingModes.AI_AND_W3C`<br />（v 2.5.11 或更早版本）`DistributedTracingModes.AI` |
| enable&#8203;AjaxErrorStatusText | 如果为 true，则在 AJAX 请求失败时包含依赖关系事件中的响应错误数据文本。 | boolean<br/> false |
| enable&#8203;AjaxPerfTracking |用于启用查找并包含报告的 `ajax`（XHR 和 fetch）报告的指标中其他浏览器 window.performance 计时的标记。 | boolean<br/> false |
| maxAjaxPerf&#8203;LookupAttempts | 查找 window.performance 计时的最大次数，此值为必需，因为并非所有浏览器在报告 XHR 请求完成之前都会填充 window.performance，而对于 fetch 请求，将在请求完成之后添加该值。| numeric<br/> 3 |
| ajaxPerfLookupDelay | 重新尝试为 `ajax` 请求查找 windows.performance 计时时要等待的时间，时间以毫秒计并直接传递给 setTimeout()。 | numeric<br/> 25 毫秒 |
| enableUnhandled&#8203;PromiseRejection&#8203;Tracking | 如果为 true，则将自动收集未处理的拒绝承诺并报告为 JavaScript 错误。 如果 disableExceptionTracking 为 true（不跟踪异常），则将忽略配置值且不会报告未处理的拒绝承诺。 | boolean<br/> false |
| disable&#8203;InstrumentationKey&#8203;Validation | 如果为 true，则跳过检测密钥验证检查。 | boolean<br/>false |
| enablePerfMgr | 启用时（为 true 时）将为已检测的代码创建本地 perfEvents 以发出 perfEvents（通过 doPerf() 帮助程序）。 这可用于根据使用情况识别 SDK 中的性能问题，或者选择性地在自己的已检测代码中识别性能问题。 [基本文档中提供了更多详细信息](https://github.com/microsoft/ApplicationInsights-JS/blob/master/docs/PerformanceMonitoring.md)。 自 v2.5.7 起 | boolean<br/>false |
| perfEvtsSendAll | 如果已启用 enablePerfMgr 且 [IPerfManager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfManager.ts) 触发了 [INotificationManager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/INotificationManager.ts).perfEvent()，此标志会确定是为所有事件触发事件并发送到所有侦听器（为 true 时），还是仅针对父级事件触发事件（为 false 时，&lt;默认值&gt;）。<br />父级 [IPerfEvent](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfEvent.ts) 事件在创建时没有其他 IPerfEvent 仍在运行，且其“父级”属性不为 NULL 或未定义状态。 自 v2.5.7 起 |  boolean<br />false |
| idLength | 标识用于生成新的随机会话和用户 ID 值的默认长度。 默认值为 22，以前的默认值为 5（v 2.5.8 或更低版本），如果需要保留以前的最大长度，则应将此值设置为 5。 |  numeric<br />22 |

## <a name="cookie-handling"></a>Cookie 处理

从版本 2.6.0 开始，Cookie 管理功能现在可直接在实例中使用，并且可以在初始化后禁用和重新启用。

如果在初始化期间通过 `disableCookiesUsage` 或 `cookieCfg.enabled` 配置禁用了该功能，现在可以使用 [ICookieMgr](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts) `setEnabled` 函数重新启用。

基于实例的 Cookie 管理还替代了 `disableCookies()`、`setCookie(...)`、`getCookie(...)` 和 `deleteCookie(...)` 之前的 CoreUtils 全局函数。 另外，要从版本 2.6.0 中引入的摇树增强功能中受益，应不再使用全局函数。

### <a name="icookiemgrconfig"></a>ICookieMgrConfig

基于实例的 Cookie 管理的 Cookie 配置，添加在版本 2.6.0 中。

| 名称 | 说明 | 类型和默认值 |
|------|-------------|------------------|
| enabled | 一个布尔，指示当前实例是否让 SDK 使用 Cookie。 如果为 false，则此配置所初始化的 SDK 实例将不会存储或读取 Cookie 中的任何数据 | boolean<br/> 是 |
| 域 | 自定义 Cookie 域。 若要跨子域共享 Application Insights Cookie，此字段会有帮助。 如果未提供，则使用根 `cookieDomain` 值中的值。 | string<br/>NULL |
| path | 指定用于 Cookie 的路径，如果未提供，将使用根 `cookiePath` 值中的任意值。 | 字符串 <br/> / |
| getCookie | 用于提取命名 Cookie 值的函数，如果未提供，将使用内部 Cookie 解析/缓存。 | `(name: string) => string` <br/> null |
| setCookie | 用于设置具有指定值的命名 Cookie 的函数，仅在添加或更新 Cookie 时调用。 | `(name: string, value: string) => void` <br/> null |
| delCookie | 用于删除具有指定值的命名 Cookie 的函数，与 setCookie 分离，从而无需分析值以确定是否添加或删除 Cookie。 如果未提供，则使用内部 cookie 解析/缓存。 | `(name: string, value: string) => void` <br/> null |

### <a name="simplified-usage-of-new-instance-cookie-manager"></a>简化了新实例 Cookie 管理器的使用

- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).setEnabled(true/false);
- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).set("MyCookie", "the%20encoded%20value");
- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).get("MyCookie");
- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).del("MyCookie");

## <a name="enable-time-on-page-tracking"></a>启用“页面访问时间”跟踪

通过设置 `autoTrackPageVisitTime: true`，跟踪用户在每个页面上花费的时间。 在每个新 PageView 上，用户在上一页花费的时间将作为名为 `PageVisitTime` 的[自定义指标](../essentials/metrics-custom-overview.md)发送。 此自定义指标可在[指标资源管理器](../essentials/metrics-getting-started.md)中作为“基于日志的指标”查看。

## <a name="enable-correlation"></a>启用关联

关联功能会生成并发送启用分布式跟踪的数据，并为[应用程序映射](../app/app-map.md)、[端到端事务查看](../app/app-map.md#go-to-details)和其他诊断工具提供支持。

下面的示例展示了启用关联功能所需的所有可能的配置，并且下方提供了特定于方案的说明：

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

如果与客户端通信的任何第三方服务器都不接受 `Request-Id` 和 `Request-Context` 标头，并且你无法更新其配置，则需要通过 `correlationHeaderExcludeDomains` 配置属性将其放入排除列表中。 此属性支持通配符。

服务器端需要能够接受与所示的这些标头的连接。 根据服务器端的 `Access-Control-Allow-Headers` 配置，通常你需要通过手动添加 `Request-Id` 和 `Request-Context` 来扩展服务器端列表。

Access-Control-Allow-Headers：`Request-Id`、`Request-Context`、`<your header>`

> [!NOTE]
> 如果使用的是 2020 年发布的 OpenTelemtry 或 Application Insights SAK 或更高版本，我们建议使用 [WC3 TraceContext](https://www.w3.org/TR/trace-context/)。 请参阅[此处](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)的配置指南。

## <a name="single-page-applications"></a>单页应用程序

默认情况下，此 SDK **不会** 处理单页应用程序中发生的基于状态的路由更改。 若要为单页应用程序启用自动路由更改跟踪，可将 `enableAutoRouteTracking: true` 添加到设置配置。

目前，我们提供了一个单独的 [React 插件](javascript-react-plugin.md)（可以使用此 SDK 对其进行初始化）。 该插件也能为你实现路由更改跟踪，并可收集其他特定于 React 的遥测数据。
> [!NOTE]
> 仅当你不使用 React 插件时，才使用 `enableAutoRouteTracking: true`。 当路由更改时，这两种方法都能发送新的 PageView。 如果这两种方法均已启用，则可能会发送重复的 PageView。

## <a name="extensions"></a>扩展

| 扩展 |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md)|
| [单击“分析自动收集”](javascript-click-analytics-plugin.md)|

## <a name="explore-browserclient-side-data"></a>浏览浏览器/客户端数据

可以转到“指标”并添加你感兴趣的各个指标，来查看浏览器/客户端数据：

![Application Insights“指标”页的屏幕截图，其中显示了 Web 应用程序指标数据的图形显示。](./media/javascript/page-view-load-time.png)

还可以通过门户中的“浏览器”体验查看 JavaScript SDK 中的数据。

选择“浏览器”，然后选择“失败”或“性能”。

![Application Insights“浏览器”页的屏幕截图，其中显示了如何将浏览器故障或浏览器性能添加到可以为 Web 应用程序查看的指标中。](./media/javascript/browser.png)

### <a name="performance"></a>性能

![Application Insights“性能”页的屏幕截图，其中显示了 Web 应用程序操作指标的图形显示。](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>依赖项

![Application Insights“性能”页的屏幕截图，其中显示了 Web 应用程序依赖项指标的图形显示。](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>分析

若要查询 JavaScript SDK 收集的遥测数据，请选择“在日志(分析)中查看”按钮。 如果添加 `client_Type == "Browser"` 的 `where` 语句，则只会看到来自 JavaScript SDK 的数据，而其他 SDK 收集的任何服务器端遥测数据将被排除。
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>源映射支持

可以在 Azure 门户中取消缩小异常遥测的已缩小调用堆栈。 “异常详细信息”面板中的所有现有集成将适用于最近取消缩小的调用堆栈。

#### <a name="link-to-blob-storage-account"></a>链接到 Blob 存储帐户

可以将 Application Insights 资源链接到自己的 Azure Blob 存储容器，以便自动取消缩小调用堆栈。 若要开始，请参阅[自动源映射支持](./source-map-support.md)。

### <a name="drag-and-drop"></a>拖放

1. 请在 Azure 门户中选择一个异常遥测项，以查看其“端到端事务详细信息”
2. 确定哪些源映射对应于此调用堆栈。 源映射必须与堆栈帧的源文件相匹配，但后缀为 `.map`
3. 将源映射拖放到 Azure 门户 中的调用堆栈上 ![该动画图像显示了如何将源映射文件从生成文件夹拖放到 Azure 门户的“调用堆栈”窗口中。](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web 基本版

若要获得精简的体验，可以改为安装基本版 Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
此版本附带最少数量的功能，你可以根据自己的需求对其进行构建。 例如，它不执行自动收集（未捕获的异常、AJAX 等等）。 此版本不包含用于发送某些遥测类型的 API（例如 `trackTrace`、`trackException` 等），因此你需要提供自己的包装器。 提供的唯一 API 是 `track`。 [此处](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html)提供了一个示例。

## <a name="examples"></a>示例

如需查看可运行的示例，请参阅 [Application Insights JavaScript SDK 示例](https://github.com/Azure-Samples?q=applicationinsights-js-demo)。

## <a name="upgrading-from-the-old-version-of-application-insights"></a>从旧版 Application Insights 升级

SDK V2 版本中的重大更改：
- 为了让用户生成更好的 API 签名，某些 API 调用（例如 trackPageView 和 trackException）已更新。 不支持在 Internet Explorer 8 和早期版本的浏览器中运行。
- 由于数据架构更新，遥测信封的字段名称和结构已更改。
- 已将 `context.operation` 转移到 `context.telemetryTrace`。 此外还更改了一些字段 (`operation.id` --> `telemetryTrace.traceID`)。
  - 若要手动刷新当前页面视图 ID（例如，在 SPA 应用中这样做），请使用 `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()`。
    > [!NOTE]
    > 为了使跟踪 ID 独一无二，以前使用 `Util.newId()`，现在使用 `Util.generateW3CId()`。 二者最终都会成为操作 ID。

如果你正在使用最新的 Application Insights PRODUCTION SDK (1.0.20)，并想要查看新 SDK 是否可在运行时中正常工作，请根据当前的 SDK 加载方案更新 URL。

- 通过 CDN 方案下载：更新当前用于指向以下 URL 的代码片段：
   ```
   "https://js.monitor.azure.com/scripts/b/ai.2.min.js"
   ```

- npm 方案：调用 `downloadAndSetup` 以从 CDN 下载完整的 ApplicationInsights 脚本，并使用检测密钥将其初始化：

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://js.monitor.azure.com/scripts/b/ai.2.min.jss"
     });
   ```

在内部环境中进行测试，以验证是否可按预期方式监视遥测。 如果一切正常，请相应地将 API 签名更新为 SDK V2 版本，并部署到生产环境中。

## <a name="sdk-performanceoverhead"></a>SDK 性能/开销

经过 gzip 压缩后只有 36 KB，只需大约 15 毫秒即可完成初始化，Application Insights 在网站中的加载时间可忽略不计。 使用代码片段时，很快就能加载极少量的库组件。 同时，整个脚本将在后台下载。

从 CDN 下载脚本时，页面的所有跟踪将会排队。 在下载的脚本以异步方式完成初始化后，将会跟踪已排队的所有事件。 因此，在页面的整个生命周期内，你都不会丢失任何遥测数据。 此设置过程可为页面提供一个无缝分析系统，而用户察觉不到该系统。

> 摘要：
> - ![npm 版本](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![gzip 压缩大小](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - 总初始化时间为 **15 毫秒**
> - 在页面的整个生命周期内都 **不会** 失去跟踪

## <a name="browser-support"></a>浏览器支持

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome 最新版 ✔ |  Firefox 最新版 ✔ | IE 9 + 和 Microsoft Edge ✔<br>IE 8- 兼容 | Opera 最新版 ✔ | Safari 最新版 ✔ |

## <a name="es3ie8-compatibility"></a>ES3/IE8 兼容性

作为 SDK，很多用户无法控制其客户所使用的浏览器。 因此，我们需要确保此 SDK 继续“工作”，并且在由旧版浏览器加载时不会中断 JS 执行。 虽然可以选择不支持 IE8 和旧版生成 (ES3) 浏览器，但有很多大型客户/用户会继续要求页面“工作”，并且我们发现，他们可能或无法控制其最终用户选择使用的浏览器。

这并不意味着我们只支持最少的一组常见功能，只是我们需要维护 ES3 代码兼容性，并且需要采用一种既不会中断 ES3 JavaScript 分析又可作为可选功能进行添加的方式来添加新功能。

[有关 IE8 支持的完整详细信息，请参阅 GitHub](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>开源 SDK

Application Insights JavaScript SDK 是开源的，用户可查看其源代码；若要对该项目做贡献，请访问[官方 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-JS)。 

有关最新的更新和 bug 修复，请[参阅发行说明](./release-notes.md)。

## <a name="next-steps"></a><a name="next"></a> 后续步骤
* [跟踪使用情况](usage-overview.md)
* [自定义事件和指标](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
* [对 SDK 加载失败进行故障排除](javascript-sdk-load-failure.md)
