---
title: Azure Functions 最佳做法
description: 了解有关设计、部署和维护在 Azure 中运行的高效函数代码的最佳做法。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: f2efa490a9788f0e52b4dfb19b4359f247a671e9
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057712"
---
# <a name="best-practices-for-reliable-azure-functions"></a>可靠 Azure Functions 的最佳做法

Azure Functions 是一个事件驱动、按需计算的体验，可以扩展现有的 Azure 应用服务应用程序平台，能够实现由 Azure、第三方服务和本地系统中发生的事件触发的代码。 Functions 使你可以通过连接到数据源或消息传递解决方案来构建解决方案，从而更轻松地处理和响应事件。 Functions 在 Azure 数据中心上运行，这些数据中心包含许多集成组件，因此非常复杂。 在托管的云环境中，预计 VM 偶尔会重启或移动，并且会发生系统升级。 函数应用还可能依赖于外部 API、Azure 服务和其他数据库，而这些依赖项也很容易出现周期性的不可靠性问题。 

本文详细介绍了有关设计和部署在基于云的环境中保持正常状态和良好性能的高效函数应用的一些最佳做法。

## <a name="choose-the-correct-hosting-plan"></a>选择正确的托管计划 

在 Azure 中创建函数应用时，必须为应用选择托管计划。 选择的计划会影响性能、可靠性和成本。 Functions 提供三个基本托管计划： 

+ [消耗计划](consumption-plan.md)
+ [高级计划](functions-premium-plan.md)
+ [专用（应用服务）计划](dedicated-plan.md)

运行 Linux 或 Windows 时，所有托管计划均已正式发布 (GA)。

在应用服务平台的上下文中，用于动态托管函数的高级计划为弹性高级计划 (EP)。 还有其他一些称为高级计划的专用（应用服务）计划。 有关详细信息，请参阅[高级计划](functions-premium-plan.md)一文。

选择的托管计划决定了以下行为：

+   如何根据需求缩放函数应用，以及如何管理实例分配。
+   每个函数应用实例可用的资源。
+   对 Azure 虚拟网络连接等高级功能的支持。

若要详细了解如何选择正确的托管计划以及不同计划的详细比较，请参阅 [Azure Functions 托管选项](functions-scale.md)。

创建函数应用时，选择正确的计划非常重要。 Functions 提供受限的功能用于切换托管计划（主要是在消耗计划和弹性高级计划之间切换）。 有关详细信息，请参阅[计划迁移](functions-how-to-use-azure-function-app-settings.md?tabs=portal#plan-migration)。 

## <a name="configure-storage-correctly"></a>正确配置存储

Functions 需要一个与函数应用关联的存储帐户。 存储帐户连接由 Functions 主机用来执行各种操作，例如管理触发器和记录函数执行。 在动态缩放函数应用时，也会使用该连接。 有关详细信息，请参阅 [Azure Functions 的存储注意事项](storage-considerations.md)。

在函数应用中错误地配置文件系统或存储帐户可能会影响函数的性能和可用性。 有关对错误配置的存储帐户进行故障排除的帮助，请参阅[存储故障排除](functions-recover-storage-account.md)一文。 

### <a name="storage-connection-settings"></a>存储连接设置

动态缩放的函数应用可以从存储帐户中的 Azure 文件存储终结点运行，也可以从与横向扩展的实例关联的文件服务器运行。 此行为由以下应用程序设置进行控制：

+ [WEBSITE_CONTENTAZUREFILECONNECTIONSTRING](functions-app-settings.md#website_contentazurefileconnectionstring)
+ [WEBSITE_CONTENTSHARE](functions-app-settings.md#website_contentshare)

仅当在 Windows 上的高级计划或消耗计划中运行时，才支持这些设置。

在 Azure 门户中或者使用 Azure CLI 或 Azure PowerShell 创建函数应用时，系统会根据需要为函数应用创建这些设置。 从 Azure 资源管理器模板（ARM 模板）创建资源时，还需要在模板中包含 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`。 

使用 ARM 模板进行首次部署时，请不要包含 `WEBSITE_CONTENTSHARE`，因为系统会生成它。   

可以使用以下 ARM 模板示例来帮助正确配置这些设置：

+ [消耗计划](https://azure.microsoft.com/resources/templates/function-app-create-dynamic/)
+ [专用计划](https://azure.microsoft.com/resources/templates/function-app-create-dedicated/)
+ [具有 VNET 集成的高级计划](https://azure.microsoft.com/resources/templates/function-premium-vnet-integration/)
+ [具有部署槽位的消耗计划](https://azure.microsoft.com/resources/templates/function-app-create-dynamic-slot/)

### <a name="storage-account-configuration"></a>存储帐户配置

创建函数应用时，必须创建或链接到支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 Functions 依赖于 Azure 存储来执行管理触发器和记录函数执行等操作。 在 `AzureWebJobsStorage` 和 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 应用程序设置中可以找到函数应用的存储帐户连接字符串。

创建此存储帐户时请注意以下事项： 

+ 为了减少延迟，请在函数应用所在的同一区域中创建存储帐户。

+ 为了提高生产环境中的性能，请为每个函数应用使用不同的存储帐户。 对于 Durable Functions 和事件中心触发的函数尤其如此。 

+ 对于事件中心触发的函数，请不要使用[启用了 Data Lake Storage](https://github.com/Azure/azure-functions-eventhubs-extension/issues/81) 的帐户。

### <a name="handling-large-data-sets"></a>处理大型数据集

在 Linux 上运行时，可以通过装载文件共享来添加额外的存储。 装载共享可以方便地使函数处理现有的大型数据集。 有关详细信息，请参阅[装载文件共享](storage-considerations.md#mount-file-shares)。

## <a name="organize-your-functions"></a>组织函数 

你可能会开发并发布多个函数作为解决方案的一部分。 这些函数通常组合到单个函数应用中，但也可以分别在多个函数应用中运行。 在高级和专用（应用服务）托管计划中，多个函数应用也可以通过在同一计划中运行来共享相同的资源。 函数和函数应用的分组方式会影响整个解决方案的性能、缩放、配置、部署和安全性等方面。 

对于消耗和高级计划，函数应用中的所有函数将一起动态缩放。

有关如何组织函数的详细信息，请参阅[函数组织最佳做法](performance-reliability.md#function-organization-best-practices)。

## <a name="optimize-deployments"></a>优化部署

部署函数应用时，请务必记住，Azure 中函数的部署单位是函数应用。 函数应用中的所有函数将同时部署，它们通常来自同一个部署包。  

要成功完成部署，请考虑采取以下做法：

+  使函数从部署包运行。 这种[从包运行的方法](run-functions-from-deployment-package.md)具有以下优点：

    + 减少文件副本锁定问题的风险。 
    + 可以直接部署到生产应用，从而触发重启。 
    + 知道包中的所有文件都可用于你的应用。 
    + 提高 ARM 模板部署性能。
    + 可以减少冷启动时间，特别是对于具有大型 npm 包树的 JavaScript 函数。

+ 考虑使用[持续部署](functions-continuous-deployment.md)将部署连接到源代码管理解决方案。 持续部署还让你可以从部署包运行。

+ 对于[高级计划托管](functions-premium-plan.md)，请考虑添加预热触发器，以便在添加新实例时减少延迟。 有关详细信息，请参阅 [Azure Functions 预热触发器](functions-bindings-warmup.md)。 

## <a name="write-robust-functions"></a>编写可靠的函数

编写有助于在一般情况下保持函数性能和可用性的函数代码时，可以遵循几项设计原则。 这些原则包括：
 
+ [避免使用长时间运行的函数。](performance-reliability.md#avoid-long-running-functions) 
+ [规划跨函数通信。](performance-reliability.md#cross-function-communication) 
+ [将函数编写为无状态。](performance-reliability.md#write-functions-to-be-stateless)
+ [编写防御函数。](performance-reliability.md#write-defensive-functions)

由于暂时性故障在云计算中很常见，因此在访问基于云的资源时，应使用[重试模式](/azure/architecture/patterns/retry)。 许多触发器和绑定已实现重试。 

## <a name="design-for-security"></a>安全设计

最好是在规划阶段而不是在函数准备就绪后考虑安全因素。 若要了解如何安全开发和部署函数，请参阅[保护 Azure Functions](security-concepts.md)。  

## <a name="consider-concurrency"></a>考虑并发

随着对函数应用的需求因传入事件的增加而不断累积，在消耗和高级计划中运行的函数应用会横向扩展。必须了解函数应用如何响应负载，以及如何配置触发器来处理传入事件。 有关一般概述，请参阅 [Azure Functions 中事件驱动的缩放](event-driven-scaling.md)。

专用（应用服务）计划要求为函数应用横向扩展做好准备。 

### <a name="worker-process-count"></a>工作进程计数

在某些情况下，横向扩展之前在实例中创建多个进程（称为语言工作进程）可以更有效地处理负载。允许的最大语言工作进程数由 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 设置进行控制。 此设置的默认值为 `1`，这意味着不使用多个进程。 达到最大进程数后，函数应用将横向扩展为更多实例以处理负载。 此设置不适用于在主机进程中运行的 [C# 类库函数](functions-dotnet-class-library.md)。

在高级计划或专用（应用服务）计划上使用 `FUNCTIONS_WORKER_PROCESS_COUNT` 时，请注意计划提供的核心数。 例如，高级计划 `EP2` 提供两个核心，因此应从 `2` 值开始，然后根据需要增加两个核心，直到达到最大数目。

### <a name="trigger-configuration"></a>触发器配置

规划吞吐量和缩放时，必须了解不同类型的触发器如何处理事件。 某些触发器允许控制批处理行为和管理并发。 调整这些选项中的值往往有助于每个实例根据被调用函数的需求适当缩放。 这些配置选项将应用于函数应用中的所有触发器，并在应用的 host.json 文件中进行维护。 有关设置详细信息，请参阅具体触发器参考文章的“配置”部分。

若要详细了解 Functions 如何处理消息流，请参阅 [Azure Functions 可靠事件处理](functions-reliable-event-processing.md)。

### <a name="plan-for-connections"></a>规划连接

在[消耗计划](consumption-plan.md)中运行的函数应用存在连接限制。 这些限制是按实例强制实施的。 由于这些限制并根据一般最佳做法，你应该从函数代码优化出站连接。 有关详细信息，请参阅[管理 Azure Functions 中的连接](manage-connections.md)。 

### <a name="language-specific-considerations"></a>特定于语言的注意事项

对于选择的语言，请注意以下事项：

# <a name="c"></a>[C#](#tab/csharp)

+ [使用异步代码，但避免阻塞调用](performance-reliability.md#use-async-code-but-avoid-blocking-calls)。

+ [使用取消标记](functions-dotnet-class-library.md?#cancellation-tokens)（仅限进程内）。

# <a name="java"></a>[Java](#tab/java)

+ 对于混合了 CPU 密集型和 IO 密集型操作的应用程序，请考虑使用[更多工作进程](functions-app-settings.md#functions_worker_process_count)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

+ [使用 `async` 和 `await`](functions-reference-node.md#use-async-and-await)。

+ [为 CPU 密集型应用程序使用多个工作进程](functions-reference-node.md?tabs=v2#scaling-and-concurrency)。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ [查看并发注意事项](functions-reference-powershell.md#concurrency)。

# <a name="python"></a>[Python](#tab/python)

+ [在 Azure Functions 中提高 Python 应用的吞吐量性能](python-scale-performance-reference.md)

---

## <a name="maximize-availability"></a>最大化可用性

冷启动是无服务器体系结构的重要考虑因素。 有关详细信息，请参阅[冷启动](event-driven-scaling.md#cold-start)。 如果冷启动是方案的考虑因素，可以在[了解无服务器冷启动](https://azure.microsoft.com/blog/understanding-serverless-cold-start/)文章中找到更深入的信息。 

高级计划是在保持动态缩放能力的同时减少冷启动次数的建议计划。 可以使用以下指导来减少冷启动次数并提高所有三个托管计划中的可用性。 

| 计划 | 指南 |
| --- | --- | 
| **高级计划** | • [在函数应用中实现预热触发器](functions-bindings-warmup.md)<br/>• [设置始终就绪实例和最大突发限制的值](functions-premium-plan.md#plan-and-sku-settings)<br/>• [在虚拟网络上使用非 HTTP 触发器时使用虚拟网络触发器支持](functions-networking-options.md#premium-plan-with-virtual-network-triggers)|
| **专用计划** | • [在启用了 Azure 应用服务运行状况检查的至少两个实例上运行](../app-service/monitor-instances-health-check.md)<br/>• [实现自动缩放](/azure/architecture/best-practices/auto-scaling)|
| **消耗计划** | • 查看对绑定和触发器使用的单一实例模式和并发设置，避免人为地对函数应用的缩放方式施加限制。<br/>• [查看可能会限制横向扩展的 `functionAppScaleLimit` 设置](event-driven-scaling.md#limit-scale-out)<br/>• 在开发和测试期间检查设置的每日使用配额 (GB-Sec) 限制。 考虑在生产环境中消除此限制。 |

## <a name="monitor-effectively"></a>有效监视

Azure Functions 提供与 Azure Application Insights 的内置集成，以监视函数执行以及从代码写入的跟踪。 若要了解详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。 Azure Monitor 还提供用于监视函数应用本身运行状况的工具。 若要了解详细信息，请参阅[将 Azure Monitor 指标与 Azure Functions 配合使用](monitor-metrics.md)。

使用 Application Insights 集成来监视函数时，应注意以下事项：

+ 确保已删除 [AzureWebJobsDashboard](functions-app-settings.md#azurewebjobsdashboard) 应用程序设置。 旧版 Functions 中支持此设置。 如果存在 `AzureWebJobsDashboard`，将其删除可以提高函数的性能。 

+  查看 [Application Insights 日志](analyze-telemetry-data.md)。 如果你需要查找的数据缺失，请考虑调整采样设置，以便更好地捕获监视方案。 可以使用 `excludedTypes` 设置从采样中排除某些类型，例如 `Request` 或 `Exception`。 若要了解详细信息，请参阅[配置采样](configure-monitoring.md?tabs=v2#configure-sampling)。

使用 Azure Functions，还可以[将系统生成的日志和用户生成的日志发送到 Azure Monitor 日志](functions-monitor-log-analytics.md)。 与 Azure Monitor 日志的集成目前以预览版提供。 

## <a name="build-in-redundancy"></a>内置冗余

你的业务需求可能要求即使在数据中心服务中断期间，函数也始终保持可用。 若要了解如何使用多区域方法来使关键函数始终保持运行，请参阅 [Azure Functions 异地灾难恢复和高可用性](functions-geo-disaster-recovery.md)。

## <a name="next-steps"></a>后续步骤

[管理函数应用](functions-how-to-use-azure-function-app-settings.md)
