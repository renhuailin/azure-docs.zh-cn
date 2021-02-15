---
title: Durable Functions 中的数据持久性和序列化
description: 了解 Azure Functions 的 Durable Functions 扩展如何保持数据
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 34845afe0efd0131e0c568eac1169ddc10be5d47
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520902"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions 中的数据持久性和序列化) 

Durable Functions 会自动将函数参数、返回值和其他状态保留到持久后端，以便提供可靠的执行。 但是，保存到持久存储的数据量和频率可能会影响应用程序性能和存储事务成本。 根据应用程序存储的数据类型，可能还需要考虑数据保留和隐私策略。

## <a name="azure-storage"></a>Azure 存储

默认情况下，Durable Functions 将数据保存到你指定的 [Azure 存储](https://azure.microsoft.com/services/storage/) 帐户中的队列、表和 blob。

### <a name="queues"></a>队列

Durable Functions 使用 Azure 存储队列来可靠地计划所有函数执行。 这些队列消息包含函数输入或输出，具体取决于消息是用于计划执行还是将值返回回调用函数。 这些队列消息还包括 Durable Functions 用于内部用途的其他元数据，如路由和端到端相关。 在为响应收到的消息完成执行某个函数后，该消息将被删除，并且执行的结果也可以保存到 Azure 存储表或 Azure 存储 Blob。

在单个 [任务中心](durable-functions-task-hubs.md)内 Durable Functions 创建消息并将其添加到名为的 *工作项* 队列， `<taskhub>-workitem` 用于计划活动函数以及一个或多个名为的 *控制队列* `<taskhub>-control-##` 来计划或恢复 orchestrator 和实体函数。 控制队列的数目等于为应用程序配置的分区数。 有关队列和分区的详细信息，请参阅 [性能和可扩展性文档](durable-functions-perf-and-scale.md)。

### <a name="tables"></a>表

业务流程成功处理消息后，其生成操作的记录将保留到名为的 *历史记录* 表 `<taskhub>History` 。 业务流程输入、输出和自定义状态数据也将保存到名为的 *实例* 表中 `<taskhub>Instances` 。

### <a name="blobs"></a>Blob

在大多数情况下，Durable Functions 不会使用 Azure 存储 Blob 来保存数据。 但是，队列和表的 [大小限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) 会阻止 Durable Functions 将所有所需数据保存到存储行或队列消息中。 例如，在序列化时需要保留到队列中的一段数据大于 45 KB 时，Durable Functions 将压缩数据并将其存储在 blob 中。 以这种方式将数据持久保存到 blob 存储时，持久性函数在表行或队列消息中存储对 blob 的引用。 当 Durable Functions 需要检索数据时，它会自动从 blob 中提取数据。 这些 blob 存储在 blob 容器中 `<taskhub>-largemessages` 。

> [!NOTE]
> 对于较大的消息，额外的压缩和 blob 操作步骤可能会消耗大量 CPU 和 i/o 延迟成本。 此外，Durable Functions 需要在内存中加载保留的数据，并且可以同时执行许多不同的函数执行。 因此，保留大数据负载也可能导致高内存使用率。 若要最大程度地减少内存开销，请考虑手动保留大数据负载 (例如，在 blob 存储) 中，并传递对此数据的引用。 这样，你的代码就可以仅在需要时才加载数据，以避免在 [orchestrator 功能重播](durable-functions-orchestrations.md#reliability)期间出现冗余负载。 但是， *不* 建议将有效负载存储到磁盘，因为在其整个生存期内，这些函数可能会在不同的 vm 上执行。

### <a name="types-of-data-that-is-serialized-and-persisted"></a>序列化和持久化的数据类型
下面列出了使用 Durable Functions 的功能时将序列化并保存的不同类型的数据：

- Orchestrator、活动和实体函数的所有输入和输出，包括任何 Id 和未处理的异常
- Orchestrator、活动和实体函数名称
- 外部事件名称和有效负载
- 自定义业务流程状态负载
- 业务流程终止消息
- 持久计时器有效负载
- 持久 HTTP 请求和响应 Url、标头和负载
- 实体调用和信号负载
- 实体状态负载

### <a name="working-with-sensitive-data"></a>处理敏感数据
使用 Azure 存储时，会自动加密静态数据。 但是，有权访问存储帐户的任何人都可以使用未加密的格式读取数据。 如果需要对敏感数据进行更强的保护，请考虑使用自己的加密密钥对数据进行加密，以便 Durable Functions 将数据保留为预加密形式。

或者，.NET 用户可以选择实现提供自动加密的自定义序列化提供程序。 有关使用加密的自定义序列化的示例，请参阅 [此 GitHub 示例](https://github.com/charleszipp/azure-durable-entities-encryption)。

> [!NOTE]
> 如果决定要实现应用程序级别的加密，请注意，业务流程和实体可能存在无限的时间。 这就是在需要轮换加密密钥时，因为一个业务流程或实体运行的时间可能比密钥轮换策略要长。 如果发生密钥轮替，则用于对数据进行加密的密钥可能不再可用于在下一次执行业务流程或实体时对数据进行解密。 因此，仅当应在相对较短的时间内运行业务流程和实体时，才建议客户加密。

## <a name="customizing-serialization-and-deserialization"></a>自定义序列化和反序列化

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>默认序列化逻辑

Durable Functions 在内部使用 [Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) 将业务流程和实体数据序列化为 Json。 Json.NET 使用的默认设置 Durable Functions 为：

**输入、输出和状态：**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**不同**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

阅读更多有关 `JsonSerializerSettings` [此](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm)内容的详细文档。

## <a name="customizing-serialization-with-net-attributes"></a>自定义 .NET 特性的序列化

序列化数据时，Json.NET 将在类和属性上查找 [各种属性](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) ，这些属性控制如何序列化和反序列化 Json 中的数据。 如果你拥有传递到 Durable Functions Api 的数据类型的源代码，请考虑将这些特性添加到类型以自定义序列化和反序列化。

## <a name="customizing-serialization-with-dependency-injection"></a>自定义具有依赖关系注入的序列化

面向 .NET 并在函数 V3 运行时运行的函数应用可以使用 [依赖关系注入 (DI) ](../functions-dotnet-dependency-injection.md) 自定义如何序列化数据和异常。 下面的示例代码演示了如何使用 DI 通过 `IMessageSerializerSettingsFactory` 和服务接口的自定义实现来重写默认 Json.NET 序列化设置 `IErrorSerializerSettingsFactory` 。

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
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingFactory>();
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

Azure Functions 节点应用程序用于[ `JSON.stringify()` 序列化](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)和[ `JSON.Parse()` 反序列](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)化。 大多数类型都应进行无缝序列化和反序列化。 在默认逻辑不足的情况下，在 `toJSON()` 对象上定义方法将劫持序列化逻辑。 但对于对象反序列化，不存在模拟。

若要对序列化/反序列化管道进行完全自定义，请考虑处理序列化和反序列化和你自己的代码，并将数据作为字符串进行传递。


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>序列化和反序列化逻辑

强烈建议使用类型批注以确保 Durable Functions 正确地序列化和反序列化数据。 尽管许多内置类型会自动处理，但某些内置数据类型需要类型批注才能在反序列化过程中保留类型。

对于自定义数据类型，必须通过从类导出静态和方法来定义数据类型的 JSON 序列化和反序列化 `to_json` `from_json` 。

---
