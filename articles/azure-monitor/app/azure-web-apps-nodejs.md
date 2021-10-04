---
title: 使用 Node.js 监视 Azure 应用服务性能 | Microsoft Docs
description: 使用 Node.js 为 Azure 应用服务进行应用程序性能监视。 对加载和响应时间、依赖项信息绘制图表，并针对性能设置警报。
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 5f80666d4a184da40979a38f6d7f17782291c9e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128698886"
---
# <a name="application-monitoring-for-azure-app-service-and-nodejs"></a>Azure 应用服务的应用程序监视和 Node.js

现在，可以比过去更轻松地针对 [Azure 应用服务](../../app-service/index.yml)中运行的基于 Node.js 的 Web 应用程序启用监视。 以前需要手动检测某个应用，而现在应用服务映像中默认会内置最新的扩展/代理。 本文将逐步讲解如何启用 Azure Monitor Application Insights 监视，并提供有关如何自动完成大规模部署过程的初步指导。

> [!NOTE]
> 如果同时检测到了基于代理的监视和基于手动 SDK 的检测，则只会采用手动检测设置， 这是为了防止发送重复数据。 有关详细信息，请查看下面的[故障排除部分](#troubleshooting)。

## <a name="enable-agent-based-monitoring"></a>启用基于代理的监视

只需几个简单的步骤，就能监视在 Azure 应用服务中运行的 Node.js 应用，无需进行任何代码更改。 适用于 Node.js 应用程序的 Application Insights 与下列项集成：Linux 上的应用服务（包括基于代码的容器和自定义容器）以及 Windows 上的应用服务（适用于基于代码的应用）。 集成处于公开预览阶段。 集成添加了 Node.js SDK（正式版）。 

1. 在应用服务的 Azure 控制面板中，选择“Application Insights”，然后选择“启用”。 

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="“Application Insights”选项卡的屏幕截图，其中选择了“启用”。"::: 

2. 选择创建新资源，或为此应用程序选择现有 Application Insights 资源。

     > [!NOTE]
     > 当单击“确定”来创建新资源时，将提示你 **应用监视设置**。 选择“继续”会将新的 Application Insights 资源链接到你的应用服务，这样做还会 **触发应用服务的重新启动**。 
    
    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="“更改资源”下拉菜单的屏幕截图。"::: 
    
3. 指定要使用的资源后，就可以开始了。 


    :::image type="content"source="./media/azure-web-apps-nodejs/app-service-node.png" alt-text="“检测应用程序”的屏幕截图。"::: 


## <a name="enable-client-side-monitoring"></a>启用客户端监视

若要针对 Node.js 应用程序启用客户端监视，需[手动将客户端 JavaScript SDK 添加到应用程序](./javascript.md)。

## <a name="automate-monitoring"></a>自动监视

若要在 Application Insights 中启用遥测数据收集，只需设置“应用程序设置”：

:::image type="content"source="./media/azure-web-apps-nodejs/application-settings-nodejs.png" alt-text="应用服务应用程序设置的屏幕截图，其中的 Application Insights 设置可用。"::: 


### <a name="application-settings-definitions"></a>应用程序设置定义

|应用设置名称 |  定义 | Value |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 用于控制运行时监视的主扩展。 | 在 Windows 中为 `~2`，在 Linux 中为 `~3` |
|XDT_MicrosoftApplicationInsights_NodeJS |  一个标志，用于控制是否包含 node.js 代理。 | 0 或 1 仅适用于 Windows。 |


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="troubleshooting"></a>故障排除

下面是我们针对 Azure 应用服务中运行的基于 Node.js 的应用程序的基于扩展/代理的监视提供的分步故障排除指南。

# <a name="windows"></a>[Windows](#tab/windows)

1. 检查 `ApplicationInsightsAgent_EXTENSION_VERSION` 应用设置是否设置为值“~2”。
2. 浏览到 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`。  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="结果页上方的链接的屏幕截图。"border ="false"::: 

    - 确认 `Application Insights Extension Status` 为 `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.` 

         如果未在运行，请遵照[启用 Application Insights 监视的说明](#enable-agent-based-monitoring)。

    - 导航到 D:\local\Temp\status.json 并打开 status.json。

    确认 `SDKPresent` 设置为“false”，`AgentInitializedSuccessfully` 设置为“true”，`IKey` 设置为有效的 iKey。

    下面是一个 JSON 文件示例：

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    如果 `SDKPresent` 为 true，则表明扩展检测到 SDK 的某一方面已在应用程序中存在，并将退出。


# <a name="linux"></a>[Linux](#tab/linux)

1. 检查 `ApplicationInsightsAgent_EXTENSION_VERSION` 应用设置是否设置为值“~3”。
2. 导航到 /var/log/applicationinsights/ 并打开 status.json。

    确认 `SDKPresent` 设置为“false”，`AgentInitializedSuccessfully` 设置为“true”，`IKey` 设置为有效的 iKey。

    下面是一个 JSON 文件示例：

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    如果 `SDKPresent` 为 true，则表明扩展检测到 SDK 的某一方面已在应用程序中存在，并将退出。
---

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

## <a name="release-notes"></a>发行说明

有关最新的更新和 bug 修复，请[参阅发行说明](web-app-extension-release-notes.md)。

## <a name="next-steps"></a>后续步骤
* [使用 Application Insights 监视 Azure Functions](monitor-functions.md)。
* [将 Azure 诊断配置为](../agents/diagnostics-extension-to-application-insights.md)向 Application Insights 发送数据。
* [监视服务运行状况指标](../data-platform.md)以确保服务可用且做出快速响应。
* [接收警报通知](../alerts/alerts-overview.md) 。
* 若要从访问网页的浏览器获取客户端遥测数据，请使用[适用于 JavaScript 应用和网页的 Application Insights](javascript.md)。
* [设置可用性 Web 测试](monitor-web-app-availability.md)，以便在站点关闭时发出警报。