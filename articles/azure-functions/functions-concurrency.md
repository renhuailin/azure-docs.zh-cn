---
title: Azure Functions 中的并发
description: Azure Functions 中的动态并发功能的概述。
author: cachai2
ms.topic: conceptual
ms.date: 9/24/2021
ms.author: cachai
ms.openlocfilehash: 60d6861fbf0f9c02df78674f85633a962fea09a3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367528"
---
# <a name="concurrency-in-azure-functions"></a>Azure Functions 中的并发

本文介绍 Azure Functions 中事件驱动的触发器的并发行为。 此外，还介绍一种用于优化并发行为的新动态模型。 

>[!NOTE]
>动态并发模型目前为预览版。 对动态并发的支持仅限于特定的绑定扩展，这些扩展目前同样以预览版提供。  

Functions 的托管模型允许在单个计算实例上并发运行多个函数调用。 例如，假设你的函数应用已横向扩展并在多个实例上运行，其中有三个不同的函数。 在这种情况下，每个函数都会处理运行该函数应用的每个 VM 实例上的调用。 单个实例上的函数调用共享相同的 VM 计算资源，例如内存、CPU 和连接。 当应用托管在动态计划（“消耗”或“高级”）中时，平台会根据传入事件的数量纵向扩展或缩减函数应用实例的数量。 有关详细信息，请参阅[事件驱动的缩放](./Event-Driven-Scaling.md)。 将函数托管在专用（应用服务）计划中时，可以手动配置实例或[设置自动缩放方案](dedicated-plan.md#scaling)。

由于多个函数调用可在每个实例上并发运行，因此每个函数需要通过某种方式来限制它在任意给定时间处理的并发调用数。

## <a name="static-concurrency"></a>静态并发

许多触发器支持主机级静态配置模型，该模型用于指定该触发器类型的按实例并发。 例如，[服务总线触发器](./functions-bindings-service-bus-trigger.md)在 [host.json 文件](functions-host-json.md)中提供 `MaxConcurrentCalls` 和 `MaxConcurrentSessions` 设置。 这些设置共同控制每个函数在每个实例上并发处理的最大消息数。 其他触发器类型具有用于跨实例对调用进行负载均衡的内置机制。 例如，事件中心和 Azure Cosmos DB 都使用基于分区的方案。 

支持并发配置的触发器类型有一种默认行为，你可以选择在函数应用的 host.json 文件中替代该行为。 这些设置适用于所有正在运行的实例，使你可以控制每个实例上的函数的最大并发。 例如，如果你的函数是 CPU 密集型或资源密集型函数，你可以选择限制并发，使实例保持正常状态。 同样，如果你的函数向受到限制的下游服务发出请求，则你也应该考虑限制并发。 

虽然这种并发配置使你可以控制特定的触发器行为（例如限制函数），但可能很难确定这些设置的最佳值。 一般情况下，必须通过负载测试的试错过程得出可接受的值。 即使你确定某一组值适用于特定的负载配置文件，但来自连接服务的事件数量也可能每天都有变化。 这种可变性意味着应用可能经常使用次优值运行。 例如，函数应用可能会在一周的最后一天处理要求特别高的消息有效负载，这就需要降低并发。 但是，在该周的剩余时间，消息有效负载更简单，这意味着你可以在这些时间使用更高的并发级别。 

理想情况下，我们希望系统允许实例处理尽可能多的工作，同时使每个实例保持正常状态和较低的延迟，而动态并发正是为此目的而设计的。

## <a name="dynamic-concurrency-preview"></a>动态并发（预览版）

Functions 现在提供一个动态并发模型，可以简化同一计划中运行的所有函数应用的并发配置。 

> [!NOTE]
> 动态并发目前仅受服务总线触发器的支持，要求你使用 Microsoft.Azure.WebJobs.Extensions.ServiceBus 扩展[版本 5.0.0-beta.6（或更高版本）](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/5.0.0-beta.6)。

### <a name="benefits"></a>优点

使用动态并发提供以下优点： 

- 简化的配置：不再需要手动确定每个触发器的并发设置。 系统会不断地获知工作负载的最佳值。 
- 动态调整：可以实时动态调高或调低并发，使系统能够适应不断变化的负载模式。 
- 实例运行状况保护：运行时会将并发限制为函数应用实例可以轻松处理的级别。 这可以防止应用承担的工作量超过其应有的工作量，从而避免应用过载。 
- 提高了吞吐量：由于各个实例接受的工作不会超过它们可以快速处理的工作，因此总体吞吐量将得到提高。 这使得工作可以在实例之间更有效地进行负载均衡。 对于可以处理更高负载的函数，可将并发的值提高至超过默认配置值，从而提高吞吐量。

### <a name="dynamic-concurrency-configuration"></a>动态并发配置

可以在主机级别（在 host.json 文件中）启用动态并发。 启用动态并发后，支持动态并发的函数应用所用的任何绑定扩展将根据需要动态调整并发。 动态并发设置将替代针对支持动态并发的触发器手动配置的任何并发设置。 

默认已禁用动态并发。 启用动态并发后，每个函数的并发从 1 开始，并且会调整为主机确定的最佳值。

可以通过在 host.json 文件中添加以下设置，在函数应用中启用动态并发： 

```json
    { 
        "version": "2.0", 
        "concurrency": { 
            "dynamicConcurrencyEnabled": true, 
            "snapshotPersistenceEnabled": true 
        } 
    } 
```

 当 `SnapshotPersistenceEnabled` 为 `true`（默认值）时，获知的并发值会定期保存到存储中，因此新实例将从这些值而不是从 1 开始，并且必须重新获知并发值。 

### <a name="concurrency-manager"></a>并发管理器 

在幕后，如果已启用动态并发，将有一个并发管理器进程在后台运行。 此管理器持续监视实例运行状况指标（例如 CPU 和线程利用率），并根据需要更改限制。 启用一个或多个限制时，函数并发将会调低，直到主机再次正常。 禁用限制时，可以提高并发。 将根据这些限制，使用各种试探法以智能方式按需调高或调低并发。 一段时间后，每个函数的并发将稳定在特定的级别。 

每个函数的并发级别会受到管理。 因此，系统可在需要较低并发级别的资源密集型函数与可以处理较高并发的更轻型函数之间进行均衡。 均衡每个函数的并发有助于保持函数应用实例的总体正常状态。  

启用动态并发后，你会在日志中看到动态并发决策。 例如，在启用各种限制后，每当调高或调低每个函数的并发，你都会看到日志。 这些日志会写入在跟踪表中的“Host.Concurrency”日志类别下。 

### <a name="extension-support"></a>扩展支持 

动态并发是在主机级别为函数应用启用的，任何支持动态并发的扩展将在该模式下运行。 动态并发需要主机和单个触发器扩展之间的协作。 在预览版中，只有以下最新版（预览版）扩展支持动态并发。

#### <a name="service-bus"></a>服务总线 

服务总线触发器目前支持三种执行模型。 动态并发对这些执行模型的影响如下：
 
- 单一调度主题/队列处理：每次调用函数只会处理一条消息。 使用静态配置时，并发由 MaxConcurrentCalls 配置选项控制。 使用动态并发时，将忽略该配置值，并动态调整并发。 
- 基于会话的单一调度主题/队列处理：每次调用函数只会处理一条消息。 每个实例根据主题/队列的活动会话数租用一个或多个会话。 每个会话中的消息将连续进行处理，以保证会话中的顺序。 不使用动态并发时，并发由 `MaxConcurrentSessions` 设置控制。 启用动态并发时，将忽略 `MaxConcurrentSessions`，每个实例处理的会话数将动态调整。 
- 批处理：每次调用函数会处理一批消息，且并发由 `MaxMessageCount` 设置控制。 由于批处理调用是连续性的，因此批处理触发的函数的并发始终为 1，且动态并发不适用。 

要使服务总线触发器能够使用动态并发，必须使用 Microsoft.Azure.WebJobs.Extensions.ServiceBus 扩展[版本 5.0.0-beta.6（或更高版本）](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/5.0.0-beta.6)。 

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions 触发器和绑定](event-driven-scaling.md)