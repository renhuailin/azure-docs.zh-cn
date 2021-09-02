---
title: Azure Functions Core Tools 参考文档
description: 支持 Azure Functions Core Tools (func.exe) 的参考文档。
ms.topic: reference
ms.date: 07/13/2021
ms.openlocfilehash: 90d4a9575c2fe6bb2dc4fbd18132e3bc21e4a07c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779825"
---
# <a name="azure-functions-core-tools-reference"></a>Azure Functions Core Tools 参考文档

本文提供 Azure Functions Core Tools 的参考文档，让你可以从本地计算机开发、管理和部署 Azure Functions 项目。 若要详细了解 Core Tools 的用法，请参阅[使用 Azure Functions Core Tools](functions-run-local.md)。 

Core Tools 的命令分为以下上下文，每个上下文都提供一组唯一的操作。

| 命令上下文 | 说明 |
| ----- | ----- |
| [`func`](#func-init) | 用于在本地计算机上创建和运行函数的命令。 |
| [`func azure`](#func-azure-functionapp-fetch-app-settings) | 用于处理 Azure 资源的命令，包括发布。 |
| [`func durable`](#func-durable-delete-task-hub)    | 用于处理 [Durable Functions](./durable/durable-functions-overview.md) 的命令。 |
| [`func extensions`](#func-extensions-install) | 用于安装和管理扩展的命令。 |
| [`func kubernetes`](#func-kubernetes-deploy) | 用于处理 Kubernetes 和 Azure Functions 的命令。 |
| [`func settings`](#func-settings-decrypt)   | 用于管理本地 Functions 主机的环境设置的命令。 |
| [`func templates`](#func-templates-list)  | 用于列出可用函数模板的命令。 |

使用本文中所列命令之前，必须[安装 Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)。 

## <a name="func-init"></a>func init 

使用特定的语言创建新的 Functions 项目。

```command
func init <PROJECT_FOLDER>
```

提供 `<PROJECT_FOLDER>` 时，系统将在使用此名称的新文件夹中创建项目。 否则，将使用当前文件夹。

`func init` 支持以下选项。除非另有说明，否则这些选项仅限版本 3.x/2.x：

| 选项     | 描述                            |
| ------------ | -------------------------------------- |
| **`--csx`** | 以 C# 脚本的形式创建 .NET 函数，这是版本 1.x 行为。 仅对 `--worker-runtime dotnet` 有效。 |
| **`--docker`** | 使用基于所选 `--worker-runtime` 的基础映像创建容器的 Dockerfile。 如果打算发布到自定义 Linux 容器，请使用此选项。 |
| **`--docker-only`** |  将 Dockerfile 添加到现有项目中。 如果未在 local.settings.json 中指定或设置 worker-runtime，则会进行相应提示。 如果你打算将现有项目发布到自定义 Linux 容器，请使用此选项。 |
| **`--force`** | 即使项目中存在现有的文件，也要初始化该项目。 此设置会覆盖同名的现有文件。 项目文件夹中的其他文件不受影响。 |
| **`--language`** | 初始化特定于语言的项目。 当前在 `--worker-runtime` 设为 `node` 时受支持。 选项包括 `typescript` 和 `javascript`。 你也可使用 `--worker-runtime javascript` 或 `--worker-runtime typescript`。  |
| **`--managed-dependencies`**  | 安装托管的依赖项。 目前只有 PowerShell 辅助运行时支持此功能。 |
| **`--source-control`** | 控制是否创建 git 存储库。 默认不会创建存储库。 如果为 `true`，则会创建存储库。 |
| **`--worker-runtime`** | 设置项目的语言运行时。 支持的值为：`csharp`、`dotnet`、`dotnet-isolated`、`javascript`、`node` (JavaScript)、`powershell`、`python` 和 `typescript`。 对于 Java，请使用 [Maven](functions-reference-java.md#create-java-functions)。 若要生成仅包含项目文件的与语言无关的项目，请使用 `custom`。 如果未设置，则初始化期间系统会提示你选择运行时。 |
|

> [!NOTE]
> 使用 `--docker` 或 `--dockerfile` 选项时，Core Tools 会自动为 C#、JavaScript、Python 和 PowerShell 函数创建 Dockerfile。 对于 Java 函数，必须手动创建 Dockerfile。 使用 Azure Functions [映像列表](https://github.com/Azure/azure-functions-docker)可为运行 Azure Functions 的容器查找正确的基础映像。

## <a name="func-logs"></a>func logs

获取在 Kubernetes 群集中运行的函数的日志。

```
func logs --platform kubernetes --name <APP_NAME>
``` 

`func logs` 操作支持以下选项：

| 选项     | 说明                            |
| ------------------------------------------ | -------------------------------------- |
| **`--platform`** | 函数应用的托管平台。 支持的选项：`kubernetes`。 |
| **`--name`** | Azure 中的函数应用名称。 |

若要了解详细信息，请参阅[在搭配 KEDA 的 Kubernetes 上使用 Azure Functions](functions-kubernetes-keda.md)。

## <a name="func-new"></a>func new

根据模板在当前项目中创建新函数。

```
func new
``` 

`func new` 操作支持以下选项：

| 选项     | 说明                            |
| ------------------------------------------ | -------------------------------------- |
| **`--authLevel`** | 允许你设置 HTTP 触发器的授权级别。 支持的值为：`function`、`anonymous`、`admin`。 在本地运行时不强制执行授权。 有关详细信息，请参阅 [HTTP 绑定](functions-bindings-http-webhook-trigger.md#authorization-keys)一文。 |
| **`--csx`** | （2.x 及更高版本。）生成版本 1.x 和门户所用的相同 C# 脚本 (.csx) 模板。 |
| **`--language`**, **`-l`**| C#、F# 或 JavaScript 等模板编程语言。 此选项在版本 1.x 中是必需的。 在 2.x 和更高版本中，不会使用此选项，因为该语言是由辅助运行时定义。 |
| **`--name`**, **`-n`** | 函数名称。 |
| **`--template`**, **`-t`** | 使用 `func templates list` 命令查看每种受支持语言的可用模板的完整列表。   |

若要了解更多信息，请参阅[创建函数](functions-run-local.md#create-func)。

## <a name="func-run"></a>func run

*仅限 1.x 版。*

此命令类似于在 Azure 门户中使用“测试”选项卡运行函数，你可以使用此命令直接调用函数。 仅在 1.x 版中支持此操作。 对于更高版本，请使用 `func start` 并[直接调用函数终结点](functions-run-local.md#passing-test-data-to-a-function)。

```command
func run
```

`func run` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--content`** | 传递给函数的内联内容。 |
| **`--debug`** | 运行函数前，将调试程序附加到主机进程。|
| **`--file`** | 要用作内容的文件名。|
| **`--no-interactive`** | 不提示输入，这对于自动化方案非常有用。|
| **`--timeout`** | 本地 Functions 主机准备就绪前的等待时间（以秒为单位）。|

例如，若要调用 HTTP 触发的函数并传递内容正文，请运行以下命令：

```
func run MyHttpTrigger --content '{\"name\": \"Azure\"}'
```

## <a name="func-start"></a>func start

启动本地运行时主机，并将函数项目加载到当前文件夹中。 

具体的命令取决于[运行时版本](functions-versions.md)。   

# <a name="v2x"></a>[v2.x+](#tab/v2)

```command
func start
```

`func start` 支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--cert`** | 包含私钥的 .pfx 文件的路径。 仅支持使用 `--useHttps`。 |
| **`--cors`** | 以逗号分隔的 CORS 来源列表，其中不包含空格。 |
| **`--cors-credentials`** | 允许使用 cookie 和身份验证标头的经验证的交叉原点请求。 |
| **`--dotnet-isolated-debug`** | 设置为 `true` 时，将暂停 .NET 工作进程，直到从正在调试的 .NET 独立项目附加调试程序。 |
| **`--enable-json-output`** | 尽可能以 JSON 格式发出控制台日志。 |
| **`--enableAuth`** | 启用完整的身份验证处理管道。 |
| **`--functions`** | 要加载的以空格分隔的函数列表。 |
| **`--language-worker`** | 用于配置语言辅助角色的参数。 例如，可以通过提供[调试端口和其他所需参数](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers)，为语言辅助角色启用调试。 |
| **`--no-build`** | 请勿在运行之前生成当前项目。 仅适用于 .NET 类项目。 默认为 `false`。  |
| **`--password`** | 密码或包含 .pfx 文件密码的文件。 只能与 `--cert` 配合使用。 |
| **`--port`** | 要侦听的本地端口。 默认值：7071。 |
| **`--timeout`** | Functions 主机启动的超时时间（以秒为单位）。 默认值：20 秒。|
| **`--useHttps`** | 绑定到 `https://localhost:{port}` ，而不是绑定到 `http://localhost:{port}` 。 默认情况下，此选项会在计算机上创建可信证书。|

在项目开始运行后，你可以[验证各个函数终结点](functions-run-local.md#passing-test-data-to-a-function)。

# <a name="v1x"></a>[v1.x](#tab/v1)

```command
func host start
```

`func start` 支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--cors`** | 以逗号分隔的 CORS 来源列表，其中不包含空格。 |
| **`--port`** | 要侦听的本地端口。 默认值：7071。 |
| **`--pause-on-error`** | 退出进程前，暂停增加其他输入。 仅当从集成开发环境 (IDE) 启动 Core Tools 时才使用。|
| **`--script-root`** | 用于指定要运行或部署的函数应用的根目录路径。 此选项用于可在子文件夹中生成项目文件的已编译项目。 例如，生成 C# 类库项目时，将在某个根子文件夹中生成 host.json、local.settings.json 和 function.json 文件，其路径类似于 `MyProject/bin/Debug/netstandard2.0`。 在这种情况下，请将前缀设置为 `--script-root MyProject/bin/Debug/netstandard2.0`。 这是在 Azure 中运行的函数应用的根目录。 |
| **`--timeout`** | Functions 主机启动的超时时间（以秒为单位）。 默认值：20 秒。|
| **`--useHttps`** | 绑定到 `https://localhost:{port}` ，而不是绑定到 `http://localhost:{port}` 。 默认情况下，此选项会在计算机上创建可信证书。|

在 1.x 版本中，还可使用 [`func run` 命令](#func-run)运行特定函数，并向其传递测试数据。 

---

## <a name="func-azure-functionapp-fetch-app-settings"></a>func azure functionapp fetch-app-settings

从特定的函数应用获取设置。

```command
func azure functionapp fetch-app-settings <APP_NAME>
```

若需示例，请参阅[获取存储连接字符串](functions-run-local.md#get-your-storage-connection-strings)。

将设置下载到项目的 local.settings.json 文件中。 出于安全，系统会对屏幕值进行掩码。 你可以通过[启用本地加密](#func-settings-encrypt)来保护 local.settings.json 文件中的设置。 

## <a name="func-azure-functionapp-list-functions"></a>func azure functionapp list-functions

返回指定函数应用中的函数列表。

```command
func azure functionapp list-functions <APP_NAME>
```
## <a name="func-azure-functionapp-logstream"></a>func azure functionapp logstream

将本地命令提示符连接到 Azure 中函数应用的流式处理日志。

```command
func azure functionapp logstream <APP_NAME>
```

连接的默认超时为 2 小时。 可以通过添加名为 [SCM_LOGSTREAM_TIMEOUT](functions-app-settings.md#scm_logstream_timeout) 的应用设置来更改超时，超时值以秒表示。 消耗计划中的 Linux 应用尚不支持此操作。 对于这些应用，请使用 `--browser` 选项在门户中查看日志。

`deploy` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--browser`** | 在默认浏览器中打开函数应用的 Azure Application Insights 实时流。 |

若要了解有关详细信息，请参阅[启用流式处理日志](functions-run-local.md#enable-streaming-logs)。

## <a name="func-azure-functionapp-publish"></a>func azure functionapp publish 

将 Functions 项目部署到 Azure 中现有的函数应用资源。 

```command
func azure functionapp publish <FunctionAppName>
```

有关详细信息，请参阅[部署项目文件](functions-run-local.md#project-file-deployment)。

根据版本应用以下发布选项：

# <a name="v2x"></a>[v2.x+](#tab/v2)

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--additional-packages`** | 构建本机依赖项时要安装的包列表。 例如：`python3-dev libevent-dev`。 |
| **`--build`**, **`-b`** | 部署到 Linux 函数应用时执行生成操作。 接受：`remote` 和 `local`。 |
| **`--build-native-deps`** | 发布 Python 函数应用时跳过生成 `.wheels` 文件夹。 |
| **`--csx`** | 发布 C# 脚本 (.csx) 项目。 |
| **`--force`** | 在某些情况下会忽略预发布验证。 |
| **`--dotnet-cli-params`** | 发布编译的 C# (.csproj) 函数时，Core Tools 调用 `dotnet build --output bin/publish`。 传递到此选项的任何参数将追加到命令行。 |
|**`--list-ignored-files`** | 基于 `.funcignore` 文件显示发布期间忽略的文件列表。 |
| **`--list-included-files`** | 基于 `.funcignore` 文件显示发布的文件列表。 |
| **`--no-build`** | 发布过程中不生成项目。 对于 Python，不执行 `pip install`。 |
| **`--nozip`** | 关闭默认的 `Run-From-Package` 模式。 |
| **`--overwrite-settings -y`** | 使用 `--publish-local-settings -i` 时隐藏覆盖应用设置的提示。|
| **`--publish-local-settings -i`** |  将 local.settings.json 中的设置发布到 Azure，如果该设置已存在，则提示进行覆盖。 如果使用的是 Microsoft Azure 存储模拟器，请先将应用设置更改为[实际的存储连接](functions-run-local.md#get-your-storage-connection-strings)。 |
| **`--publish-settings-only`**, **`-o`** |  仅发布设置，并跳过内容。 默认为提示。 |
| **`--slot`** | 发布所指向的特定插槽的可选名称。 |

# <a name="v1x"></a>[v1.x](#tab/v1)

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--overwrite-settings -y`** | 使用 `--publish-local-settings -i` 时隐藏覆盖应用设置的提示。|
| **`--publish-local-settings -i`** |  将 local.settings.json 中的设置发布到 Azure，如果该设置已存在，则提示进行覆盖。 如果使用的是 Microsoft Azure 存储模拟器，请先将应用设置更改为[实际的存储连接](functions-run-local.md#get-your-storage-connection-strings)。 |

---

## <a name="func-azure-storage-fetch-connection-string"></a>func azure storage fetch-connection-string    

获取指定 Azure 存储帐户的连接字符串。

```command
func azure storage fetch-connection-string <STORAGE_ACCOUNT_NAME>
```

## <a name="func-deploy"></a>func deploy

将自定义 Linux 容器中的函数应用部署到不使用 KEDA 的 Kubernetes 群集。

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <DOCKER_USER>
```

此命令会将项目生成为自定义容器，然后使用默认缩放程序或 KNative 将其发布到 Kubernetes 群集。 若要发布到使用 KEDA 进行动态缩放的群集，请改为使用 [`func kubernetes deploy` 命令](#func-kubernetes-deploy)。 自定义容器必须有一个 Dockerfile。 若要使用 Dockerfile 创建应用，请使用 `--dockerfile` 选项和 [`func init`命令](#func-init)。     

`deploy` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--config`** | 设置可选的部署配置文件。 |
| **`--max`**  | （可选）设置要部署到的最大函数应用实例数。 |
| **`--min`**  | （可选）设置要部署到的最小函数应用实例数。 |
| **`--name`** | 函数应用的名称（必选）。 |
| **`--platform`** | 函数应用的托管平台（必选）。 有效选项为 `kubernetes` 和 `knative`。|
| **`--registry`** | 当前用户登录到的 Docker 注册表的名称（必选）。 |

Core Tools 将使用本地 Docker CLI 来生成并发布映像。 

请确保已在本地安装 Docker。 运行 `docker login` 命令以连接到帐户。

## <a name="func-durable-delete-task-hub"></a>func durable delete-task-hub

删除 Durable Functions 任务中心内所有的存储项目。

```command
func durable delete-task-hub
```

`delete-task-hub` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 要使用的包含存储连接字符串的设置名称（可选）。 |
| **`--task-hub-name`** |             要使用的 Durable 任务中心的名称（可选）。 |

若要了解详细信息，请参阅 [Durable Functions 文档](./durable/durable-functions-instance-management.md#delete-a-task-hub)。

## <a name="func-durable-get-history"></a>func durable get-history

返回指定业务流程实例的历史记录。

```command
func durable get-history --id <INSTANCE_ID>
```

`get-history` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--id`** | 指定业务流程实例的 ID（必选）。 |
| **`--connection-string-setting`** | 要使用的包含存储连接字符串的设置名称（可选）。 |
| **`--task-hub-name`** |             要使用的 Durable 任务中心的名称（可选）。 |

若要了解详细信息，请参阅 [Durable Functions 文档](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1)。

## <a name="func-durable-get-instances"></a>func durable get-instances

返回所有业务流程实例的状态。 支持使用 `top` 参数分页。

```command
func durable get-instances
```

`get-instances` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--continuation-token`** | 指示要返回的请求的特定页/节的可选标记。 |
| **`--connection-string-setting`** | 要使用的包含存储连接字符串的应用设置的名称。 |
| **`--created-after`** | （可选）获取在此日期/时间 (UTC) 之后创建的实例。 接受所有 ISO 8601 格式的日期/时间。 |
| **`--created-before`** | （可选）获取在特定日期/时间 (UTC) 之前创建的实例。 接受所有 ISO 8601 格式的日期/时间。 |
| **`--runtime-status`** | （可选）获取状态与特定状态相符的实例，包括`running`、`completed` 和 `failed`。 可以提供一个或多个以空格分隔的状态。 |
| **`--top`** | （可选）限制给定请求中返回的记录数。 |
| **`--task-hub-name`** | 要使用的 Durable Functions 任务中心的名称（可选）。 |

若要了解详细信息，请参阅 [Durable Functions 文档](./durable/durable-functions-instance-management.md#azure-functions-core-tools-2)。

## <a name="func-durable-get-runtime-status"></a>func durable get-runtime-status  

返回指定业务流程实例的状态。

```command
func durable get-runtime-status --id <INSTANCE_ID>
```

`get-runtime-status` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 要使用的包含存储连接字符串的设置名称（可选）。 |
| **`--id`** | 指定业务流程实例的 ID（必选）。 |
| **`--show-input`** | 设置后，响应中会包含此函数的输入。 |
| **`--show-output`** | 设置后，响应中会包含执行历史记录。 |
| **`--task-hub-name`** | 要使用的 Durable Functions 任务中心的名称（可选）。 |

若要了解详细信息，请参阅 [Durable Functions 文档](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1)。

## <a name="func-durable-purge-history"></a>func durable purge-history

清除时间超过指定阈值的业务流程的实例状态、历史记录和 Blob 存储。

```command
func durable purge-history
```

`purge-history` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 要使用的包含存储连接字符串的设置名称（可选）。 |
| **`--created-after`** | （可选）清除在此日期/时间 (UTC) 之后创建之实例的历史记录。 接受所有 ISO 8601 格式的日期/时间值。 |
| **`--created-before`** | （可选）清除在此日期/时间 (UTC) 之前创建之实例的历史记录。 接受所有 ISO 8601 格式的日期/时间值。|
| **`--runtime-status`** | （可选）删除状态与特定状态相符之实例的历史记录，包括 `completd`、`terminated`、`canceled` 和 `failed`。 可以提供一个或多个以空格分隔的状态。 如果未包含 `--runtime-status`，则无论状态如何，都会删除实例的历史记录。|
| **`--task-hub-name`** | 要使用的 Durable Functions 任务中心的名称（可选）。 |

若要了解详细信息，请参阅 [Durable Functions 文档](./durable/durable-functions-instance-management.md#azure-functions-core-tools-7)。

## <a name="func-durable-raise-event"></a>func durable raise-event         

向指定的业务流程实例引发事件。

```command
func durable raise-event --event-name <EVENT_NAME> --event-data <DATA>
```

`raise-event` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 要使用的包含存储连接字符串的设置名称（可选）。 |
| **`--event-data`** | 要传递到事件的数据（内联或来自 JSON 文件）（必选）。 对于文件，请使用 `@` 符号作为文件路径的前缀，例如 `@path/to/file.json`。 |
| **`--event-name`** | 要引发的事件的名称（必选）。
| **`--id`** | 指定业务流程实例的 ID（必选）。 |
| **`--task-hub-name`** | 要使用的 Durable Functions 任务中心的名称（可选）。 |

若要了解详细信息，请参阅 [Durable Functions 文档](./durable/durable-functions-instance-management.md#azure-functions-core-tools-5)。

## <a name="func-durable-rewind"></a>func Durable rewind              

后退指定的业务流程实例。

```command
func durable rewind --id <INSTANCE_ID> --reason <REASON>
```

`rewind` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 要使用的包含存储连接字符串的设置名称（可选）。 |
| **`--id`** | 指定业务流程实例的 ID（必选）。 |
| **`--reason`** | 后退业务流程实例的原因（必选）。|
| **`--task-hub-name`** | 要使用的 Durable Functions 任务中心的名称（可选）。 |

若要了解详细信息，请参阅 [Durable Functions 文档](./durable/durable-functions-instance-management.md#azure-functions-core-tools-6)。

## <a name="func-durable-start-new"></a>func durable start-new

开始执行指定的业务流程协调程序函数的新实例。

```command
func durable start-new --id <INSTANCE_ID> --function-name <FUNCTION_NAME> --input <INPUT>
```

`start-new` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 要使用的包含存储连接字符串的设置名称（可选）。 |
| **`--function-name`** | 要开始执行的业务流程协调程序函数的名称（必选）。|
| **`--id`** | 指定业务流程实例的 ID（必选）。 |
| **`--input`** | 以内联方式或从 JSON 文件提供的业务流程协调程序函数的输入。 对于文件，请使用 `@` 符号作为文件路径的前缀，例如 `@path/to/file.json`。 |
| **`--task-hub-name`** | 要使用的 Durable Functions 任务中心的名称（可选）。 |

若要了解详细信息，请参阅 [Durable Functions 文档](./durable/durable-functions-instance-management.md#azure-functions-core-tools)。

## <a name="func-durable-terminate"></a>func durable terminate

终止指定的业务流程实例。

```command
func durable terminate --id <INSTANCE_ID> --reason <REASON>
```

`terminate` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 要使用的包含存储连接字符串的设置名称（可选）。 |
| **`--id`** | 指定业务流程实例的 ID（必选）。 |
| **`--reason`** | 终止业务流程的原因（必选）。 |
| **`--task-hub-name`** | 要使用的 Durable Functions 任务中心的名称（可选）。 |

若要了解详细信息，请参阅 [Durable Functions 文档](./durable/durable-functions-instance-management.md#azure-functions-core-tools-4)。

## <a name="func-extensions-install"></a>func extensions install

在非 C# 类库项目中安装 Functions 扩展。 

如果可能，应改为使用扩展捆绑包。 若要了解详细信息，请参阅[扩展捆绑包](functions-bindings-register.md#extension-bundles)。

对于 C# 类库和 .NET 独立项目，请改用标准 NuGet 包安装方法，例如 `dotnet add package`。

`install` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | 包含扩展名为 .csproj 的文件的目录路径。|
| **`--csx`** |   支持 C# 脚本 (.csx) 项目。 |
| **`--force`** |  更新现有扩展的版本。 |
| **`--output`** |  扩展的输出路径。 |
| **`--package`** |   特定扩展包的标识符。 如果未指定，则会安装所有引用的扩展，与使用 `func extensions sync` 相同。|
| **`--source`** |  未使用 NuGet org 时的 NuGet 信息提要源。|
| **`--version`** |  扩展包的版本。 |

如果在 host.json 文件中定义了扩展捆绑包，则不执行任何操作。

## <a name="func-extensions-sync"></a>func extensions sync

安装添加到函数应用的所有扩展。

`sync` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | 包含扩展名为 .csproj 的文件的目录路径。|
| **`--csx`** |   支持 C# 脚本 (.csx) 项目。 |
| **`--output`** |  扩展的输出路径。 |

重新生成缺少的扩展名为 .csproj 的文件。 如果在 host.json 文件中定义了扩展捆绑包，则不执行任何操作。

## <a name="func-kubernetes-deploy"></a>func kubernetes deploy

将 Functions 项目作为自定义 docker 容器部署到使用 KEDA 的 Kubernetes 群集。

```command
func kubernetes deploy 
```

此命令会将项目生成为自定义容器，然后发布到使用 KEDA 进行动态缩放的 Kubernetes 群集。 若要发布到使用默认缩放器或 KNative 的群集，请改用 [ `func deploy`命令](#func-deploy)。 自定义容器必须有一个 Dockerfile。 若要使用 Dockerfile 创建应用，请使用 `--dockerfile` 选项和 [`func init`命令](#func-init)。 

可使用以下 Kubernetes 配置选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | （可选）显示部署模板，但不执行。 |
| **`--config-map-name`** | （可选）部署中要使用的包含[函数应用设置](functions-how-to-use-azure-function-app-settings.md#settings)的现有配置映射的名称。 需要 `--use-config-map`。 默认行为是基于 [local.settings.json 文件]中的 `Values` 对象创建设置。|
| **`--cooldown-period`** | 在所有触发器不再处于活动状态后以及部署回缩到零（默认值为 300 秒）之前的冷却期（以秒为单位）。 |
| **`--ignore-errors`** | 在资源返回错误后继续进行部署。 默认行为是出错时停止。 |
| **`--image-name`** | 用于 Pod 部署以及从中读取函数的图像的名称。 |
| **`--keda-version`** | 设置要安装的 KEDA 的版本。 有效选项为 `v1` 和 `v2`（默认）。 |
| **`--keys-secret-name`** | 用于存储[函数访问密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)的 Kubernetes 机密集合的名称。 |
| **`--max-replicas`** | 设置 Horizontal Pod Autoscaler (HPA) 可缩放到的最大副本计数。 |
| **`--min-replicas`** | 设置最小副本计数，低于此数值时，HPA 不会执行缩放。 |
| **`--mount-funckeys-as-containervolume`** | 将[函数访问密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)装载为容器量。 |
| **`--name`** | Kubernetes 中的部署和其他项目使用的名称。 |
| **`--namespace`** | 设置要部署到的 Kubernetes 命名空间，默认部署到默认命名空间。 |
| **`--no-docker`** | 从当前目录而不是从映像读取函数。 需要装载映像文件系统。 |
| **`--registry`** | 设置后，系统将运行 Docker 版本并将映像推送到该名称的注册表。 `--registry` 与 `--image-name` 不能同时使用。 对于 Docker，请使用你的用户名。 |
| **`--polling-interval`** | 用于检查非 HTTP 触发器的轮询间隔（以秒为单位），默认值为 30 秒。 |
| **`--pull-secret`** | 用于访问专用注册表凭据的机密。 |
| **`--secret-name`** | 现有 Kubernetes 机密集合的名称，其中包含要在部署中使用的[函数应用设置](functions-how-to-use-azure-function-app-settings.md#settings)。 默认行为是基于 [local.settings.json 文件]中的 `Values` 对象创建设置。 |
| **`--show-service-fqdn`** | 显示具有 Kubernetes FQDN 的 HTTP 触发器的 URL，而不是使用 IP 地址的默认行为。 |
| **`--service-type`** | 设置 Kubernetes 服务的类型。 支持的值为：`ClusterIP`、`NodePort` 和 `LoadBalancer`（默认）。 |
| **`--use-config-map`** | 使用 `ConfigMap` 对象 (v1) 而不是 `Secret` 对象 (v1) 来配置[函数应用设置](functions-how-to-use-azure-function-app-settings.md#settings)。 使用 `--config-map-name` 设置映射名称。|

Core Tools 将使用本地 Docker CLI 来生成并发布映像。 

请确保已在本地安装 Docker。 运行 `docker login` 命令以连接到帐户。

有关详细信息，请参阅[将函数应用部署到 Kubernetes](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes)。

## <a name="func-kubernetes-install"></a>func kubernetes install

在 Kubernetes 群集中安装 KEDA。

```command
func kubernetes install 
```

将 KEDA 安装到 kubectl 配置文件中定义的群集。

`install` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | 显示部署模板，但不执行。 |
| **`--keda-version`** | 设置要安装的 KEDA 的版本。 有效选项为 `v1` 和 `v2`（默认）。 |
| **`--namespace`** | 支持安装到特定的 Kubernetes 命名空间。 如果不设置，则使用默认命名空间。 |

若要了解详细信息，请参阅[在 Kubernetes 中管理 KEDA 和函数](functions-kubernetes-keda.md#managing-keda-and-functions-in-kubernetes)。

## <a name="func-kubernetes-remove"></a>func kubernetes remove

从 kubectl 配置文件中定义的 Kubernetes 群集中删除 KEDA。

```command
func kubernetes remove 
```

从 kubectl 配置文件中定义的群集中删除 KEDA。

`remove` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--namespace`** | 支持从特定的 Kubernetes 命名空间中卸载。 如果不设置，则使用默认命名空间。 | 

若要了解详细信息，请参阅[卸载 Kubernetes 中的 KEDA](functions-kubernetes-keda.md#uninstalling-keda-from-kubernetes)。

## <a name="func-settings-add"></a>func settings add

将新设置添加到 [local.settings.json 文件]中的 `Values` 集合。

```command
func settings add <SETTING_NAME> <VALUE>
```

将 `<SETTING_NAME>` 替换为应用设置的名称，将 `<VALUE>` 替换为设置的值。 

`add` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | 将名称/值对添加到 `ConnectionStrings` 集合而不是 `Values` 集合。 仅在某些框架要求时才使用 `ConnectionStrings` 集合。 若要了解详细信息，请参阅 [local.settings.json 文件]。 |

## <a name="func-settings-decrypt"></a>func settings decrypt

解密 [local.settings.json 文件]的 `Values` 集合中以前加密的值。

```command
func settings decrypt
```

同时还会解密 `ConnectionStrings` 集合中的连接字符串值。 在 local.settings.json 中，`IsEncrypted` 也设置为 `false`。 对本地设置加密可以减少泄露 local.settings.json 中重要信息的风险。 在 Azure 中，应用程序设置始终以加密的形式进行存储。 

## <a name="func-settings-delete"></a>func settings delete

从 [local.settings.json 文件]中的 `Values` 集合删除现有设置。

```command
func settings delete <SETTING_NAME>
```

将 `<SETTING_NAME>` 替换为应用设置的名称，将 `<VALUE>` 替换为设置的值。 

`delete` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | 从 `ConnectionStrings` 集合中移除名称/值对，而不是从 `Values` 集合中移除。 |

## <a name="func-settings-encrypt"></a>func settings encrypt

对 [local.settings.json 文件]的 `Values` 集合中单个项的值进行加密。

```command
func settings encrypt
```

同时也对 `ConnectionStrings` 集合中的连接字符串值加密。 在 local.settings.json 文件中，`IsEncrypted` 也设置为 `true`，表明本地运行时会在使用前先对设置进行解密。 对本地设置加密可以减少泄露 local.settings.json 中重要信息的风险。 在 Azure 中，应用程序设置始终以加密的形式进行存储。 

## <a name="func-settings-list"></a>func settings list

输出 [local.settings.json 文件]的 `Values` 集合中的设置列表。 

```command
func settings list
```

同时也会输出 `ConnectionStrings` 集合中的连接字符串。 出于安全，系统在默认情况下会对值进行掩码。 你可以使用 `--showValue` 选项来显示实际值。

`list` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--showValue`** | 显示输出中的实际未掩码值。 |

## <a name="func-templates-list"></a>func templates list

列出可用的函数（触发器）模板。

`list` 操作支持以下选项：

| 选项     | 说明                            |
| ------------ | -------------------------------------- |
| **`--language`** | 筛选返回模板时针对的语言。 默认值为返回所有语言。 |

[local.settings.json 文件]: functions-develop-local.md#local-settings-file
