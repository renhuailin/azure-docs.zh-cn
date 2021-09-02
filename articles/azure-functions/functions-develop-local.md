---
title: 在本地开发并运行 Azure Functions
description: 了解如何在本地计算机上编写和测试 Azure 函数，然后再在 Azure Functions 中运行它们。
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 4568cd1befc31cbec30758a3117c30762e7de406
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830853"
---
# <a name="code-and-test-azure-functions-locally"></a>在本地对 Azure Functions 进行编码和测试

尽管你可以在 [Azure 门户]中开发和测试 Azure Functions，但许多开发人员更偏爱本地开发体验。 在 Functions 中，可以轻松使用你偏好的代码编辑器和开发工具在本地计算机上开发和测试函数。 本地函数可以连接到实时 Azure 服务，你可以在本地计算机上使用完整的 Functions 运行时调试函数。

本文提供了指向首选语言的特定开发环境的链接。 它还为本地发展提供了一些共享指导，例如使用 [local.settings.json 文件](#local-settings-file)。 

## <a name="local-development-environments"></a>本地开发环境

在本地计算机开发函数的方式取决于[语言](supported-languages.md)和工具偏好。 下表中的环境支持本地开发：

|环境                              |语言         |说明|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C#（类库）](functions-dotnet-class-library.md)<br/>[C# 隔离进程 (.NET 5.0)](dotnet-isolated-process-guide.md)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](./create-first-function-vs-code-powershell.md)<br/>[Python](functions-reference-python.md) | [适用于 VS Code 的 Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)在 VS Code 中添加了 Functions 支持。 需要 Core Tools。 使用 2.x 版 Core Tools 时，支持 Linux、macOS 和 Windows 上的开发。 若要了解详细信息，请参阅[使用 Visual Studio Code 创建第一个函数](./create-first-function-vs-code-csharp.md)。 |
| [命令提示符或终端](functions-run-local.md) | [C#（类库）](functions-dotnet-class-library.md)<br/>[C# 隔离进程 (.NET 5.0)](dotnet-isolated-process-guide.md)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](functions-reference-powershell.md)<br/>[Python](functions-reference-python.md) | [Azure Functions Core Tools] 提供核心运行时和模板用于创建函数，以实现本地开发。 版本 2.x 支持 Linux、macOS 和 Windows 上的开发。 所有环境依赖于 Core Tools 提供本地 Functions 运行时。 |
| [Visual Studio 2019](functions-develop-vs.md) | [C#（类库）](functions-dotnet-class-library.md)<br/>[C# 隔离进程 (.NET 5.0)](dotnet-isolated-process-guide.md) | Azure Functions 工具包含在 [Visual Studio 2019](https://www.visualstudio.com/vs/) 和更高版本的 **Azure 开发** 工作负荷中。 可以编译类库中的函数，并将 .dll 文件发布到 Azure。 包含用于本地测试的 Core Tools。 有关详细信息，请参阅[使用 Visual Studio 开发 Azure Functions](functions-develop-vs.md)。 |
| [Maven](./create-first-function-cli-java.md)（不同的） | [Java](functions-reference-java.md) | Maven 原型支持 Core Tools，以实现 Java 函数的开发。 版本 2.x 支持 Linux、macOS 和 Windows 上的开发。 有关详细信息，请参阅[使用 Java 和 Maven 创建第一个函数](./create-first-function-cli-java.md)。 还支持使用 [Eclipse](functions-create-maven-eclipse.md) 和 [IntelliJ IDEA](functions-create-maven-intellij.md) 进行开发 |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

其中每个本地开发环境允许创建函数应用项目，并使用预定义的 Functions 模板创建新函数。 每个环境使用 Core Tools，使你能够在自己的计算机上针对实际的 Functions 运行时测试和调试函数，就像对其他任何应用执行此操作一样。 还可以将函数应用项目从其中的任何环境发布到 Azure。

## <a name="local-settings-file"></a>本地设置文件

local.settings.json 文件存储应用设置和本地开发工具使用的设置。 只有在本地运行项目时，才会使用 local.settings.json 文件中的设置。 

> [!IMPORTANT]  
> 因为 local.settings.json 可能包含机密（如连接字符串），因此你绝不应将其存储在远程存储库中。 支持 Functions 的工具提供了将 local.settings.json 文件中的设置与你的项目部署到的函数应用中的[应用设置](functions-how-to-use-azure-function-app-settings.md#settings)同步的方法。

本地设置文件的结构如下：

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

在本地运行项目时，支持这些设置：

| 设置      | 说明                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 当此设置设为 `true` 时，所有值都使用本地计算机密钥进行加密。 与 `func settings` 命令配合使用。 默认值为 `false`。 当本地计算机上的 local.settings.json 文件中包含机密（如服务连接字符串）时，可能需要对其进行加密。 主机在运行时会自动对设置解密。 在尝试读取本地加密设置之前，请使用 `func settings decrypt` 命令。 |
| **`Values`** | 在本地运行项目时所使用的一系列应用程序设置。 这些键值 (string-string) 对与 Azure 的函数应用中的应用程序设置相对应，例如 [`AzureWebJobsStorage`]。 许多触发器和绑定都有一个引用连接字符串应用设置的属性，例如 [Blob 存储触发器](functions-bindings-storage-blob-trigger.md#configuration)的 `Connection`。 对于此类属性，你需要一个在 `Values` 数组中定义的应用程序设置。 在下表中查看常用设置的列表。 <br/>值必须是字符串，而不能是 JSON 对象或数组。 设置名称不能包含冒号 (`:`) 或双下划线 (`__`)。 双下划线字符由运行时保留，并保留冒号以支持[依赖项注入](functions-dotnet-dependency-injection.md#working-with-options-and-settings)。 |
| **`Host`** | 在本地运行项目时，本部分中的设置会自定义 Functions 主机进程。 这些设置独立于 host json 设置，后者在 Azure 中运行项目时也适用。 |
| **`LocalHttpPort`** | 设置运行本地 Functions 主机时使用的默认端口（`func host start` 和 `func run`）。 `--port` 命令行选项优先于此设置。 例如，在 Visual Studio IDE 中运行时，可通过以下方法来更改端口号：导航到“项目属性”->“调试”窗口，并在 `host start --port <your-port-number>` 命令中显式指定可在“应用程序参数”字段中提供的端口号。 |
| **`CORS`** | 定义[跨域资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)可以使用的来源。 以逗号分隔的列表提供来源，其中不含空格。 支持通配符值 (\*)，它允许使用任何来源的请求。 |
| **`CORSCredentials`** |  设置为 `true` 时，允许 `withCredentials` 请求。 |
| **`ConnectionStrings`** | 一个集合。 不要将此集合用于函数绑定使用的连接字符串。 此集合仅供通常从配置文件的 `ConnectionStrings` 节获取连接字符串的框架使用，例如[实体框架](/ef/ef6/)。 此对象中的连接字符串添加到提供者类型为 [System.Data.SqlClient](/dotnet/api/system.data.sqlclient) 的环境中。 此集合中的项不会使用其他应用设置发布到 Azure。 必须将这些值显式添加到函数应用设置的 `Connection strings` 集合中。 如果要在函数代码中创建 [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection)，则应将连接字符串值与其他连接一起存储在门户中的应用程序设置中。 |

在本地运行时，以下应用程序设置可包括在 `Values` 中：

| 设置 | 值 | 说明 |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| 存储帐户连接字符串，或者<br/>`UseDevelopmentStorage=true`| 包括 Azure 存储帐户的连接字符串。 如果使用 HTTP 之外的触发器，则是必需的。 有关详细信息，请查看 [`AzureWebJobsStorage`] 参考。<br/>如果已在本地安装 [Azure 存储仿真器](../storage/common/storage-use-emulator.md)，且已将 [`AzureWebJobsStorage`] 设置为 `UseDevelopmentStorage=true`，则 Core Tools 将使用此仿真器。 此模拟器在开发期间非常有用，但是在部署之前，应当使用实际的存储连接进行测试。| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | 要在本地运行时禁用函数，请向集合添加 `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"`，其中 `<FUNCTION_NAME>` 是函数的名称。 要了解详细信息，请参阅[如何在 Azure Functions 中禁用函数](disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| 指示 Functions 运行时的目标语言。 对于 Functions 运行时版本 2.x 及更高版本来说是必需的。 此设置是 Core Tools 为你的项目生成的。 要了解详细信息，请查看 [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) 参考。|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |指示在本地运行时使用 PowerShell 7。 如果未设置，则使用 PowerShell Core 6。 仅当在本地运行时才使用此设置。 在 Azure 中运行时，PowerShell 运行时版本由 `powerShellVersion` 站点配置设置决定，后者可[在门户中设置](functions-reference-powershell.md#changing-the-powershell-version)。 | 

## <a name="next-steps"></a>后续步骤

+ 若要详细了解如何使用 Visual Studio 2019 在本地开发编译的 C# 函数，请参阅[使用 Visual Studio 开发 Azure Functions](functions-develop-vs.md)。
+ 若要详细了解如何在 Mac、Linux 或 Windows 计算机上使用 VS Code 本地开发函数，请参阅首选语言的 Visual Studio Code 入门文章：
    + [C# 类库](create-first-function-vs-code-csharp.md)
    + [C# 隔离进程 (.NET 5.0)](create-first-function-vs-code-csharp.md?tabs=isolated-process)
    + [Java](create-first-function-vs-code-java.md)
    + [JavaScript](create-first-function-vs-code-node.md)
    + [PowerShell](create-first-function-vs-code-powershell.md)
    + [Python](create-first-function-vs-code-python.md)
    + [TypeScript](create-first-function-vs-code-typescript.md)
+ 若要详细了解如何通过命令提示符或终端开发函数，请参阅[使用 Azure Functions Core Tools](functions-run-local.md)。

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure 门户]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
