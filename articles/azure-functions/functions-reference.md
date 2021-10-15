---
title: Azure Functions 开发指南
description: 了解在 Azure 中开发函数时需要掌握的 Azure Functions 概念和技术，包括各种编程语言和绑定。
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 9/02/2021
ms.openlocfilehash: 49c6fc554eab18ec598db7ec21ef8c15b95d7be9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669638"
---
# <a name="azure-functions-developer-guide"></a>Azure Functions 开发人员指南
在 Azure Functions 中，特定函数共享一些核心技术概念和组件，不受所用语言或绑定限制。 跳转学习某个特定语言或绑定的详细信息之前，请务必通读此通用概述。

本文假定你已阅读 [Azure Functions 概述](functions-overview.md)。

## <a name="function-code"></a>函数代码
函数是 Azure Functions 的基本概念。 函数包含两个重要部分，即可以用各种语言编写的代码，以及一些配置，function.json 文件。 对于编译语言，此配置文件是从代码中的注释自动生成的。 对于脚本语言，必须自己提供配置文件。

Function.json 文件定义函数触发器、绑定和其他配置设置。 每个函数有且只有一个触发器。 运行时使用此配置文件确定要监视的事件，以及如何将数据传入函数执行和从函数执行返回数据。 下面是一个示例 function.json 文件。

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

有关详细信息，请参阅 [Azure Functions 触发器和绑定概念](functions-triggers-bindings.md)。

在 `bindings` 属性配置两个触发器和绑定。 每个绑定共享一些通用设置和一些特定于个别类型的绑定的设置。 每个绑定都需要以下设置：

| 属性    | 值 | 类型 | 注释|
|---|---|---|---|
| type  | 绑定名称。<br><br>例如，`queueTrigger`。 | string | |
| direction | `in`, `out`  | string | 表示绑定是用于接收数据到函数中或是从函数发送数据。 |
| name | 函数标识符。<br><br>例如，`myQueue`。 | string | 将用于函数中绑定数据的名称。 对于 C#，它将是参数名称；对于 JavaScript，它是键/值列表中的键。 |

## <a name="function-app"></a>函数应用
函数应用在 Azure 中提供用于运行函数的执行上下文。 因此，它是函数的部署和管理单元。 函数应用由一个或多个共同管理、部署和缩放的独立函数组成。 函数应用中的所有函数共享相同的定价计划、部署方法和运行时版本。 将函数应用视为组织和共同管理函数的一种方法。 若要了解详细信息，请参阅[如何管理函数应用](functions-how-to-use-azure-function-app-settings.md)。 

> [!NOTE]
> 函数应用中的所有函数必须使用相同的语言编写。 在 Azure Functions 运行时的[先前版本](functions-versions.md)中，这不是必需的。

## <a name="folder-structure"></a>文件夹结构
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

以上是 Function app 的默认（和推荐）文件夹结构。 如果要更改函数代码的文件位置，请修改 function.json 文件的 `scriptFile` 部分。 我们还建议使用[包部署](deployment-zip-push.md)将项目部署到 Azure 中的函数应用。 也可以使用现有工具，比如[持续集成和部署](functions-continuous-deployment.md)以及 Azure DevOps。

> [!NOTE]
> 如果手动部署包，请确保将 host.json 文件和函数文件夹直接部署到 `wwwroot` 文件夹。 请勿在部署中包含 `wwwroot` 文件夹。 否则，最后将得到 `wwwroot\wwwroot` 文件夹。

#### <a name="use-local-tools-and-publishing"></a>使用本地工具和发布
可以使用各种工具创作和发布各种函数应用，包括 [Visual Studio](./functions-develop-vs.md)、[Visual Studio Code](./create-first-function-vs-code-csharp.md)、[IntelliJ](./functions-create-maven-intellij.md)、[Eclipse](./functions-create-maven-eclipse.md) 和 [Azure Functions Core Tools](./functions-develop-local.md)。 有关详细信息，请参阅[在本地对 Azure Functions 进行编码和测试](./functions-develop-local.md)。

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> 如何编辑 Azure 门户中的函数
通过 Azure 门户中内置的函数编辑器可直接内联更新代码和 function.json 文件。 建议仅用于小的更改或概念证明 - 最佳做法是使用 VS Code 等本地开发工具。

## <a name="parallel-execution"></a>并行执行
多个触发事件发生的速度超过了单线程函数运行的处理速度时，运行时可并行多次调用函数。  如果 Function App 正在使用[消耗量托管计划](event-driven-scaling.md)，则 Function App 可自动扩大。  无论应用是在消耗量托管计划还是常规[应用服务托管计划](../app-service/overview-hosting-plans.md)上运行，每个 Function App 实例都可能使用多个线程并行处理并发函数调用。  每个 Function App 实例中并发函数的最大调用数根据所用触发器类型以及 Function App 中其他函数所用资源而有所不同。

## <a name="functions-runtime-versioning"></a>Functions 运行时版本控制

可使用 `FUNCTIONS_EXTENSION_VERSION` 应用设置配置 Functions 运行时的版本。 例如，值“~3”表示函数应用将使用 3.x 作为其主版本。 函数应用在发布后，将升级到各个新的次要版本。 有关详细信息（包括如何查看函数应用的确切版本），请参阅[如何针对 Azure Functions 运行时版本](set-runtime-version.md)。

## <a name="repositories"></a>存储库
Azure Functions 代码为开放源，位于 GitHub 存储库：

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions 主机](https://github.com/Azure/azure-functions-host/)
* [Azure Functions 门户](https://github.com/azure/azure-functions-ux)
* [Azure Functions 模板](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 扩展](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>绑定
下面是所有受支持的绑定表。

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

对来自绑定的错误怀有疑问？ 请查看 [Azure Functions 绑定错误代码](functions-bindings-error-pages.md)文档。


## <a name="connections"></a>连接

函数项目从其配置提供程序按名称引用连接信息。 它不直接接受连接详细信息，而是允许跨环境对其进行更改。 例如，触发器定义可能包括 `connection` 属性。 这可能是指连接字符串，但不能直接在 `function.json` 中设置连接字符串。 相反，应将 `connection` 设置为包含连接字符串的环境变量的名称。

默认配置提供程序使用环境变量。 在 Azure Functions 服务中运行时，可以通过[应用程序设置](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings)进行设置，而在本地开发时，可以通过[本地设置文件](functions-develop-local.md#local-settings-file)进行设置。

### <a name="connection-values"></a>连接值

当连接名称解析为单个精确值时，运行时会将值标识为通常包含机密的连接字符串。 连接字符串的详细信息由你要连接到的服务定义。

不过，连接名称还可以引用多个配置项目的集合。 可以通过使用以双下划线 `__` 结尾的共享前缀将环境变量视为集合。 然后，可以通过将连接名称设置为此前缀来引用该组。

例如，Azure Blob 触发器定义的 `connection` 属性可能是 `Storage1`。 只要没有名称配置为 `Storage1` 的单个字符串值，`Storage1__serviceUri` 就会用于连接的 `serviceUri` 属性。 每个服务的连接属性各不相同。 请参阅相关文档，了解使用连接的扩展。

### <a name="configure-an-identity-based-connection"></a>配置基于标识的连接

Azure Functions 中的某些连接配置为使用标识而不是机密。 支持取决于使用连接的扩展。 在某些情况下，即使连接到的服务支持基于标识的连接，Functions 中仍可能需要连接字符串。

以下触发器和绑定扩展支持基于标识的连接：

> [!NOTE]
> Durable Functions 不支持基于标识的连接。

| 扩展名称 | 扩展版本                                                                                     | 支持的计划     |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------|
| Azure Blob     | [版本 5.0.0-beta1 或更高版本](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | 全部                 |
| Azure 队列    | [版本 5.0.0-beta1 或更高版本](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | 全部                 |
| Azure 事件中心    | [版本 5.0.0-beta1 或更高版本](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) | 全部            |
| Azure 服务总线    | [版本 5.0.0-beta2 或更高版本](./functions-bindings-service-bus.md#service-bus-extension-5x-and-higher) | 全部         |
| Azure Cosmos DB   | [版本 4.0.0-预览版 1 或更高版本](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) | 弹性高级 |


还可以使用基于标识的连接来配置 Functions 运行时 (`AzureWebJobsStorage`) 所用的存储连接。 请参阅下面的[使用标识连接到主机存储](#connecting-to-host-storage-with-an-identity)。

在 Azure Functions 服务中托管时，基于标识的连接将使用[托管标识](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json)。 默认情况下使用系统分配的标识，但可以使用 `credential` 和 `clientID` 属性来指定用户分配的标识。 在其他上下文（如本地开发）中运行时，将改用开发人员标识，尽管可以使用其他连接参数对其进行自定义。

#### <a name="grant-permission-to-the-identity"></a>向标识授予权限

无论使用何种标识，都必须具有执行所需操作的权限。 这通常是通过在 Azure RBAC 中分配角色或在访问策略中指定标识来完成的，具体取决于要连接到的服务。 请参阅每个扩展的文档，了解需要哪些权限以及如何设置这些权限。

> [!IMPORTANT]
> 某些权限可能由并非所有上下文都需要的目标服务公开。 尽可能遵循最低权限原则，仅授予标识所需的权限。 例如，如果应用只需从 Blob 读取数据，请使用[存储 Blob 数据读取者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色，因为[存储 Blob 数据所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)包含过多的读取操作权限。
以下角色涵盖正常操作中每个扩展所需的主要权限：

| 服务     | 内置角色示例 |
|-------------|------------------------|
| Azure Blob  | [存储 Blob 数据读取器](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)、[存储 Blob 数据所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)                 |
| Azure 队列 | [存储队列数据读取器](../role-based-access-control/built-in-roles.md#storage-queue-data-reader)、[存储队列数据消息处理器](../role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)、[存储队列数据消息发送方](../role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)、[存储队列数据参与者](../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)             |
| 事件中心   |    [Azure 事件中心数据接收方](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)、[Azure 事件中心数据发送方](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)、[Azure 事件中心数据所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)              |
| 服务总线 | [Azure 服务总线数据接收方](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)、[Azure 服务总线数据发送方](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)、[Azure 服务总线数据所有者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) |
| Azure Cosmos DB | [Cosmos DB 内置数据读者](../cosmos-db/how-to-setup-rbac.md#built-in-role-definitions)、[Cosmos DB 内置数据参与者](../cosmos-db/how-to-setup-rbac.md#built-in-role-definitions) |

#### <a name="connection-properties"></a>连接属性

Azure 服务的基于标识的连接接受以下属性，其中 `<CONNECTION_NAME_PREFIX>` 是触发器或绑定定义中的 `connection` 属性值：

| 属性    | 扩展所需 | 环境变量 | 说明 |
|---|---|---|---|
| 服务 URI | Azure Blob<sup>1</sup>、Azure 队列 | `<CONNECTION_NAME_PREFIX>__serviceUri` | 要连接到的服务的数据平面 URI。 |
| 完全限定的命名空间 | 事件中心、服务总线 | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | 完全限定的事件中心和服务总线命名空间。 |
| 帐户终结点 | Azure Cosmos DB | `<CONNECTION_NAME_PREFIX>__accountEndpoint` | Azure Cosmos DB 帐户终结点 URI。 |
| 令牌凭据 | (可选) | `<CONNECTION_NAME_PREFIX>__credential` | 定义如何为连接获取令牌。 建议仅在指定用户分配的标识时使用，此时它应设置为“managedidentity”。 仅当在 Azure Functions 服务中托管时，此属性才有效。 |
| 客户端 ID | (可选) | `<CONNECTION_NAME_PREFIX>__clientId` | 当 `credential` 设置为“managedidentity”时，此属性指定在获取令牌时要使用的用户分配的标识。 该属性接受与分配给应用程序的用户分配的标识对应的客户端 ID。 如果未指定，则将使用系统分配的标识。 在[本地开发方案](#local-development-with-identity-based-connections)中使用时，此属性的使用方式不同，且不应设置 `credential`。 |

<sup>1</sup> Azure Blob 需要 Blob URI 和队列服务 URI。

给定的连接类型可能支持其他选项。 请参阅相关文档，了解用于建立连接的组件。

##### <a name="local-development-with-identity-based-connections"></a>使用基于标识的连接进行本地开发

在本地运行时，上述配置会告知运行时使用本地开发人员标识。 连接将尝试从以下位置获取令牌，顺序如下：

- Microsoft 应用程序之间共享的本地缓存
- Visual Studio 中的当前用户上下文
- Visual Studio Code 中的当前用户上下文
- Azure CLI 中的当前用户上下文

如果这些选项都不成功，则会出现错误。

在某些情况下，你可能希望指定使用其他标识。 可以添加指向其他标识的连接的配置属性。

> [!NOTE]
> 在 Azure Functions 服务中托管时，不支持以下配置选项。

若要在 Azure Active Directory 服务主体中使用客户端 ID 和机密进行连接，除了上面的[连接属性](#connection-properties)之外，还需要使用以下属性来定义连接：

| 属性    | 环境变量 | 说明 |
|---|---|---|
| 租户 ID | `<CONNECTION_NAME_PREFIX>__tenantId` | Azure Active Directory 租户（目录）ID。 |
| 客户端 ID | `<CONNECTION_NAME_PREFIX>__clientId` |  租户中应用注册的客户端（应用程序）ID。 |
| 客户端机密 | `<CONNECTION_NAME_PREFIX>__clientSecret` | 为应用注册生成的客户端密码。 |

与 Azure Blob 进行的基于标识的连接所需的 `local.settings.json` 属性的示例： 

```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__serviceUri": "<serviceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

与 Azure Cosmos DB 基于标识的连接所需的 `local.settings.json` 属性示例： 

```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__accountEndpoint": "<accountEndpoint>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="connecting-to-host-storage-with-an-identity"></a>使用标识连接到主机存储

默认情况下，Azure Functions 使用 `AzureWebJobsStorage` 连接来实现核心行为，例如协调计时器触发器和默认应用密钥存储的单一实例执行。 也可以将此连接配置为使用标识。

> [!CAUTION]
> 某些应用在其触发器、绑定和/或函数代码中重复使用 `AzureWebJobsStorage` 进行存储连接。 在从连接字符串更改此连接之前，请确保全部所用的 `AzureWebJobsStorage` 都能够使用基于标识的连接格式。

若要以此方式配置连接，请确保应用的标识具有[存储 Blob 数据所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色，以支持核心主机功能。 如果出于任何其他目的使用“AzureWebJobsStorage”，可能需要更多的权限。

如果所用的存储帐户使用全球 Azure 的默认 DNS 后缀和服务名称（遵循 `https://<accountName>.blob/queue/file/table.core.windows.net` 格式），则你可以将 `AzureWebJobsStorage__accountName` 设置为你的存储帐户名称。 

如果改用主权云中的或者采用自定义 DNS 的存储帐户，请将 `AzureWebJobsStorage__serviceUri` 设置为你的 Blob 服务的 URI。 如果要对任何其他服务使用“AzureWebJobsStorage”，则可以改为分别指定 `AzureWebJobsStorage__blobServiceUri`、`AzureWebJobsStorage__queueServiceUri` 和 `AzureWebJobsStorage__tableServiceUri`。

## <a name="reporting-issues"></a>报告问题
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* [在本地对 Azure Functions 进行编码和测试](./functions-develop-local.md)
* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions C# 开发人员参考](functions-dotnet-class-library.md)
* [Azure Functions Node.js 开发人员参考](functions-reference-node.md)
