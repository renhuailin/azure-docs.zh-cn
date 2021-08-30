---
title: 诊断和解决工具
description: 了解如何使用 Azure 门户中的“诊断和解决”工具来排查应用程序在 Azure 应用服务方面的问题。
keywords: 应用服务, azure 应用服务, 诊断, 支持, web 应用, 故障排除, 自助服务
ms.topic: article
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: cf044baf233c159ff271221522f0f9faf15cc1ed
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451581"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure 应用服务诊断概述

运行 Web 应用程序时，我们希望能够对出现的各种问题做好准备，例如，出现 500 错误，或者用户反映站点已关闭。 应用服务诊断是智能的交互式体验，可帮助你排查应用的问题，且无需配置。 如果应用确实出现问题，应用服务诊断会指出问题所在，并引导你获取适当的信息，以便更轻松快速地排查和解决问题。

尽管此体验在应用过去 24 小时内出现问题时可发挥最大的作用，但是，你始终可以使用所有诊断图形进行分析。

应用服务诊断不但适用于 Windows 上的应用，也适用于 [Linux/容器](./overview.md#app-service-on-linux)、[应用服务环境](./environment/intro.md)和 [Azure Functions](../azure-functions/functions-overview.md) 上的应用。

## <a name="open-app-service-diagnostics"></a>打开应用服务诊断

若要访问应用服务诊断，请在 [Azure 门户](https://portal.azure.com)中导航到你的应用服务 Web 应用或应用服务环境。 在左侧导航栏中，单击“诊断并解决问题”。

对于 Azure Functions，请导航到函数应用，在顶部的导航栏中，单击“**平台功能**”并从“**资源管理**”部分中选择“**诊断并解决问题**”。

在应用服务诊断主页中，可以通过使用每个主页磁贴中的关键字，选择最能描述应用问题的类别。 此外，还可以在此页中找到“诊断工具”。 请参阅[诊断工具](#diagnostic-tools)。

![主页](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> 如果应用程序关闭或执行速度缓慢，则可以[收集分析跟踪](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html)，从而确定问题的根本原因。 分析为轻型，适用于生产方案。
>

## <a name="interactive-interface"></a>交互式接口

选择最适合应用程序问题的主页类别后，应用服务诊断的交互式接口——精灵可以指导用户诊断和解决应用的问题。 对于用户感兴趣的问题类别，可以使用精灵提供的磁贴快捷方式来查看其完整诊断报告。 磁贴快捷方式为用户提供了一种访问诊断指标的直接方式。

![磁贴快捷方式](./media/app-service-diagnostics/tile-shortcuts-2.png)

单击这些磁贴后，可以看到与磁贴中所述问题相关的主题列表。 这些主题会提供完整报告的重要信息片段。 您可以单击以上任一主题，进一步调查问题。 此外，还可以单击“**查看完整报告**”来浏览单个页面上的所有主题。

![主题](./media/app-service-diagnostics/application-logs-insights-3.png)

![查看完整报告](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>诊断报告

通过单击某个主题选择进一步调查问题后，便可以查看有关该主题的更多详细信息，这些详细信息通常补充有图形和 markdown。 诊断报告是一种功能强大的工具，可用于查明应用的问题。

![诊断报告](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>运行状况检查

如果不知道应用发生什么问题，或者不知道从何处着手排查问题，运行健康状况检查是一个很好的入手点。 健康状况检查将会分析应用程序，提供简明的交互式概述，指出哪个组件正常、哪个组件出错，并告知要从哪个位置调查问题。 使用其智能交互式界面可逐步完成故障排除的过程。 运行状况检查与适用于 Windows 应用的精灵体验以及适用于 Linux 应用的 web 应用关闭诊断报告集成。

### <a name="health-checkup-graphs"></a>健康状况检查图

健康状况检查有四个不同的关系图。

- **请求和错误：** 显示过去 24 小时内发出的请求数以及 HTTP 服务器错误的关系图。
- **应用程序性能：** 显示过去 24 小时内各个百分位组的响应时间的关系图。
- **CPU 使用率：** 显示过去 24 小时内每个实例的总 CPU 使用率的关系图。  
- **内存使用率：** 显示过去 24 小时内每个实例的总物理内存使用率的关系图。

![运行状况检查](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>调查应用程序代码问题（仅适用于 Windows 应用）

因为许多应用问题与应用程序代码中的问题相关，所以应用服务诊断集成了 [Application Insights](../azure-monitor/app/app-insights-overview.md) 来突出显示异常和依赖项问题，以便与所选故障时间进行关联。 Application Insights 必须单独启用。

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

若要查看 Application Insights 异常和依赖项，请选择“**Web 应用关闭**”或“**Web 应用速度缓慢**”磁贴快捷方式。

### <a name="troubleshooting-steps-only-for-windows-app"></a>故障排除步骤（仅适用于 Windows 应用）

如果在过去 24 小时内检测到属于特定问题类别的问题，可以查看完整的诊断报告。应用服务诊断可以提示用户查看更多的故障排除建议，以及获取引导性更强的体验所需的后续措施。

![Application Insights 和故障排除及后续步骤](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools"></a>诊断工具 

诊断工具包括更高级的类型，这些类型可帮助用户调查应用程序代码问题、运行缓慢、连接字符串等。 和前瞻性工具，该工具可帮助用户缓解 CPU 使用率、请求数和内存方面的问题。

### <a name="proactive-cpu-monitoring-only-for-windows-app"></a>主动 CPU 监视（仅适用于 Windows 应用）

当应用程序或子进程消耗 CPU 资源较高时，主动 CPU 监视可以提供一种简单且主动的操作方式。 用户可以设置自己的 CPU 阈值规则，以暂时缓解 CPU 使用率过高的问题，直到找出意外问题的真正原因。 有关详细信息，请参阅“[在 CPU 问题发生前采取缓解措施](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)”。

![主动 CPU 监视](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing"></a>自动修复 

自动修复是应用遇到意外行为时可以采取的缓解措施。 用户可以根据请求计数、请求缓慢、内存限制和 HTTP 状态代码设置自己的规则，以触发缓解措施。 使用此工具可暂时缓解意外行为，直到找出根本原因。 该工具目前可用于 Windows Web 应用、Linux Web 应用和 Linux 自定义容器。 支持的条件和缓解措施因 Web 应用的类型而异。 有关详细信息，请参阅[在应用服务诊断中发布新的自动修复体验](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)和[发布适用于 Linux 的自动修复](https://azure.github.io/AppService/2021/04/21/Announcing-Autoheal-for-Azure-App-Service-Linux.html)。

![自动修复](./media/app-service-diagnostics/auto-healing-10.png)

### <a name="proactive-auto-healing-only-for-windows-app"></a>主动自动修复（仅适用于 Windows 应用）

与主动 CPU 监视一样，主动自动修复是一种用于缓解应用程序意外行为的现成解决方案。 当应用服务确定应用处于不可恢复的状态时，主动自动修复会重启应用。 有关详细信息，请参阅“[引入前瞻性自动修复](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)”。

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>导航器和更改分析（仅适用于 Windows 应用）

在采用持续集成并且应用具有许多依赖项的大型团队中，可能很难确定导致行为不正常的特定更改。 导航器可自动渲染应用的依赖项映射以及同一订阅中的所有资源，从而帮助显示应用的拓扑。 借助导航器，用户可以查看应用及其依赖项所做更改的合并列表，并缩小导致不正常行为的更改范围。 它可通过主页磁贴“**导航器**”进行访问，在首次使用前需要先启用。 有关详细信息，请参阅“[利用导航器了解应用的依赖项](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)”。

![导航器默认页](./media/app-service-diagnostics/navigator-default-page-11.png)

![差异视图](./media/app-service-diagnostics/diff-view-12.png)

可以通过磁贴快捷方式、**应用程序更改** 以及 **应用程序** 在 **可用性和性能** 方面的崩溃来访问应用更改的更改分析，这样用户就可以将之与其他指标同时使用。 此功能必须先启用才可使用。 有关详细信息，请参阅“[在应用服务诊断中发布新的变更分析体验](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)”。

通过在标题中添加“[Diag]”，在 [UserVoice](https://feedback.azure.com/forums/169385-web-apps) 上发布问题或反馈。
