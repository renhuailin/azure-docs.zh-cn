---
title: Azure Functions 最佳做法
description: 了解 Azure Functions 的最佳做法和模式。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 89ff49b3ea5abae7ced046f714d34943a58c64a6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428294"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>优化 Azure Functions 的性能和可靠性

本文为提高[无服务器](https://azure.microsoft.com/solutions/serverless/)函数应用的性能和可靠性提供了指南。  

## <a name="general-best-practices"></a>常规最佳做法

下面是有关如何使用 Azure Functions 生成和构建无服务器解决方案的最佳做法。

### <a name="avoid-long-running-functions"></a>避免使用长时间运行的函数

长时间运行的大型函数可能会引起意外超时问题。 若要详细了解给定托管计划的超时，请参阅[函数应用超时持续时间](functions-scale.md#timeout)。 

由于含有许多 Node.js 依赖项，函数规模可能会变得很大。 导入依赖项也会导致加载时间增加，引起意外的超时问题。 显式和隐式加载依赖项。 由代码加载的单个模块可能会加载自己的附加模块。 

尽可能将大型函数重构为可协同工作且快速返回响应的较小函数集。 例如，webhook 或 HTTP 触发器函数可能需要在特定时间限制内确认响应；webhook 需要快速响应，这很常见。 可将 HTTP 触发器有效负载传递到由队列触发器函数处理的队列。 此方法允许延迟实际工作并返回即时响应。


### <a name="cross-function-communication"></a>跨函数通信

[Durable Functions](durable/durable-functions-overview.md) 和 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)用于管理状态转换以及多个函数之间的通信。

如果不使用 Durable Functions 或逻辑应用来集成多个函数，则最好是将存储队列用于跨函数通信。 主要原因是与其他存储选项相比，存储队列成本更低且更易预配。 

存储队列中各消息的大小限制为 64 KB。 如果需要在函数之间传递更大的消息，可使用 Azure 服务总线队列，以在标准层中支持最大为 256 KB 的消息大小，在高级层中最大为 1 MB 的消息大小。

如果在处理前需要筛选消息，则服务总线主题十分有用。

对于支持大容量通信，事件中心十分有用。


### <a name="write-functions-to-be-stateless"></a>将函数编写为无状态 

如有可能，函数应为无状态和幂等。 将任何所需的状态信息与用户的数据相关联。 例如，正在处理的排序可能具有关联的 `state` 成员。 函数本身保持无状态时，该函数可根据该状态处理排序。 

对于计时器触发器，特别建议采用幂等函数。 例如，如果有必须每天运行一次的内容，则编写它，使它可在一天内的任何时间运行，并生成相同的结果。 某天没有任何工作时，可退出该函数。 此外，如果未能完成以前的运行，则下次运行应从中断的位置继续运行。


### <a name="write-defensive-functions"></a>编写防御函数

假定任何时候函数都可能会遇到异常。 设计函数，使其具有在下次执行期间从上一失败点继续执行的能力。 请考虑需执行以下操作的方案：

1. 在数据库中进行 10,000 行的查询。
2. 为每行创建队列消息，从而处理下一行。
 
根据系统复杂程度，可能有：行为有误的相关下游服务，网络故障或已达配额限制等等。所有这些可在任何时间影响用户的函数。 需设计函数，使其做好该准备。

如果将 5,000 个那些项插入到队列中进行处理，然后发生故障，代码将如何响应？ 跟踪已完成的一组中的项。 否则，下次可能再次插入它们。 这种双插入可能会严重影响工作流，因此请[将函数设置为幂等](functions-idempotent.md)。 

如果已处理队列项，则允许函数不执行任何操作。

利用已为 Azure Functions 平台中使用的组件提供的防御措施。 有关示例，请参阅 [Azure 存储队列触发器和绑定](functions-bindings-storage-queue-trigger.md#poison-messages)文档中的 **处理有害队列消息**。 

## <a name="function-organization-best-practices"></a>函数组织最佳实践

作为你的解决方案的一部分，你可以开发和发布多个函数。 这些函数通常组合到一个函数应用中，但也可以在单独的函数应用中运行。 在高级和专用 (应用服务) 托管计划中，多个函数应用也可以通过在同一计划中运行来共享相同的资源。 如何对函数和函数应用进行分组会影响整个解决方案的性能、缩放、配置、部署和安全性。 没有适用于每种方案的规则，因此在规划和开发函数时，请考虑本节中的信息。

### <a name="organize-functions-for-performance-and-scaling"></a>组织函数以进行性能和缩放

你创建的每个函数都有内存占用量。 虽然这种占用通常很小，但函数应用内的函数过多可能会导致应用在新实例上的启动速度变慢。 这也意味着，函数应用程序的总体内存使用率可能会更高。 很难说，在一个应用中应该有多少个函数，这取决于您的特定工作负荷。 但是，如果函数在内存中存储了大量数据，请考虑在单个应用中使用更少的函数。

如果在单个高级计划或专用 (应用服务) 计划中运行多个函数应用，则这些应用将一起扩展。 如果有一个函数应用比其他函数具有更高的内存要求，则会在应用部署到的每个实例上使用不相称的内存资源量。 由于这可能会使更少的内存可用于每个实例上的其他应用程序，因此你可能希望在其自己的单独宿主计划中运行类似于此的高内存使用的函数应用。

> [!NOTE]
> 使用 [消耗计划](./functions-scale.md)时，我们建议你始终将每个应用置于其自己的计划中，因为仍要单独缩放应用。

考虑是否要将函数分组为不同的负载配置文件。 例如，如果您有一个函数用于处理成千上万个队列消息，而另一个只是偶尔调用但具有高内存要求，则您可能需要将它们部署到不同的函数应用中，以便它们获取自己的资源集，并相互独立地扩展。

### <a name="organize-functions-for-configuration-and-deployment"></a>组织用于配置和部署的函数

函数应用具有一个 `host.json` 文件，该文件用于配置函数触发器和 Azure Functions 运行时的高级行为。 对文件所做的更改 `host.json` 适用于应用中的所有函数。 如果你的某些函数需要自定义配置，请考虑将它们移动到其自己的函数应用中。

本地项目中的所有函数作为一组文件一起部署到 Azure 中的函数应用。 你可能需要单独部署单独的功能，或使用 [部署槽](./functions-deployment-slots.md) 等功能来实现某些功能，而不是其他功能。 在这种情况下，应将这些函数 (部署在单独的代码项目中，) 到不同的函数应用。

### <a name="organize-functions-by-privilege"></a>按权限组织函数 

应用程序设置中存储的连接字符串和其他凭据为函数应用中的所有函数提供了关联资源中相同的权限集。 请考虑将具有特定凭据访问权限的函数数量降至最少，具体方法是将不使用这些凭据的函数移动到单独的函数应用中。 你始终可以使用诸如[函数链](/learn/modules/chain-azure-functions-data-using-bindings/)之类的技术在不同函数应用中的函数之间传递数据。  

## <a name="scalability-best-practices"></a>可伸缩性最佳做法

有许多因素会影响函数应用实例的缩放方式。 有关[函数缩放](functions-scale.md)的文档中提供了详细信息。  下面是确保以最佳方式缩放函数应用的最佳做法。

### <a name="share-and-manage-connections"></a>共享和管理连接

只要可能，请重用与外部资源的连接。 请参阅[如何管理 Azure Functions 中的连接](./manage-connections.md)。

### <a name="avoid-sharing-storage-accounts"></a>避免共享存储帐户

创建函数应用时，必须将其与存储帐户相关联。 存储帐户连接在 [AzureWebJobsStorage 应用程序设置](./functions-app-settings.md#azurewebjobsstorage)中进行维护。 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>请勿在同一函数应用中混合测试和生产代码

Function App 中的各函数共享资源。 例如，共享内存。 如果生产中使用的是 Function App，则请勿向其添加与测试相关的函数和资源。 生产代码执行期间，这可能会导致意外的开销。

请注意在生产 Function App 中加载的内容。 将内存平均分配给应用中的每个函数。

如果在多个 .NET 函数中引用共享程序集，请将其放在常用的共享文件夹中。 否则，可能会意外部署在函数之间表现不同的同一二进制的多个版本。

请勿在生产代码中使用详细日志记录，否则会对性能产生负面影响。

### <a name="use-async-code-but-avoid-blocking-calls"></a>使用异步代码，但避免阻止调用

异步编程是推荐的最佳做法，在涉及到阻止 I/O 操作时更是如此。

在 C# 中，请始终避免引用 `Result` 属性或在 `Task` 实例上调用 `Wait` 方法。 这种方法会导致线程耗尽。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>使用多个工作进程

默认情况下，Functions 的任何主机实例均使用单个工作进程。 若要提高性能，尤其是在单线程运行时（如 Python），请使用 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 将每个主机的工作进程数增加 (多达10个) 。 然后，Azure Functions 会尝试在这些工作进程之间平均分配同步函数调用。 

FUNCTIONS_WORKER_PROCESS_COUNT 适用于 Functions 在横向扩展应用程序以满足需求时创建的每个主机。 

### <a name="receive-messages-in-batch-whenever-possible"></a>尽量批量接收消息

某些触发器（例如事件中心）允许通过单次调用接收一批消息。  批处理消息可大幅提升性能。  可以根据 [host.json 参考文档](functions-host-json.md)中的详述，在 `host.json` 文件中配置最大批大小

对于 C# 函数，可将类型更改为强类型化数组。  例如，方法签名可以是 `EventData[] sensorEvent`，而不是 `EventData sensorEvent`。  对于其他语言，需要将中的基数属性显式设置为，以 `function.json` `many` 启用批处理， [如下所示](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)。

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>配置主机行为以更好地处理并发性

使用函数应用中的 `host.json` 文件可以配置主机运行时和触发器行为。  除了批处理行为以外，还可以管理大量触发器的并发性。 调整这些选项中的值往往有助于每个实例根据被调用函数的需求适当缩放。

host.json 文件中的设置应用于应用中的所有函数，以及函数的单个实例。 例如，如果你有一个具有两个 HTTP 函数且 [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) 请求设置为25的函数应用，则对 HTTP 触发器的请求会计入共享的25个并发请求。  当该函数应用扩展为10个实例时，十个函数会有效地允许250个 (10 个实例 * 25 个并发请求的并发请求) 。 

可在 [host.json 配置文章](functions-host-json.md)在找到其他主机配置选项。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* [如何在 Azure Functions 中管理连接](manage-connections.md)
* [Azure 应用服务最佳实践](../app-service/app-service-best-practices.md)
