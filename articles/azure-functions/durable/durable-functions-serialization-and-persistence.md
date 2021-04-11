---
title: Durable Functions 中的数据保留和序列化 - Azure
description: 了解 Azure Functions 的 Durable Functions 扩展如何保留数据
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 91c04b3943af5eee436bb4a18a946000b76cff3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057977"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的数据保留和序列化

Durable Functions 会自动将函数参数、返回值和其他状态保留到持久后端，以便提供可靠的执行。 但是，保留到持久存储的数据量和频率可能会影响应用程序性能和存储事务成本。 可能还需要考虑数据保留和隐私策略，具体取决于应用程序存储的数据的类型。

## <a name="azure-storage"></a>Azure 存储

默认情况下，Durable Functions 将数据保留到你指定的 [Azure 存储](https://azure.microsoft.com/services/storage/)帐户中的队列、表和 blob。

### <a name="queues"></a>队列

Durable Functions 使用 Azure 存储队列来可靠地计划所有函数执行。 这些队列消息包含函数输入或输出，具体取决于消息是用于计划某个执行还是将值返回到调用函数。 这些队列消息还包括 Durable Functions 用于内部用途的其他元数据，例如路由和端到端关联。 在为收到的消息做出响应而执行完某个函数后，该消息会被删除，执行的结果也可能会被保留到 Azure 存储表或 Azure 存储 Blob。

在单个[任务中心](durable-functions-task-hubs.md)内，Durable Functions 会创建消息并将其添加到一个名为 `<taskhub>-workitem` 的工作项队列（用于计划活动函数），以及一个或多个名为 `<taskhub>-control-##` 的控制队列（用于计划或恢复业务流程协调程序和实体函数）。 控制队列的数目等于为应用程序配置的分区数。 有关队列和分区的详细信息，请参阅[性能和可伸缩性文档](durable-functions-perf-and-scale.md)。

### <a name="tables"></a>表

业务流程成功处理消息后，其生成操作的记录会保留到名为 `<taskhub>History` 的历史记录表。 业务流程的输入、输出和自定义状态数据也会保留到名为 `<taskhub>Instances` 的实例表。

### <a name="blobs"></a>Blob

大多数情况下，Durable Functions 不会使用 Azure 存储 Blob 来保留数据。 但是，队列和表的[大小限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits)可能会阻止 Durable Functions 将所有必需数据保留到存储行或队列消息中。 例如，当序列化后需要保留到队列中的一段数据大于 45 KB 时，Durable Functions 会改为压缩数据并将其存储在 blob 中。 以这种方式将数据保留到 blob 存储时，Durable Functions 会在表行或队列消息中存储对该 blob 的引用。 当 Durable Functions 需要检索数据时，它会自动从 blob 中提取数据。 这些 blob 存储在 blob 容器 `<taskhub>-largemessages` 中。

> [!NOTE]
> 对于较大的消息，额外的压缩和 blob 操作步骤可能会导致 CPU 和 I/O 延迟方面的成本高昂。 此外，Durable Functions 需要在内存中加载保留的数据，并且可能会同时为许多不同的函数执行而这样做。 因此，保留较大的数据有效负载也可能导致内存使用率过高。 若要最大程度地减少内存开销，请考虑手动保留较大的数据有效负载（例如，将其保留在 blob 存储中），改为传递对该数据的引用。 这样，代码就可以仅在需要时加载数据，避免在[业务流程协调程序函数重播](durable-functions-orchestrations.md#reliability)期间出现冗余负载。 但是，不建议将有效负载存储到磁盘，因为磁盘上的状态并不保证可用，这是因为函数在其整个生存期内可能会在不同的 VM 上执行。

### <a name="types-of-data-that-is-serialized-and-persisted"></a>序列化并保留的数据的类型
下面列出了在使用 Durable Functions 的功能时将要序列化并保留的不同类型的数据：

- 业务流程协调程序、活动和实体函数的所有输入和输出，包括任何 ID 和未处理的异常
- 业务流程协调程序、活动和实体函数的名称
- 外部事件名称和有效负载
- 自定义业务流程状态有效负载
- 业务流程终止消息
- 持久计时器有效负载
- 持久 HTTP 请求和响应 URL、标头和有效负载
- 实体调用和信号有效负载
- 实体状态有效负载

### <a name="working-with-sensitive-data"></a>处理敏感数据
使用 Azure 存储时，所有数据都会自动进行静态加密。 但是，有权访问存储帐户的任何人都可以读取采用未加密格式的数据。 如果需要对敏感数据进行更强的保护，请考虑先使用自己的加密密钥对数据进行加密，以便 Durable Functions 以预加密格式保留数据。

另外，.NET 用户也可以选择实现提供自动加密的自定义序列化提供程序。 可以在[此 GitHub 示例](https://github.com/charleszipp/azure-durable-entities-encryption)中找到一个包含加密功能的自定义序列化的示例。

> [!NOTE]
> 如果决定实施应用程序级别的加密，请注意，业务流程和实体可以存在无限长的时间。 这一点在需要轮换加密密钥时很重要，因为业务流程或实体运行的时间可能比密钥轮换策略要长。 如果发生密钥轮换，则下一次执行业务流程或实体时，用于加密数据的密钥也许不再可用于对数据进行解密。 因此，仅当应在相对较短的时段内运行业务流程和实体时，才建议进行客户加密。

## <a name="customizing-serialization-and-deserialization"></a>自定义序列化和反序列化

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>默认序列化逻辑

Durable Functions 在内部使用 [Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) 将业务流程和实体数据序列化为 JSON。 Durable Functions 用于 Json.NET 的默认设置为：

**输入、输出和状态：**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**异常：**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

有关 `JsonSerializerSettings` 的更详细文档，请参阅[此文](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm)。

## <a name="customizing-serialization-with-net-attributes"></a>使用 .NET 特性自定义序列化

序列化数据时，Json.NET 会在类和属性上查找[各种特性](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm)，这些特性控制如何从 JSON 序列化和反序列化数据。 如果你拥有传递到 Durable Functions API 的数据类型的源代码，请考虑将这些特性添加到类型以自定义序列化和反序列化。

## <a name="customizing-serialization-with-dependency-injection"></a>使用依赖项注入自定义序列化

面向 .NET 并在 Functions V3 运行时上运行的函数应用可以使用[依赖项注入 (DI)](../functions-dotnet-dependency-injection.md) 来自定义数据和异常的序列化方式。 下面的示例代码演示了如何使用 DI 通过 `IMessageSerializerSettingsFactory` 和 `IErrorSerializerSettingsFactory` 服务接口的自定义实现来替代默认的 Json.NET 序列化设置。

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingsFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>序列化和反序列化逻辑

Azure Functions Node 应用程序使用 [`JSON.stringify()` 进行序列化](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)，使用 [`JSON.Parse()` 进行反序列化](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)。 大多数类型都会无缝地进行序列化和反序列化。 在默认逻辑不充分的情况下，在对象上定义 `toJSON()` 方法会劫持序列化逻辑。 但是，对于对象反序列化，不存在模拟。

若要对序列化/反序列化管道进行完全的自定义，请考虑使用你自己的代码来处理序列化和反序列化，并将数据作为字符串进行传递。


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>序列化和反序列化逻辑

强烈建议使用类型注解以确保 Durable Functions 正确地序列化和反序列化数据。 虽然许多内置类型是自动处理的，但是某些内置数据类型需要类型注解才能在反序列化期间保留类型。

对于自定义数据类型，必须通过从类中导出静态 `to_json` 和 `from_json` 方法来定义数据类型的 JSON 序列化和反序列化。

---
