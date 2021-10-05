---
title: 监视 Azure 应用服务性能 - Java | Microsoft Docs
description: 使用 Java 为 Azure 应用服务进行应用程序性能监视。 对加载和响应时间、依赖项信息绘制图表，并针对性能设置警报。
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: d673524b30eae13e24758aff23a68bd2b38c2e3e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128698896"
---
# <a name="application-monitoring-for-azure-app-service-and-java"></a>Azure 应用服务的应用程序监视和 Java

无需对代码进行任何修改，就能监视在 [Azure 应用服务](../../app-service/index.yml)上运行的基于 Java 的 Web 应用程序。 本文逐步讲解如何启用 Azure Monitor Application Insights 监视，并提供有关如何自动完成大规模部署的初步指导。

## <a name="enable-application-insights"></a>启用 Application Insights

为在 Azure 应用服务上运行的 Java 应用程序启用应用程序监视的建议方法是使用 Azure 门户。 在 Azure 门户中启用应用程序监视会自动使用 Application Insights 来检测应用程序。  

### <a name="auto-instrumentation-through-azure-portal"></a>通过 Azure 门户进行自动检测

此方法不要求更改代码或进行高级配置，因此它是开始监视 Azure 应用服务的最简单方法。 可以应用其他配置，然后根据具体的方案评估是否需要通过[手动检测](./java-2x-get-started.md?tabs=maven)进行更高级的监视。

### <a name="enable-backend-monitoring"></a>启用后端监视

只需单击一下，就可以为 Azure 应用服务中运行的 Java 应用启用监视，而无需更改代码。 适用于 Java 的 Application Insights 与下列项集成：Linux 上的应用服务（包括基于代码的容器和自定义容器）以及 Windows 上的应用服务（适用于基于代码的应用）。 请务必了解系统将如何监视你的应用程序。 集成会添加 [Application Insights Java 3.x](./java-in-process-agent.md)，系统会自动收集遥测数据。

1. 在应用服务的 Azure 控制面板中，选择“Application Insights”，然后选择“启用”。 

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="“Application Insights”选项卡的屏幕截图，其中选择了“启用”。"::: 

2. 选择创建新资源，或为此应用程序选择现有 Application Insights 资源。

    > [!NOTE]
    > 选择“确定”来创建新资源时，系统将提示你“应用监视设置” 。 选择“继续”会将新的 Application Insights 资源链接到你的应用服务，这样做还会 **触发应用服务的重新启动**。 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="“更改资源”下拉菜单的屏幕截图。"::: 

3. 此步骤不是必需的。 指定要使用的资源后，可以配置 Java 代理。 如果你不配置 Java 代理，将会应用默认配置。 

    完整的[配置集](./java-standalone-config.md)可供使用，你只需粘贴有效的 [json 文件](./java-standalone-config.md#an-example)即可。 排除以预览版提供的连接字符串和任何配置 - 在目前以预览版提供的项推出正式版后，你可以添加这些项。

    通过 Azure 门户修改配置后，APPLICATIONINSIGHTS_CONFIGURATION_FILE 环境变量将自动填充并显示在“应用服务设置”面板中。 此变量将包含你在 Java 应用的 Azure 门户配置文本框中粘贴的完整 json 内容。 

    :::image type="content"source="./media/azure-web-apps-java/create-app-service-ai.png" alt-text="“检测应用程序”的屏幕截图。"::: 
    

## <a name="enable-client-side-monitoring"></a>启用客户端监视

若要针对 Java 应用程序启用客户端监视，需[手动将客户端 JavaScript SDK 添加到应用程序](./javascript.md)。

## <a name="automate-monitoring"></a>自动监视

### <a name="application-settings"></a>应用程序设置

若要为 Application Insights 启用遥测数据收集，只需设置以下应用程序设置：

|应用设置名称 |  定义 | Value |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 控制运行时监视 | `~2` (Windows) 或 `~3` (Linux) |
|XDT_MicrosoftApplicationInsights_Java |  用于控制是否包含 Java 代理的标志 | 0 或 1 仅适用于 Windows
|APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL | 仅当需要调试 Application Insights 与应用服务的集成时才使用 | debug

:::image type="content"source="./media/azure-web-apps-java/application-settings-java.png" alt-text="应用服务应用程序设置的屏幕截图，其中显示了可用的 Application Insights 设置。"::: 

> [!NOTE]
> Profiler 和快照调试器不适用于 Java 应用程序

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]

## <a name="troubleshooting"></a>故障排除

下面是适用于在 Azure 应用服务上运行的基于 Java 的应用程序的分步故障排除指南。

1. 检查 `ApplicationInsightsAgent_EXTENSION_VERSION` 应用设置是否设置为“~2”（在 Windows 上）或“~3”（在 Linux 上）
1. 检查日志文件以确定代理是否已成功启动：浏览到 `https://yoursitename.scm.azurewebsites.net/ ，通过 SSH 切换到根目录，日志文件位于 LogFiles/ApplicationInsights 下。 
  
    :::image type="content"source="./media/azure-web-apps-java/app-insights-java-status.png" alt-text="结果页上方的链接的屏幕截图。"::: 

1. 为 Java 应用启用应用程序监视后，可以通过查看实时指标来验证代理是否正常工作 - 即使是在将应用部署到应用服务之前，也会看到来自环境的一些请求。 请记住，只有在应用已部署并正在运行时，才能使用完整的遥测数据集。 
1. 如果未看到任何错误并且没有遥测数据，请将 APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL 环境变量设置为“debug”
1. 有时，在应用服务中没有最新版本的 Application Insights Java 代理可用 - 最新版本需要在几个月后才会推出到所有区域。 如果需要使用最新版本的 Java 代理来监视应用服务中的应用，可以手动上传代理： 
    * 将 Java 代理 jar 文件上传到应用服务
        * 获取最新版本的 [Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli)
        * 获取最新版本的 [Application Insights Java 代理](./java-in-process-agent.md)
        * 将 Java 代理部署到应用服务 - 用于部署 Java 代理 jar 的示例命令：`az webapp deploy --src-path applicationinsights-agent-{VERSION_NUMBER}.jar --target-path java/applicationinsights-agent-{VERSION_NUMBER}.jar --type static --resource-group {YOUR_RESOURCE_GROUP} --name {YOUR_APP_SVC_NAME}`，或使用[此指南](../../app-service/quickstart-java.md?tabs=javase&pivots=platform-linux#configure-the-maven-plugin)通过 Maven 插件进行部署
    * 上传代理 jar 文件后，转到应用服务配置，然后添加新环境变量 JAVA_OPTS 并将其值设置为 `-javaagent:D:/home/{PATH_TO_THE_AGENT_JAR}/applicationinsights-agent-{VERSION_NUMBER}.jar`
    * 通过 Application Insights 选项卡禁用 Application Insights
    * 重启应用

    > [!NOTE]
    > 如果设置 JAVA_OPTS 环境变量，则必须在门户中禁用 Application Insights。 或者，如果你想要从门户启用 Application Insights，请确保不要在应用服务配置设置中设置 JAVA_OPTS 变量。 


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