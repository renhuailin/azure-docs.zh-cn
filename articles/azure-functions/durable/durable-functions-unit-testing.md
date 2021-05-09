---
title: Azure Durable Functions 单元测试
description: 了解如何进行 Durable Functions 单元测试。
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 3d3b89337d4643b59d37ebe0aaeaf6f207bf053e
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108165436"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions 单元测试

单元测试是现代软件开发实践中的重要组成部分。 单元测试可验证业务逻辑行为，防止将来引入无法察觉的中断性变更。 Durable Functions 的复杂性很容易增大，因此，引入单元测试有助于避免中断性变更。 以下部分介绍如何对三种函数类型执行单元测试 - 业务流程客户端、业务流程协调程序和活动函数。

> [!NOTE]
> 本文提供了针对 Durable Functions 2.x 的 Durable Functions 应用的单元测试指南。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

## <a name="prerequisites"></a>先决条件

学习本文中的示例需要了解以下概念和框架：

* 单元测试

* Durable Functions

* [xUnit](https://github.com/xunit/xunit) - 测试框架

* [moq](https://github.com/moq/moq4) - 模拟框架

## <a name="base-classes-for-mocking"></a>用于模拟的基类

通过以下接口支持模拟：

* [IDurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient)、[IDurableEntityClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableentityclient) 和 [IDurableClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableclient)

* [IDurableOrchestrationContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext)

* [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableactivitycontext)

* [IDurableEntityContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableentitycontext)

这些接口可以与 Durable Functions 支持的各种触发器和绑定配合使用。 执行 Azure Functions 时，函数运行时会运行包含这些接口的具体实现的函数代码。 对于单元测试，可以传递这些接口的模拟版本来测试业务逻辑。

## <a name="unit-testing-trigger-functions"></a>对触发器函数进行单元测试

在此部分，单元测试将会验证用于启动新业务流程的以下 HTTP 触发器函数的逻辑。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

单元测试任务是验证响应有效负载中提供的 `Retry-After` 标头的值。 因此，单元测试将模拟某些 `IDurableClient` 方法，以确保行为可预测。

首先，使用模拟框架（在此示例中为 [moq](https://github.com/moq/moq4)）来模拟 `IDurableClient`：

```csharp
// Mock IDurableClient
var durableClientMock = new Mock<IDurableClient>();
```

> [!NOTE]
> 尽管可以通过直接将接口实现为类来模拟接口，但模拟框架从各方面简化了该过程。 例如，如果在多个次要版本中向接口添加了新的方法，则 moq 不需要任何代码更改，这与具体的实现不同。

然后，模拟 `StartNewAsync` 方法来返回已知的实例 ID。

```csharp
// Mock StartNewAsync method
durableClientMock.
    Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
    ReturnsAsync(instanceId);
```

接下来模拟 `CreateCheckStatusResponse`，以便始终返回空白的 HTTP 200 响应。

```csharp
// Mock CreateCheckStatusResponse method
durableClientMock
    // Notice that even though the HttpStart function does not call IDurableClient.CreateCheckStatusResponse() 
    // with the optional parameter returnInternalServerErrorOnFailure, moq requires the method to be set up
    // with each of the optional parameters provided. Simply use It.IsAny<> for each optional parameter
    .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId, returnInternalServerErrorOnFailure: It.IsAny<bool>())
    .Returns(new HttpResponseMessage
    {
        StatusCode = HttpStatusCode.OK,
        Content = new StringContent(string.Empty),
        Headers =
        {
            RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
        }
    });
```

另外还要模拟 `ILogger`：

```csharp
// Mock ILogger
var loggerMock = new Mock<ILogger>();
```

现在，从单元测试调用 `Run` 方法：

```csharp
// Call Orchestration trigger function
var result = await HttpStart.Run(
    new HttpRequestMessage()
    {
        Content = new StringContent("{}", Encoding.UTF8, "application/json"),
        RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
    },
    durableClientMock.Object,
    functionName,
    loggerMock.Object);
```

 最后一步是将输出与预期值进行比较：

```csharp
// Validate that output is not null
Assert.NotNull(result.Headers.RetryAfter);

// Validate output's Retry-After header value
Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

合并所有步骤后，单元测试将获得以下代码：

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>对业务流程协调程序函数进行单元测试

业务流程协调程序包含的业务逻辑通常要多得多，因此，它们的单元测试更有趣。

在本部分，单元测试将验证 `E1_HelloSequence` 业务流程协调程序函数的输出：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

单元测试代码首先会创建模拟：

```csharp
var durableOrchestrationContextMock = new Mock<IDurableOrchestrationContext>();
```

然后模拟活动方法调用：

```csharp
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

接下来，单元测试调用 `HelloSequence.Run` 方法：

```csharp
var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

最后验证输出：

```csharp
Assert.Equal(3, result.Count);
Assert.Equal("Hello Tokyo!", result[0]);
Assert.Equal("Hello Seattle!", result[1]);
Assert.Equal("Hello London!", result[2]);
```

合并所有步骤后，单元测试将获得以下代码：

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>对活动函数进行单元测试

可以像测试非持久性函数一样对活动函数进行单元测试。

在本部分，单元测试将验证 `E1_SayHello` 活动函数的行为：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

另外，单元测试将验证输出格式。 单元测试可以直接使用参数类型，也可以模拟 `IDurableActivityContext` 类：

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
>
> [详细了解 moq](https://github.com/Moq/moq4/wiki/Quickstart)
