---
title: Application Insights 使用分析 | Azure Monitor
description: 了解用户，以及他们将应用用于哪些目的。
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 67739c94afeb841450eb465fe5cd5259093e2e78
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729321"
---
# <a name="usage-analysis-with-application-insights"></a>Application Insights 使用分析

Web 或移动应用有哪些最热门的功能？ 用户是否使用应用实现了其目标？ 他们是否中途放弃应用，后来又回头使用了吗？  [Application Insights](./app-insights-overview.md) 可帮助你有效地深入分析人们如何使用应用。 每次更新应用时，都可以评估它在用户那里的运行状况。 了解这些信息后，可以针对下一个开发周期做出数据驱动的决策。


## <a name="send-telemetry-from-your-app"></a>从应用发送遥测数据

通过在应用服务器代码和网页中安装 Application Insights 来获得最佳体验。 应用的客户端和服务器组件将遥测发送回 Azure 门户进行分析。

1. **服务器代码：** 为 [ASP.NET](./asp-net.md)、[Azure](./app-insights-overview.md)、[Java](./java-in-process-agent.md)、[Node.js](./nodejs.md) 或 [其他](./platforms.md)应用安装适当的模块。

    * 不想安装服务器代码？只需[创建 Azure Application Insights 资源](./create-new-resource.md)。

2. **网页代码**：将以下脚本添加到网页的结束标记 ``</head>`` 之前。 将检测密钥替换为 Application Insights 资源的相应值：
    
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
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    若要了解更多用于监视网站的高级配置，请查看 [JavaScript SDK 参考](./javascript.md)一文。

3. **移动应用代码：** 通过 [按照此指南操作](../app/mobile-center-quickstart.md)，使用 App Center SDK 收集应用中的事件，然后将这些事件的副本发送到 Application Insights 进行分析。

4. **获取遥测：** 在调试模式下运行项目几分钟，并在“Application Insights”中的“概述”窗格中查找结果。

    发布应用以监视应用性能，并查看用户使用该应用在执行哪些操作。

## <a name="explore-usage-demographics-and-statistics"></a>浏览用户人口和统计信息
查明人们何时使用应用，他们对哪些页面最感兴趣，用户在哪里以及他们使用什么浏览器和操作系统。 

“用户和会话”报告按页面或自定义事件筛选数据，并按位置、环境和页面等属性将数据分段。 也可以添加自己的筛选器。

:::image type="content" source="./media/usage-overview/users.png" alt-text="屏幕截图显示了以条形图显示的“用户”选项卡。" lightbox="./media/usage-overview/users.png":::

右侧的见解指出了数据集中的相关模式。  

* “用户”报告统计所选时间段内访问页面的唯一用户数目。 对于 Web 应用，将使用 Cookie 统计用户。 如果某个用户使用不同的浏览器或客户端计算机访问站点或者清除了其 Cookie，该用户会被统计多次。
* “会话”报告统计访问站点的用户会话数。 会话是指某个用户的活动时段，如果有半个小时以上处于非活动状态，会话会被终止。

[有关用户、会话和事件工具的详细信息](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>保留 - 有多少个回头用户？

保留可帮助你根据特定时间桶内执行某个业务操作的用户队列，了解用户回头使用其应用的频率。 

- 了解哪些特定的功能导致某些用户比其他用户回来得更频繁 
- 基于真实的用户数据构成假设 
- 确定产品中是否存在保留问题 

:::image type="content" source="./media/usage-overview/retention.png" alt-text="屏幕截图显示了保留工作簿，其中显示了有关用户返回使用其应用的频率的信息。" lightbox="./media/usage-overview/retention.png":::

使用顶部的保留控件可以定义特定的事件和时间范围来计算保留。 中间的图表根据指定的时间范围提供总体保留百分比的视觉表示形式。 底部的图表显示给定时间段内的各个保留。 这种详细程度可让你更细致地了解用户正在做什么，以及哪些因素可能会影响用户回头。  

[有关保留工作簿的详细信息](usage-retention.md)

## <a name="custom-business-events"></a>自定义业务事件

要明确了解用户将应用用于什么目的，可以插入代码行来记录自定义事件。 这些事件可以跟踪任何活动，包括详细的用户操作（例如单击特定的按钮），以及更重要的业务活动（例如购买活动或游戏获胜）。

你还可以使用[单击分析自动收集插件](javascript-click-analytics-plugin.md)来收集自定义事件。

尽管在某些情况下页面视图可呈现有用的事件，但一般情况下这些事件并不真实。 用户无需购买产品即可打开产品页面。 

使用特定的业务事件可以绘制用户在站点中的进度图表。 了解用户对不同选项的偏好，以及他们在哪个位置放弃了应用或者遇到了问题。 了解这些信息后，可以针对开发积压工作的优先级做出明智的决策。

可以在应用的客户端记录事件：

```JavaScript
    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

也可以在服务器端进行记录：

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

可将属性值附加到这些事件，以便在门户检查事件时可以筛选或拆分事件。 也已将一组标准属性（例如匿名用户 ID）附加到每个事件，以便你跟踪单个用户的活动序列。

详细了解[自定义事件](./api-custom-events-metrics.md#trackevent)和[属性](./api-custom-events-metrics.md#properties)。

### <a name="slice-and-dice-events"></a>分解事件

在“用户”、“会话”和“事件”工具中，可按用户、事件名称和属性分解自定义事件。

:::image type="content" source="./media/usage-overview/events.png" alt-text="屏幕截图显示了按 AnalyticsItemsOperation 筛选并按 AppID 拆分的“事件”选项卡。" lightbox="./media/usage-overview/events.png":::
  
## <a name="design-the-telemetry-with-the-app"></a>在应用中设计遥测

设计应用的每项功能时，请考虑如何衡量它在用户那里取得的成功。 决定需要记录哪些事件，并一开始就在应用中针对这些事件编写跟踪调用。

## <a name="a--b-testing"></a>A | B 测试
如果不知道哪个功能变体将更有成效，可同时发布这两项，使不同用户都能访问它们。 评估每个项的成效，并移至统一的版本。

对于此技术，可将不同的属性值附加到应用的每个版本所发送的所有遥测数据。 为此，可以在活动的 TelemetryContext 中定义属性。 这些默认属性将添加到应用程序发送的每个遥测消息（并非仅自定义消息），不过标准遥测也是这样。

在 Application Insights 门户中根据属性值筛选和拆分数据，以便比较不同的版本。

为此，请[设置遥测初始值设定项](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)：

ASP.NET 应用

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

在 Global.asax.cs 等 Web 应用初始值设定项中：

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

ASP.NET Core 应用

> [!NOTE]
> 使用 `ApplicationInsights.config` 或使用 `TelemetryConfiguration.Active` 添加初始值对 ASP.NET Core 应用程序无效。 

对于 [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) 应用程序，添加新的 `TelemetryInitializer` 是通过将其添加到依赖关系注入容器来完成的，如下所示。 这是在 `Startup.cs` 类的 `ConfigureServices` 方法中完成的。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

所有新的 TelemetryClient 会自动添加指定的属性值。 单个遥测事件可以替代默认值。

## <a name="next-steps"></a>后续步骤
   - [用户、会话、事件](usage-segmentation.md)
   - [漏斗图](usage-funnels.md)
   - [保留](usage-retention.md)
   - [用户流](usage-flows.md)
   - [工作簿](../visualize/workbooks-overview.md)
