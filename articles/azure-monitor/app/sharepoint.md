---
title: 使用 Application Insights 监视 SharePoint site 站点
description: 开始使用新检测密钥监视新应用程序
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 74f0eeecb303d68af8aaa48b5c1806ceeef4d318
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "102486363"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>使用 Application Insights 监视 SharePoint site 站点

Azure Application Insights 监视应用的可用性、性能和使用情况。 下面介绍如何为 SharePoint 站点设置它。

> [!NOTE]
> 出于安全考虑，你不能在 SharePoint 新式用户体验中直接向网页添加本文所述的脚本。 作为替代方法，可使用 [SharePoint 框架 (SPFx)](/sharepoint/dev/spfx/extensions/overview-extensions) 来构建可用于在 SharePoint 站点上安装 Application Insights 的自定义扩展。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源
在 [Azure 门户](https://portal.azure.com)中，创建新的 Application Insights 资源。 选择 ASP.NET 作为应用程序类型。

![单击“属性”，选择密钥，并按 Ctrl+C](./media/sharepoint/001.png)

打开的窗口是查看关于应用的性能和用法数据的位置。 若要在下次登录 Azure 时返回到它，应在开始屏幕上查找它的磁贴。 或者单击“浏览”查找它。

## <a name="add-the-script-to-your-web-pages"></a>将脚本添加到网页

当前代码片段（下面列出）为版本“5”，该版本在代码片段中编码为 sv:"#"，[可在 GitHub 上找到当前版本](https://go.microsoft.com/fwlink/?linkid=2156318)。

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.gbl.min.js", // The SDK URL Source
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

> [!NOTE]
> SharePoint 的 URl 使用不同的模块格式“...\ai.2.gbl.min.js”（注意附加的 .gbl.），需要使用此备用模块格式才能避免脚本加载顺序所导致的问题，此类问题会导致 SDK 无法初始化并将导致遥测事件丢失。
>
> 此问题是由于在 SDK 之前加载并初始化 requireJS 而导致的。

在要跟踪的每个页面的 &lt;/head&gt; 标记之前插入脚本。如果网站有母版页，可以在那里插入脚本。 例如，在 ASP.NET MVC 项目中，将其放置在 View\Shared\_Layout.cshtml 中

该脚本包含将遥测定向到 Application Insights 资源的检测密钥。

### <a name="add-the-code-to-your-site-pages"></a>将代码添加到站点页面
#### <a name="on-the-master-page"></a>在母版页上
如果可以编辑站点的模板页，这会为站点中的每个页面提供监控。

签出母版页并使用 SharePoint Designer 或任何其他编辑器编辑它。

![显示如何使用 SharePoint Designer 或任何其他编辑器编辑母版页的屏幕截图。](./media/sharepoint/03-master.png)

将代码添加到以下项前面： </head> 标记。 

![显示要将代码添加到站点页的位置的屏幕截图。](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>或在个别页面上
要监视一组有限的页面，请将脚本分别添加到每个页面。 

插入 Web 部分并在其中嵌入代码片段。

![显示如何添加脚本来监视一组有限页面的屏幕截图。](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>查看关于应用的数据
重新部署应用。

返回到 [Azure 门户](https://portal.azure.com)中的应用程序边栏选项卡。

第一批事件会显示在“搜索”中。 

![显示可在应用中查看的新数据的屏幕截图。](./media/sharepoint/09-search.png)

如果需要更多数据，在几秒后单击“刷新”。

## <a name="capturing-user-id"></a>捕获用户 ID
标准网页代码片段不从 SharePoint 捕获用户 ID，但可在进行少量修改的情况下执行该操作。

1. 从 Application Insights 中的 Essentials 下拉列表中复制应用的检测密钥。 

    ![显示如何从 Application Insights 中的 Essentials 下拉列表中复制应用的检测的屏幕截图。](./media/sharepoint/02-props.png)

1. 在下面代码片段中将“XXXX”替换为检测密钥。 
2. 将脚本嵌入 SharePoint 应用，而不是从门户获取的代码片段。

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>后续步骤
* 用于监视站点可用性的 [Web 测试](./monitor-web-app-availability.md)。
* 适用于其他应用类型的 [Application Insights](./app-insights-overview.md)。

<!--Link references-->
