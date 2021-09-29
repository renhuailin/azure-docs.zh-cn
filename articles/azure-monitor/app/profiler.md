---
title: 使用 Application Insights 探查实时 Azure 应用服务应用 | Microsoft Docs
description: 使用 Application Insights Profiler 探查 Azure 应用服务上的实时应用。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.openlocfilehash: e80ce3a4e96720bc3149dd9d8083a1f8f0943dbe
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815756"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>使用 Application Insights 探查实时 Azure 应用服务应用

可以在使用基本服务层级或更高层级的 Azure 应用服务上运行的 ASP.NET 和 ASP.NET Core 应用中运行 Profiler。 目前只有通过[此方法](profiler-aspnetcore-linux.md)才能在 Linux 上启用 Profiler。

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> 为应用启用 Profiler
若要为应用启用 Profiler，请遵循下面的说明。 如果你在运行另一种类型的 Azure 服务，则下面提供了用于在其他受支持平台上启用 Profiler 的说明：
* [云服务](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Service Fabric 应用程序](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [虚拟机](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler 是作为应用服务运行时的一部分预先安装的。 下面的步骤将说明如何为应用服务启用它。 即使在生成时已在应用程序中包含了 App Insights SDK，也应该执行这些步骤。

> [!NOTE]
> Application Insights Profiler 的无代码安装遵循 .NET Core 支持策略。
> 若要详细了解支持的运行时，请参阅 [.Net Core 支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。

1. 导航到应用服务的 Azure 控制面板。
1. 为应用服务启用“Always On”设置。 可以在“设置”、“配置”页（见下一步中的屏幕截图）下找到此设置，然后选择“常规设置”选项卡。
1. 导航到“设置”>“Application Insights”页面。

   ![在应用服务门户上启用 App Insights](./media/profiler/AppInsights-AppServices.png)

1. 按窗格中的说明创建新资源，或者选择现有的 App Insights 资源，以便监视应用。 此外，请确保 Profiler 已启用。 如果 Application Insights 资源位于与应用服务不同的订阅中，则无法使用此页来配置 Application Insights。 你仍然可以通过手动创建所需的应用设置来手动执行此操作。 [下一部分包含有关手动启用 Profiler 的说明。](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![添加 App Insights 站点扩展][Enablement UI]

1. 现已使用应用服务应用设置启用了 Profiler。

    ![Profiler 的应用设置][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>手动或使用 Azure 资源管理器启用 Profiler
可以通过为 Azure 应用服务创建应用设置来启用 Application Insights Profiler。 包含如上所示选项的页面将为你创建这些应用设置。 但是，也可以使用模板或其他方式自动创建这些设置。 如果 Application Insights 资源与 Azure 应用服务位于不同的订阅中，这些设置也将起作用。
下面是启用 Profiler 所需的设置：

|应用设置    | 值    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 资源的 iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


可以使用 [Azure 资源管理器模板](./azure-web-apps-net-core.md#app-service-application-settings-with-azure-resource-manager)、[Azure PowerShell](/powershell/module/az.websites/set-azwebapp)、[Azure CLI](/cli/azure/webapp/config/appsettings) 设置这些值。

## <a name="enable-profiler-for-other-clouds"></a>为其他云启用 Profiler

目前唯一需要修改终结点的区域是 [Azure 政府](../../azure-government/compare-azure-government-global-azure.md#application-insights)和 [Azure 中国](/azure/china/resources-developer-guide)。

|应用设置    | 美国政府云 | 中国云 |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>禁用 Profiler

若要为单个应用实例停止或重启 Profiler，请选择左侧边栏上的“WebJobs”，然后停止名为“`ApplicationInsightsProfiler3`”的 Web 作业。

  ![禁用针对 Web 作业的 Profiler][disable-profiler-webjob]

建议在所有应用上都启用 Profiler，以便尽早发现任何性能问题。

使用 WebDeploy 将更改部署到 Web 应用程序时可能会删除 Profiler 的文件。 可以通过在部署期间排除 App_Data 文件夹的删除来防止发生这种删除。 


## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中使用 Application Insights](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png