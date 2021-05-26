---
title: Durable Functions 中的版本控制 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中实现版本控制。
author: cgillum
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: azfuncdf
ms.openlocfilehash: c5d3ac097efd81b369db16d6f7b6a4bf59b7540a
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110377392"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Durable Functions 中的版本控制 (Azure Functions)

在应用程序的生存期内添加、删除和更改函数是不可避免的。 [Durable Functions](durable-functions-overview.md) 允许以之前不可能的方式将函数链接在一起，且此链接将影响处理版本控制的方式。

## <a name="how-to-handle-breaking-changes"></a>如何处理重大更改

需要知道一些重大更改示例。 本文介绍最常见的示例。 所有这些示例背后的主题都是，新的和现有的函数业务流程均受函数代码更改所影响。

### <a name="changing-activity-or-entity-function-signatures"></a>更改活动或实体函数签名

签名更改涉及到函数名称、输入或输出的更改。 如果对活动或实体函数进行了此类更改，则可能会中断依赖于它的任何业务流程协调程序函数。 如果更新业务流程协调程序函数以适应此更改，则可能会中断现有的正在进行的实例。

例如，假设有以下 C# 业务流程协调程序函数。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

此简单函数接收“Foo”结果并将其传递给“Bar”。 假设需要将返回值“Foo”从 `bool` 更改为 `int` 以支持各种不同的结果值。 结果类似以下形式：

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string result = await context.CallActivityAsync<string>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

此更改会应用于业务流程协调程序函数的所有新实例，但会中断任何正在进行的实例。 例如，请考虑这样一种情况：业务流程实例调用名为 `Foo` 的函数并先后返回布尔值和检查点。 如果此时部署签名更改，则检查点实例在恢复和重播对 `Foo` 的调用时将立即失败。 发生此故障是因为历史记录表中的结果为 `bool`，而新代码尝试将其反序列化为 `string`，从而导致类型安全语言运行时异常。

此示例只是函数签名更改可以中断现有实例的许多不同方法之一。 一般情况下，如果业务流程协调程序需要更改其调用函数的方式，则此更改可能会出现问题。

### <a name="changing-orchestrator-logic"></a>更改业务流程协调程序逻辑

另一类版本控制问题来自于，以与更改正在进行的实例的执行路径的方式更改业务流程协调程序函数代码。

请考虑以下 C# 业务流程协调程序函数：

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

现在，假设要做出更改，在两个现有函数调用之间添加新的函数调用。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

此更改会向“Foo”和“Bar”之间的“SendNotification”添加新的函数调用。 不存在签名更改。 在从对“Bar”的调用中恢复现有实例时，将出现问题。 在重播过程中，如果对“Foo”的原始调用返回 `true`，则业务流程协调程序重播将调用不在其执行历史记录中的“SendNotification” 。 Durable Task Framework 检测到这种不一致，并引发 `NonDeterministicOrchestrationException`，因为在它希望出现对 Bar 的调用时出现了对 SendNotification 的调用 。 将 API 调用添加到其他持久操作（例如创建持久计时器、等待外部事件、调用子业务流程等）时，可能会发生相同类型的问题。

## <a name="mitigation-strategies"></a>缓解策略

以下是一些用于处理版本控制问题的策略：

* 不执行任何操作（不推荐）
* 停止所有正在进行的实例
* 并行部署

### <a name="do-nothing"></a>不执行任何操作

版本控制最简单的方法是不执行任何操作，让正在进行的业务流程实例失败。 根据更改的类型，可能会发生以下类型的故障。

* 业务流程可能会失败并出现 `NonDeterministicOrchestrationException` 错误。
* 业务流程可能会无限期停滞，报告 `Running` 状态。
* 如果删除函数，则尝试调用该函数的任何函数都可能会失败并出现错误。
* 如果函数在计划运行后被删除，则应用可能会遇到 Durable Task Framework 引擎中的低级别运行时故障，可能会导致性能严重降低。

由于这些潜在的故障，因此不建议使用“不执行任何操作”策略。

### <a name="stop-all-in-flight-instances"></a>停止所有正在进行的实例

另一个选项是停止所有正在进行的实例。 如果使用默认的 [Durable Functions 的 Azure 存储提供程序](durable-functions-storage-providers.md#azure-storage)，可以通过清除内部 control-queue 和 workitem-queue 队列的内容来停止所有实例 。 或者，可以停止函数应用，删除这些队列，然后重启该应用。 应用重启后，将自动重新创建队列。 以前的业务流程实例可能无限期地保持“正在运行”状态，但它们不会通过失败消息打乱日志，或者对应用造成任何损害。 这是适用于快速原型开发（包括本地开发）的理想方法。

> [!NOTE]
> 此方法需要直接访问基础存储资源，可能并不适合 Durable Functions 支持的所有存储提供程序。

### <a name="side-by-side-deployments"></a>并行部署

确保安全部署中断更改的最万无一失的方法是将其与较旧版本进行并行部署。 可使用以下任何方法来完成此操作：

* 将所有更新部署为全新的函数，保持现有函数不变。 由于以递归方式更新新函数版本的调用方所涉及的复杂性，因此通常不建议这样做。
* 将所有更新部署为使用不同存储帐户的新函数应用。
* 使用相同的存储帐户但使用更新的[任务中心](durable-functions-task-hubs.md)名称来部署函数应用的新副本。 这会导致创建新版本的应用可使用的新存储项目。 旧版本的应用程序将继续使用以前的一组存储项目来执行。

建议使用并行部署来部署函数应用的新版本。

> [!NOTE]
> 此并行部署策略指南使用特定于 Azure 存储的术语，但一般适用于所有受支持的 [Durable Functions 存储提供程序](durable-functions-storage-providers.md)。

### <a name="deployment-slots"></a>部署槽

在 Azure Functions 或 Azure 应用服务中执行并行部署时，建议将新版本的函数应用部署到新的[部署槽位](../functions-deployment-slots.md)。 通过部署槽位，可以并行运行函数应用的多个副本，且仅其中一个槽位为活动生产槽位。 当准备好向现有基础结构公开新业务流程逻辑时，它可以像将新版本交换到生产槽一样简单。

> [!NOTE]
> 在对业务流程协调程序函数使用 HTTP 和 webhook 触发器时，此策略效果最佳。 对于非 HTTP 触发器（如队列或事件中心），触发器定义应[派生自在交换操作过程中更新的应用设置](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何处理性能和缩放问题](durable-functions-perf-and-scale.md)
