---
title: 在 Azure 应用服务中为 .NET 应用启用 Snapshot Debugger | Microsoft Docs
description: 在 Azure 应用服务中为 .NET 应用启用快照调试器
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 5a637a6f355be32f82878a52a30c77d020190651
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211611"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>在 Azure 应用服务中为 .NET 应用启用快照调试器

Snapshot Debugger 当前支持在 Windows 服务计划的 Azure App Service 上运行的 ASP.NET 和 ASP.NET Core 应用。

建议你在使用快照调试程序时，在 "基本" 服务层或更高版本上运行应用程序。

对于大多数应用程序，“免费”和“共享”服务层没有足够的内存或磁盘空间来保存快照。

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> 启用 Snapshot Debugger
若要为应用启用快照调试器，请遵循下面的说明。

如果你在运行另一种类型的 Azure 服务，则下面提供了用于在其他受支持平台上启用 Snapshot Debugger 的说明：
* [Azure Function](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure 云服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机和虚拟机规模集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [本地虚拟机或物理计算机](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> 如果你使用的是 .NET Core 的预览版本，或你的应用程序通过依赖程序集直接或间接引用 Application Insights SDK，请按照为 [其他环境启用 Snapshot Debugger 的](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) 说明，将 [microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用程序，然后完成下面的说明。 
>
> Application Insights Snapshot Debugger 的无代码置备安装遵循 .NET Core 支持策略。
> 有关支持的运行时的详细信息，请参阅 [.Net Core 支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。

预安装 Snapshot Debugger 作为应用程序服务运行时的一部分，但需启用它才能获得适用于应用服务应用的快照。

部署应用后，请按照以下步骤启用 Snapshot Debugger：

1. 导航到应用服务的 Azure 控制面板。
2. 转到“设置”>“Application Insights”页面。

   ![在应用服务门户上启用 App Insights](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 按页面中的说明创建新资源，或者选择现有 App Insights 资源，以便监视应用。 另外，请确保快照调试器的两个开关都为“开”  。

   ![添加 App Insights 站点扩展][Enablement UI]

4. 现已使用应用服务应用设置启用了快照调试器。

    ![快照调试器的应用设置][snapshot-debugger-app-setting]

## <a name="enable-snapshot-debugger-for-other-clouds"></a>为其他云启用 Snapshot Debugger

目前，需要终结点修改的唯一区域是 [Azure 政府](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) 和 [azure 中国](https://docs.microsoft.com/azure/china/resources-developer-guide) 通过 Application Insights 的连接字符串。

|连接字符串属性    | 美国政府云 | 中国云 |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

有关其他连接替代的详细信息，请参阅 [Application Insights 文档](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net#connection-string-with-explicit-endpoint-overrides)。

## <a name="disable-snapshot-debugger"></a>禁用快照调试器

执行与 **启用快照调试器** 相同的步骤，但将快照调试器的两个开关都切换到“关”  。

建议对所有应用启用 Snapshot Debugger，以简化应用程序异常的诊断。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板

对于 Azure 应用服务，可在 Azure 资源管理器模板中设置应用设置，以启用 Snapshot Debugger 和 Profiler，请参阅下面的模板代码片段：

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>后续步骤

- 为应用程序生成可触发异常的流量。 然后等待 10 到 15 分钟，这样快照就会发送到 Application Insights 实例。
- 请参见 Azure 门户中的[快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)。
- 排查 Snapshot Debugger 问题时如需帮助，请参阅 [ Snapshot Debugger 故障排除](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

